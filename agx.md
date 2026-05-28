# The AgX Image Formation Pipeline Reference

This document outlines the technical steps and mathematical logic required to build a filmic, "AgX-style" color pipeline, specifically for use in sandboxes like the Blender Compositor or custom shaders.

---

## Stage 1: The Linear Input
Before any math occurs, ensure your image is in **Scene Linear** (no gamma or display curves applied). AgX is typically tuned for wide-gamut primaries (like Rec.2020 or ACEScg).

### Step 1: The Inset Matrix (Technical Desaturation)
To prevent digital primary colors (R, G, B) from "clipping" and causing hue shifts (like bright reds turning yellow), we apply a **3x3 Inset Matrix**.

**The Logic:** "Steal" a small percentage of Green and Blue to add to Red (and vice versa). This pulls the colors toward the neutral axis.

**Example 3x3 Inset Matrix (Rec.709):**

[ 0.85, 0.10, 0.05 ]  <-- New Red
[ 0.05, 0.90, 0.05 ]  <-- New Green
[ 0.05, 0.05, 0.90 ]  <-- New Blue

* **Tip:** Ensure every row adds up to exactly **1.0** to keep the exposure constant.
* **Magnitude:** Increasing the off-diagonal numbers (the 0.10, 0.05) increases the "Inset" (desaturation).

---

## Stage 2: The Logarithmic Encoding
The Log stage "squeezes" high dynamic range light into a 0–1 digital signal.

### Step 2: The "Screws" (Slope and Intercept)
Calculate your **Slope (m)** and **Intercept (b)** based on your desired range in stops.
* **Range:** -10 stops (Black) to +6.5 stops (White) relative to 0.18.
* **Low** = $\log_2(0.18 \cdot 2^{-10})$
* **High** = $\log_2(0.18 \cdot 2^{6.5})$

**Formula for Screws:**
1.  $m = \frac{1}{\text{High} - \text{Low}}$
2.  $b = - \text{Low} \cdot m$

### Step 3: The Encoding Formula
Apply the log math to your inset linear image ($L_{in}$):
$$V_{log} = \log_2(L_{in}) \cdot m + b$$

* **Tip:** In Blender/Python, add a tiny offset (like 0.000001) to $L_{in}$ to avoid "Log of Zero" errors.

---

## Stage 3: Image Formation (The Sigmoid)
Once in Log, the image looks flat. We now apply the "Look" using an S-curve (Sigmoid).

### Step 4: The S-Curve (Formation)
A sigmoid creates the "Toe" (shadows) and the "Shoulder" (highlight roll-off).
* **Pivot Point:** Place a point in your curve at **(0.45, 0.45)**. This represents Middle Gray.
* **The Rotation:** Rotate the S-curve around this pivot. Increasing the "steepness" increases contrast.
* **The Shoulder:** Ensure the curve becomes horizontal as it reaches $y=1.0$. This prevents harsh digital clipping.



### Step 5: Perceptual Desaturation
Mimic film by fading colors to white as they get bright.
1.  **Mask:** Create a mask based on the brightness (Luminance) of the post-sigmoid image.
2.  **Logic:** As brightness goes from 0.8 to 1.0, reduce Saturation from 1.0 to 0.0.
3.  **Result:** Bright highlights "glow" white instead of looking "neon."

---

## Stage 4: Output Transform
The final step is the "Handshake" with your monitor.

### Step 6: Target Gamma
If you are targeting a standard monitor, apply your **Gamma 2.2** or **Rec.709** transfer function.
* **Warning:** If you built your pipeline correctly in nodes, set your global display transform to **Raw** or **Linear** so you don't double-process the image.

---

## Troubleshooting & Tips
* **The "Chase":** If moving the S-curve makes the image too dark/bright, your pivot is off. Ensure Middle Gray stays pinned at 0.45.
* **Banding:** If you see stripes in the shadows, your $a$ (Dynamic Range) is too high for your bit-depth. Use 10-bit or 16-bit float for AgX workflows.
* **Yellow Skew:** If bright reds look too yellow, check your Inset Matrix. You may be "stealing" too much Green relative to Blue.

