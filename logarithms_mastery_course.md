# 🎬 The Logarithms & Color Science Mastery Course
*A mathematical and practical guide from basic exponents to recreating the AgX tone mapper and custom camera log curves.*

---

## Course Overview
In digital imaging, cinematography, and rendering, we face a fundamental conflict: **digital sensors measure light linearly, but the human eye perceives it logarithmically.** 

This course is designed to bridge the gap between pure high-school algebra and the cutting-edge color science used in Hollywood films, game engines (like Unreal Engine 5), and 3D packages (like Blender 4.x). 

By the end of this course, you will understand:
1. The mathematical foundation of logarithms and how to manipulate them.
2. Why video requires logarithmic storage and how exposure "stops" map to logarithmic bases.
3. The exact mathematical architecture of professional camera log curves (S-Log3, LogC3, Apple Log).
4. How to recreate the industry-standard **AgX color transform** pipeline from raw math.
5. How to write your own custom log curves and compile them into 1D/3D Look-Up Tables (`.cube` files) using Python.

---

## 🟢 Module 1: The Mathematical Birth of Logarithms

### 1.1 The Core Intuition: Inverting the Exponent
At its simplest, a logarithm is the **inverse operation of exponentiation**. While an exponent asks, *"What do I get when I raise this base to this power?"*, a logarithm asks, **"To what power must I raise this base to get this number?"**

$$\text{If } b^y = x, \quad \text{then } \log_b(x) = y$$

- Here, $b$ is the **base**, $y$ is the **exponent** (or logarithm), and $x$ is the **argument**.
- **Crucial Rule:** The argument $x$ must be strictly greater than $0$ ($x > 0$). You cannot take the logarithm of zero or a negative number in real numbers. As $x$ approaches $0$, $\log_b(x)$ drops towards negative infinity ($-\infty$).

### 1.2 Common Bases in Color Science
While mathematically you can use any positive base, color science and physics rely almost exclusively on three bases:

| Base | Mathematical Name | Symbol | Primary Use Case in Color Science |
| :--- | :--- | :--- | :--- |
| **Base 2** | Binary Logarithm | $\log_2(x)$ | **Exposure Stops.** Doubling the light = $+1$ Stop. |
| **Base 10** | Common Logarithm | $\log_{10}(x)$ or $\log(x)$ | **Sensitometry.** Decibels, optical density, and camera log standards (Cineon, S-Log). |
| **Base $e$** | Natural Logarithm | $\ln(x)$ | **Physical systems.** Sensor heat dissipation, light decay in volume scattering. |

### 1.3 The 3 Golden Rules of Log Manipulation
To write, modify, or reverse-engineer log curves, you must master the three algebraic laws that allow us to simplify complex functions:

#### 1. The Product Rule (Multiplication to Addition)
The logarithm of a product is the sum of the logarithms of its factors.
$$\log_b(A \cdot B) = \log_b(A) + \log_b(B)$$
*Cinematography Intuition:* Adding an ND filter that cuts light in half is additive in stops, even though it is multiplicative in raw light.

#### 2. The Quotient Rule (Division to Subtraction)
The logarithm of a quotient is the difference of the logarithms of its numerator and denominator.
$$\log_b\left(\frac{A}{B}\right) = \log_b(A) - \log_b(B)$$

#### 3. The Power Rule (Exponents to Multipliers)
The logarithm of a number raised to a power is that power multiplied by the logarithm of the number.
$$\log_b(A^p) = p \cdot \log_b(A)$$
*Color Science Intuition:* Applying a classic "gamma" curve ($y = x^\gamma$) to a log-encoded image becomes a simple linear slope multiplier ($y = \gamma \cdot \log(x)$). This is why grading in log space feels so smooth and uniform!

---