Final Logic Check for your Blender Setup:
Linear In -> Matrix (Inset) -> Log Math (Squeeze).

Color Balance (Exposure Adjust).

RGB Curves (The S-Curve "Look").

Desaturation (The Highlight "Glow").

Gamma 2.2 (The Display).

Does this structure give you enough clarity to start building the node tree in the Compositor?

# AgX Post-Formation: Mathematical Purity Attenuation

This stage occurs **after** the Sigmoid contrast but **before** the final Display Gamma. Its goal is to handle "Perceptual White" by draining saturation out of the highlights using pure math instead of visual masks.

---

## 1. The Logic
We utilize the **YCbCr** color space to isolate Luminance ($Y$) from Chrominance ($Cb, Cr$). We then calculate a "Factor" ($F$) that acts as a multiplier for the color channels. 

As $Y$ approaches 1.0 (pure white), $F$ must approach 0.0 (zero saturation).

---

## 2. The Power-Inverse Formula
The most filmic way to calculate the desaturation factor is the **Power-Inverse** curve. It allows the color to stay "strong" through the midtones and then "crash" into white at the very top of the exposure.

### The Formula:
$$F = \max(0, 1 - Y^n)$$

* **$Y$**: The Luminance of the pixel (0 to 1).
* **$n$**: The "Concentration Screw." This determines how "late" the desaturation begins.

### The "Screw" ($n$) Settings:
| Value | Result | Use Case |
| :--- | :--- | :--- |
| **$n = 2$** | Rapid, linear fade | Vintage, low-saturation "faded" look. |
| **$n = 6$** | The "AgX Sweet Spot" | Natural highlight roll-off; preserves skin tones. |
| **$n = 12$** | Extreme late drop | Vibrant, modern digital look; neon stays colorful. |

---

## 3. Blender Compositor Implementation

To recreate this mathematically in Blender nodes, follow this signal flow:

### Step A: Isolate the Chroma
1.  **Convert Color Space Node:** Set From `RGB` to `YCbCr`.
2.  **Separate Color Node:** Set to `YCbCr`. You now have three channels: $Y$ (Luma), $Cb$ (Blue-Chroma), and $Cr$ (Red-Chroma).

### Step B: The Math "Screw"
1.  **Math Node (Power):** * Base: $Y$ channel.
    * Exponent: $n$ (create a Value node for this, start at **6.0**).
2.  **Math Node (Subtract):** * Subtract the result of the Power node from **1.0**.
3.  **Math Node (Maximum):** * Compare the result with **0.0** (this prevents negative saturation). This is your final Factor **$F$**.

### Step C: Apply and Re-Combine
1.  **Math Node (Multiply):** Multiply $Cb$ by $F$.
2.  **Math Node (Multiply):** Multiply $Cr$ by $F$.
3.  **Combine Color Node:** Use the original $Y$, and the new $Cb$ and $Cr$.
4.  **Convert Color Space Node:** Set From `YCbCr` to `RGB`.

---

## 4. The "Threshold" Alternative
If you prefer a sharp cutoff (no desaturation until a specific point), use this formula instead:

$$F = \text{clamp}\left( \frac{1 - Y}{1 - \text{Threshold}}, 0, 1 \right)$$

* **Threshold (e.g., 0.8):** Color remains 100% pure until $Y$ hits 0.8, then fades to 0 at $Y=1.0$.

---