### 🛠️ Interactive Desmos Drill 1: The Log Sandbox
Open [Desmos.com/calculator](https://www.desmos.com/calculator) and input the following equations to understand how these properties shape a curve:

1. Plot the basic binary log:
   `y = log_2(x)`
2. Add a horizontal offset (pedestal) and multiplier:
   `y = c * log_2(a * x + b)`
3. Create sliders for `a`, `b`, and `c` and observe:
   - What happens when `b` is negative? (The curve shifts right, and the undefined region moves).
   - What happens to the slope as `a` increases? (The curve compresses horizontally).
   - How does `c` scale the output? (It stretches the curve vertically).

---

## 🟡 Module 2: Perceptual Reality vs. Sensor Reality

### 2.1 The Sensor's View: Linear Light
Digital camera sensors are linear devices. They count photons. If a patch of a scene reflects 100 photons, it generates twice as much electrical charge as a patch reflecting 50 photons. 
This is **Scene-Linear Light**.

$$I_{sensor} \propto \text{Number of Photons}$$

If you graph linear light, a doubling of exposure from 1% to 2% uses the exact same numerical space as a doubling from 50% to 100%.

### 2.2 The Human View: Logarithmic Perception
Human vision does not operate linearly. We perceive light proportionally, a phenomenon described by the **Weber-Fechner Law**: *the perceived change in a stimulus is proportional to the initial stimulus intensity.*

$$\Delta S = k \cdot \frac{\Delta I}{I}$$

To our eyes, the jump from 1 stop to 2 stops of light (doubling intensity) feels identical to the jump from 10 stops to 11 stops (also doubling intensity). Thus, our sight is naturally logarithmic.

```
Linear Light (Photons):  [1] [2] [4] [8] [16] [32] [64] [128] [256] -> Exponential steps
Human Perception (Stops): [0] [1] [2] [3]  [4]  [5]  [6]  [7]   [8]   -> Linear, equal steps
```

### 2.3 The Dynamic Range Bottleneck
If we store scene-linear light directly in a standard digital container (like an 8-bit or 10-bit file):
- **Highlights get all the bits:** An 8-bit file has 256 possible integer values (0 to 255). The brightest stop of light (e.g., from 50% to 100% brightness) takes up **128 of those values** (half the file's capacity!).
- **Shadows get starved:** The bottom 6 stops of shadow detail must squeeze into just **4 or 5 values**, causing horrendous, blocky banding.

**Logarithmic Encoding** solves this by squeezing the highlights and stretching the shadows. It distributes the available bit depth evenly across the entire dynamic range of exposure stops, ensuring each stop of light receives an equal share of digital data.

---

### 🛠️ Interactive Desmos Drill 2: The Bit-Distribution Visualizer
In Desmos, visualize how linear vs. log storage allocates details:

1. Plot the linear ramp:
   `y_1 = x` (for $x \in [0, 1]$)
2. Plot the log curve:
   `y_2 = log_2(127 * x + 1) / 7` (maps $[0, 1]$ to $[0, 1]$)
3. Look at the interval $x \in [0, 0.05]$ (the deepest shadows, representing 5% of the light):
   - On the linear line `y_1`, this occupies only $0.05$ (5%) of your vertical code space.
   - On the log curve `y_2`, this occupies $\approx 0.41$ (41%) of your vertical code space!
   - This visualizes exactly how log encoding "steals" space from highlights and gives it to shadows.

---

## 🟠 Module 3: Anatomy of Camera Log Curves

### 3.1 The Standard Camera Log Formula
Most camera manufacturers (Sony, ARRI, RED, Apple) base their log formats on the classic **Cineon** specification. The mathematical template for converting scene-linear light ($x$) to normalized log data ($y$) is:

$$y = c \cdot \log_{10}(a \cdot x + b) + d$$

Let's dissect the engineering purpose of each constant:
* **$x$:** The scene-referred linear reflection value (where $0.18$ represents 18% Middle Gray, and $1.0$ is diffuse white).
* **$a$ (Gain/Sensitivity):** Scales the linear input. A larger $a$ compresses a wider dynamic range of stops into the curve.
* **$b$ (Linear Offset):** Prevents the logarithm from attempting to evaluate $\log_{10}(0) = -\infty$. It acts as a safety cushion for total darkness.
* **$c$ (Contrast/Scale):** Stretches the output logarithm to fit the desired container range (usually 0.0 to 1.0).
* **$d$ (Output Pedestal):** Shifts the entire curve vertically so that "absolute black" ($x=0$) lands exactly on the camera's desired minimum digital code value.

### 3.2 Professional Case Study: Sony S-Log3
Camera manufacturers often modify the pure log equation at the very bottom of the range. Because a pure log curve becomes extremely steep near zero, camera sensors can suffer from severe noise amplification in deep blacks. 

To solve this, professional curves like **Sony S-Log3** transition to a **perfectly linear equation** below a specific threshold (the "pivot point"):

$$\text{For } x \ge 0.01125077: \quad y = 0.4375 \cdot \log_{10}(x \cdot 0.9890507 + 0.015467) + 0.596206$$

$$\text{For } x < 0.01125077: \quad y = 5.625 \cdot x + 0.0922822$$

Notice how smoothly the linear slope ($5.625$) matches the slope of the logarithmic curve at the pivot point!

### 3.3 The Core Anchor: 18% Middle Gray
For a camera log curve to be useful, it must anchor **Middle Gray ($x=0.18$)** to a consistent target output.
- In **Sony S-Log3**, Middle Gray $0.18$ maps to exactly **$0.41$** (or 41% on a waveform monitor).
- In **Arri LogC3**, Middle Gray $0.18$ maps to exactly **$0.39$**.
- In **Apple Log**, Middle Gray $0.18$ maps to exactly **$0.45$**.

### 3.4 Un-Logging: The Mathematical Inverse
To edit, grade, or display log footage on a standard screen, we must "un-log" it (convert it back to scene-linear light). We do this by deriving the algebraic inverse of the formula:

$$y = c \cdot \log_{10}(a \cdot x + b) + d$$

Subtract $d$ from both sides:
$$y - d = c \cdot \log_{10}(a \cdot x + b)$$

Divide by $c$:
$$\frac{y - d}{c} = \log_{10}(a \cdot x + b)$$

Raise both sides as powers of 10 (applying the inverse of $\log_{10}$):
$$10^{\left(\frac{y - d}{c}\right)} = a \cdot x + b$$

Subtract $b$:
$$10^{\left(\frac{y - d}{c}\right)} - b = a \cdot x$$

Divide by $a$ to isolate $x$:
$$x = \frac{10^{\left(\frac{y - d}{c}\right)} - b}{a}$$

This exponential equation is exactly what a **Color Space Conversion LUT** or shader runs under the hood to restore high contrast and linear light behavior to flat log footage!

---

### 🛠️ Interactive Desmos Drill 3: Solving for Constants
In Desmos, we will use a **Regression** to calculate custom constants. Let's design a log curve where:
- Absolute darkness ($x=0$) must output exactly **$0.09$** (black pedestal).
- Middle Gray ($x=0.18$) must output exactly **$0.45$**.
- Dynamic Range scale $a$ is set to $10.0$ and safety cushion $b$ is $0.01$.

1. Open Desmos and write your known points:
   `x_1 = 0`, `y_1 = 0.09`
   `x_2 = 0.18`, `y_2 = 0.45`
2. Define the parameters:
   `a = 10`
   `b = 0.01`
3. Enter the regression lines to solve for scale `c` and pedestal `d`:
   `y_1 ~ c * log10(a * x_1 + b) + d`
   `y_2 ~ c * log10(a * x_2 + b) + d`
4. Desmos instantly solves the system and shows:
   `c ≈ 0.16279`
   `d ≈ 0.41558`
5. You now have a mathematically sound, anchored custom log curve:
   $$y = 0.16279 \cdot \log_{10}(10x + 0.01) + 0.41558$$

---

## 🔵 Module 4: Recreating AgX Log and the AgX Tone Mapper

### 4.1 The Philosophy of AgX
Older color systems like ACES or Blender's former "Filmic" engine suffered from the **Abrupt Highlight Clipping Problem** (also known as the "red rush" or "blue skew"). In these systems, as a color channel gets extremely bright, it hits the maximum container value ($1.0$) and flatlines, while other channels continue rising. This causes highly saturated highlights to shift hue (e.g., a brilliant fire-orange shifts to yellow, or saturated blue skies shift to magenta).

**AgX** (popularized in Blender 4.0+) mimics physical film by implementing:
1. **Chroma Inset:** Squeezing the saturated primaries inward toward the grayscale diagonal *before* any exposure compression happens. This ensures highlights naturally desaturate (blend into white) as they overexpose, rather than shifting hues.
2. **Normalized Log Space:** Converting linear light into a Log2 stop-based representation.
3. **filmic Contrast Curve:** Using a specialized 6th-order polynomial sigmoid curve to create a smooth shoulder (highlight rolloff) and toe (shadow contrast).

---

### 4.2 The 6-Step AgX Pipeline
Here is the exact mathematical sequence required to apply the sRGB AgX display transform to scene-linear Rec.709 pixels:

#### Step 1: Primaries Inset
We multiply the linear RGB pixel vector $[R, G, B]^T$ by the 3x3 `AgXInsetMatrix`. This pushes the color primaries inward to create a safety margin for saturation:

$$\begin{bmatrix} R_{inset} \\ G_{inset} \\ B_{inset} \end{bmatrix} = \begin{bmatrix}
0.85662715 & 0.13731897 & 0.11189821 \\
0.09512124 & 0.76124199 & 0.07679942 \\
0.04825161 & 0.10143904 & 0.81130237
\end{bmatrix} \begin{bmatrix} R_{linear} \\ G_{linear} \\ B_{linear} \end{bmatrix}$$

#### Step 2: Clamp & Log2 Conversion
To prevent mathematical errors, we clamp values to a very small positive floor ($10^{-10}$). Then, we convert the values to base-2 logarithmic stops:

$$EV = \log_2( \max(\text{Color}, 10^{-10}) )$$

We then clamp the stops to the dynamic range limits of the AgX shaper space. The AgX configuration spans **16.5 stops** of exposure, bounded between **$-12.47393$ stops (Min)** and **$4.026069$ stops (Max)**:

$$EV_{clamped} = \text{clamp}(EV, -12.47393, 4.026069)$$

#### Step 3: Normalization to [0, 1]
We scale this logarithmic stops value into a normalized float range between $0.0$ and $1.0$:

$$x_{norm} = \frac{EV_{clamped} - (-12.47393)}{4.026069 - (-12.47393)} = \frac{EV_{clamped} + 12.47393}{16.5}$$

#### Step 4: Sigmoid Contrast Polynomial
We apply the 6th-order contrast polynomial to each channel. This polynomial acts as the filmic sigmoid curve, compressing highlights and stabilizing blacks:

$$y = 15.5 x^6 - 40.14 x^5 + 31.96 x^4 - 6.868 x^3 + 0.4298 x^2 + 0.1191 x - 0.00232$$

#### Step 5: Outset Matrix
To restore our color gamut boundaries and return the primaries to their correct relationships, we multiply the contrasted color by the `AgXOutsetMatrix`:

$$\begin{bmatrix} R_{out} \\ G_{out} \\ B_{out} \end{bmatrix} = \begin{bmatrix}
1.12710058 & -0.14132976 & -0.14132976 \\
-0.11060664 & 1.15782370 & -0.11060664 \\
-0.01649394 & -0.01649394 & 1.25193641
\end{bmatrix} \begin{bmatrix} R_{sigmoid} \\ G_{sigmoid} \\ B_{sigmoid} \end{bmatrix}$$

#### Step 6: Display EOTF (Gamma 2.2)
Finally, we apply the display gamma (usually sRGB or standard power $2.2$):

$$Color_{display} = \text{clamp}(Color_{out}, 0.0, 1.0)^{1 / 2.2}$$

---

### 💻 Python Project 1: Recreating AgX in Code
Create a new file named `test_agx.py` in your scratchpad or run this script. It applies the complete AgX mathematical pipeline using pure Python and NumPy.

```python
import numpy as np

# AgX Constants
AGX_INSET = np.array([
    [0.856627153315983, 0.137318972929847, 0.11189821299995],
    [0.0951212405381588, 0.761241990602591, 0.0767994186031903],
    [0.0482516061458583, 0.101439036467562, 0.811302368396859]
])

AGX_OUTSET = np.array([
    [1.1271005818144368, -0.1413297634984383, -0.14132976349843826],
    [-0.11060664309660323, 1.157823702216272, -0.11060664309660294],
    [-0.016493938717834573, -0.016493938717834257, 1.2519364065950405]
])

MIN_EV = -12.47393
MAX_EV = 4.026069
SPAN_EV = MAX_EV - MIN_EV # 16.5 stops

def agx_default_contrast_approx(x):
    """6th-order polynomial approximation of the AgX sigmoid curve."""
    x2 = x * x
    x4 = x2 * x2
    return (15.5 * x4 * x2 - 
            40.14 * x4 * x + 
            31.96 * x4 - 
            6.868 * x2 * x + 
            0.4298 * x2 + 
            0.1191 * x - 
            0.00232)

def linear_to_agx_display(rgb_linear):
    """
    Applies the full AgX Display Transform pipeline to a scene-linear RGB value.
    Input: numpy array of shape (..., 3) containing linear light values.
    """
    # 1. Apply Inset Matrix
    # We use dot product on the last axis
    color = np.dot(rgb_linear, AGX_INSET.T)
    
    # 2. Clamp to prevent log(0) and apply Log2
    color = np.maximum(color, 1e-10)
    log_ev = np.log2(color)
    
    # 3. Clamp log stops to AgX EV limits and normalize to [0, 1]
    log_ev = np.clip(log_ev, MIN_EV, MAX_EV)
    norm_x = (log_ev - MIN_EV) / SPAN_EV
    
    # 4. Apply Sigmoid Contrast
    sigmoid = agx_default_contrast_approx(norm_x)
    
    # 5. Apply Outset Matrix
    out = np.dot(sigmoid, AGX_OUTSET.T)
    
    # 6. Apply display gamma (EOTF power 2.2) and clamp final pixels
    out = np.clip(out, 0.0, 1.0)
    display_pixels = np.power(out, 1.0 / 2.2)
    
    return display_pixels

# Test Verification
if __name__ == "__main__":
    # Test values: Deep Shadow, Middle Gray (18%), Diffuse White, and HDR Highlight (10x White)
    test_inputs = np.array([
        [0.001, 0.001, 0.001],  # Deep Shadow
        [0.18, 0.18, 0.18],     # Middle Gray
        [1.0, 1.0, 1.0],        # Diffuse White
        [10.0, 5.0, 2.0]        # HDR Highlight (Saturated orange-red)
    ])
    
    outputs = linear_to_agx_display(test_inputs)
    
    print("--- AgX Reconstruction Test Results ---")
    for inp, out in zip(test_inputs, outputs):
        print(f"Linear Input: {inp} -> AgX Display Output: {out.round(4)}")
```

---

## 🟣 Module 5: Designing Your Own Custom Log Curves

### 5.1 Formulation of Custom Curves
To design a custom camera log curve for a game engine or a custom camera project, you must mathematically solve for the four constants ($a, b, c, d$) of the Cineon equation based on your artistic constraints.

Suppose you want to design a curve with the following parameters:
1. **Dynamic Range ($N$ Stops):** You want to fit $12$ stops of linear light. Thus, our linear black point $x_{min}$ and linear white point $x_{max}$ are separated by $12$ stops ($2^{12} = 4096$ scaling factor).
2. **Anchor Point:** We want linear Middle Gray ($x_{gray} = 0.18$) to map to exactly $y_{gray} = 0.40$ in our normalized digital file.
3. **Black Pedestal:** Linear zero ($x = 0$) should map to exactly $y_{min} = 0.05$ (ensuring total darkness has a safe recordable digital level).

This gives us a system of algebraic constraints:

$$\text{Constraint 1: } y(0) = c \cdot \log_{10}(b) + d = y_{min}$$
$$\text{Constraint 2: } y(0.18) = c \cdot \log_{10}(a \cdot 0.18 + b) + d = y_{gray}$$
$$\text{Constraint 3: } y(x_{max}) = c \cdot \log_{10}(a \cdot x_{max} + b) + d = 1.0$$

By setting the scale factor $a$ (which behaves as our sensitivity slider), we can numerically solve for the remaining variables!

---

### 💻 Python Project 2: Automatic Log Solver & LUT Generator
Here is a complete, copy-paste ready Python script. It:
1. Solves the system of equations for your custom log curve parameters using a numerical solver.
2. Mathematically verifies that your constraints are perfectly satisfied.
3. Generates a standard **1D LUT (Look-Up Table)** in the industry-standard `.cube` format, which can be loaded directly into DaVinci Resolve, Premiere Pro, Photoshop, or OBS.

```python
import math

def solve_log_constants(a, x_gray, y_gray, y_min):
    """
    Solves for the constants c and d of the Cineon formula:
    y = c * log10(a * x + b) + d
    
    Given:
      - a: Sensitivity scalar (user-defined tuning parameter)
      - x_gray: Middle Gray linear input (usually 0.18)
      - y_gray: Target Middle Gray output (usually 0.40 - 0.45)
      - y_min: Pedestal output for x=0 (usually 0.05 - 0.09)
      
    For simplicity, we set the safety cushion 'b' equal to 0.01.
    """
    b = 0.01
    
    # Using Constraint 1: y_min = c * log10(b) + d -> d = y_min - c * log10(b)
    # Using Constraint 2: y_gray = c * log10(a * x_gray + b) + d
    # Substitute d into Constraint 2:
    # y_gray = c * log10(a * x_gray + b) + y_min - c * log10(b)
    # y_gray - y_min = c * (log10(a * x_gray + b) - log10(b))
    # y_gray - y_min = c * log10((a * x_gray + b) / b)
    
    numerator = a * x_gray + b
    log_ratio = math.log10(numerator / b)
    
    # Solve for c
    c = (y_gray - y_min) / log_ratio
    
    # Solve for d using d = y_min - c * log10(b)
    d = y_min - c * math.log10(b)
    
    return b, c, d

def generate_1d_lut(filename, b, c, d, a, size=1024):
    """
    Generates a standard 1D LUT .cube file for the custom log curve.
    This LUT converts scene-referred linear values [0, 1] into our custom log curve.
    """
    with open(filename, 'w') as f:
        f.write("# Custom Log curve generator\n")
        f.write(f"TITLE \"Custom_Linear_to_Log_a{a}\"\n")
        f.write(f"LUT_1D_SIZE {size}\n")
        f.write("LUT_1D_INPUT_RANGE 0.0 1.0\n\n")
        
        for i in range(size):
            # Normalized input coordinate from 0.0 to 1.0
            x = i / (size - 1)
            
            # Apply our solved Cineon formula
            # Clamping input to 0 to prevent negative values in log
            x_clamp = max(x, 0.0)
            y = c * math.log10(a * x_clamp + b) + d
            
            # Clamp output to standard video ranges [0, 1]
            y = min(max(y, 0.0), 1.0)
            
            # Write to LUT file (RGB identical since it's 1D)
            f.write(f"{y:.6f} {y:.6f} {y:.6f}\n")
            
    print(f"Successfully generated 1D LUT file: '{filename}'")

if __name__ == "__main__":
    # Custom Log Curve Design Parameters
    sensitivity = 15.0       # We want a high-sensitivity curve (large dynamic range compression)
    linear_gray = 0.18       # 18% Middle Gray
    target_gray = 0.42       # We want Middle Gray to sit at 42% on the waveform
    pedestal_black = 0.08    # Total darkness sits raised at 8% (milky blacks look)
    
    # 1. Solve for constants
    b, c, d = solve_log_constants(sensitivity, linear_gray, target_gray, pedestal_black)
    
    print("--- SOLVED CUSTOM LOG CONSTANTS ---")
    print(f"Formula: y = {c:.6f} * log10({sensitivity:.1f} * x + {b:.2f}) + {d:.6f}")
    
    # 2. Verify math constraints
    # Check black point: x = 0
    y_black_verify = c * math.log10(b) + d
    # Check gray point: x = 0.18
    y_gray_verify = c * math.log10(sensitivity * linear_gray + b) + d
    
    print("\n--- CONSTRAINT VERIFICATION ---")
    print(f"Target Black Pedestal: {pedestal_black} -> Math result: {y_black_verify:.6f} (Pass: {abs(y_black_verify - pedestal_black) < 1e-6})")
    print(f"Target Middle Gray:    {target_gray}  -> Math result: {y_gray_verify:.6f} (Pass: {abs(y_gray_verify - target_gray) < 1e-6})")
    
    # 3. Generate the LUT file
    lut_filename = "Custom_Linear_to_Log.cube"
    generate_1d_lut(lut_filename, b, c, d, sensitivity)
```

---

## 🏁 Course Summary Checklist
As you progress through your experiments in Desmos and Python, check off these foundational milestones:

* [ ] **The "Why":** Can you explain to a colleague why digital cameras must shoot in log to preserve dynamic range?
* [ ] **Rule manipulation:** Can you algebraically expand $\log_{10}(a \cdot x + b)$ to isolate $x$ when solving for inverse functions?
* [ ] **AgX Inset Purpose:** Do you understand why AgX pulls colors inward *before* applying the logarithmic scale? (Answer: To desaturate bright highlights naturally instead of skewing their hues).
* [ ] **LUT Generation:** Have you generated the `Custom_Linear_to_Log.cube` file and verified that it opens inside your video software?