## Tips for the Workflow
* **Order of Operations:** Ensure this happens **after** your RGB Curves (Sigmoid). If you do it before, the Sigmoid will warp the math and make the desaturation look "dirty."
* **Neutral Axis:** Because YCbCr separates hue from brightness, this method will **never shift your hue** (e.g., your bright reds won't turn pink or orange; they will only turn a lighter, desaturated red).
* **Bit Depth:** This math is precise. Ensure your Blender project is set to **32-bit Float** (OpenEXR) to avoid "stepping" or "banding" in the highlights.


# 🗺️ Mastery Roadmap: From Log Basics to AgX Architecture

This roadmap is designed to build your intuition in layers. Do not move to the next phase until you can explain "why" the math in the current phase works.

---

## 🟢 Phase 1: The Mathematician (Desmos)
*Goal: Understand the "Bones" of the signal and normalization.*

1.  **Dynamic Range Normalization**
    * Build a slider for $M$ (Max Linear Light).
    * Create the function $f(x) = x/M$.
    * **Task:** Ensure any input $x$ is perfectly "clamped" between 0 and 1.
2.  **The "Indestructible" Log Formula**
    * Implement: $y = \frac{\log_2(a \cdot x + b) - \log_2(b)}{\log_2(a + b) - \log_2(b)}$
    * **Task:** Observe how increasing $a$ "pushes" the curve up, creating more room for highlights.
3.  **The Anchor Point (Regression)**
    * Use Desmos regression (`~`) to find the power $g$ that forces your curve through $(0.18, 0.45)$.
    * **Task:** Change $a$ and watch $g$ automatically update to keep Middle Gray "pinned."

---

## 🟡 Phase 2: The Architect (Blender Compositor)
*Goal: Build the "Pipeline" and visualize signal flow.*

1.  **The Technical Inset**
    * Build a **Color Matrix** node group. 
    * Use the "Safe" coefficients (e.g., 0.85 diagonal).
    * **Task:** Toggle the node to see how it "shrinks" the saturation before the math hits.
2.  **The Log Encoding Group**
    * Translate your Desmos formula into a chain of **Math Nodes**.
    * **Task:** Feed a linear image in; the output should look flat, grey, and "washed out" on the Waveform.
3.  **The Sigmoid Formation**
    * Use an **RGB Curves** node to create an S-Curve.
    * **Task:** Pin the middle at 0.45. Sculpt the "Shoulder" until bright lights look "creamy" instead of "clipped."

---

## 🟠 Phase 3: The Color Scientist (Matrices & Hues)
*Goal: Control how color "travels" and "behaves."*

1.  **The Purity Knob (Inverse Matrix)**
    * Calculate the Inverse of your Inset Matrix.
    * Use a **Mix Node** to blend between the Inset path and the Inverse path.
    * **Task:** Find the "Sweet Spot" (usually 0.7) where color is vibrant but highlights stay safe.
2.  **Mathematical Desaturation**
    * Implement the $1 - Y^n$ formula using a **YCbCr** node chain.
    * **Task:** Adjust $n$ (the screw) until only the brightest 10% of the image turns white.
3.  **The Hue Skew Test**
    * Tweak your Inset Matrix coefficients to "skew" Red toward Orange.
    * **Task:** Observe how this creates a more "organic" look for fire or skin tones.

---

## 🔴 Phase 4: The Developer (Advanced Implementation)
*Goal: Professional-level verification and "The Undo."*

1.  **Color Space Bridges**
    * Research the 3x3 matrix for `Rec.709 to Rec.2020`.
    * **Task:** Implement it as a node and observe how the primary colors shift on the Vectorscope.
2.  **The "Inversion" Challenge**
    * **Task:** Build a second node tree that perfectly "undoes" your AgX pipe. If you feed your finished image back through the "Inverse Math," you should get your original Linear Image back.
3.  **OCIO Integration**
    * Study the **AgX OCIO config** files by Troy James Sobotka.
    * **Task:** Identify the "Slope" and "Intercept" values used in the professional version and compare them to yours.

---

## 💡 Pro-Tips for Success
* **Scopes are Truth:** Always keep the **Waveform** and **Vectorscope** open. They show you the math that your eyes might miss.
* **32-Bit Float:** Always set your project to 32-bit (OpenEXR) in Blender. 8-bit math will break and "band" immediately under these transforms.
* **Stay Linear:** Never apply a Gamma 2.2 curve until the very last node.
