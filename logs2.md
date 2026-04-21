# Logarithms and Color Science: A Beginner's Mastery Guide

This guide covers the transition from basic mathematical logarithms to the normalized log-encoding curves used in digital cinematography (e.g., Apple Log).

## 1. The Core Concept
A logarithm is the inverse of an exponent. It asks: **"What power do I need to reach this number?"**

* **Exponential:** $3^2 = 9$
* **Logarithmic:** $\log_{3}(9) = 2$

### Common Logarithm
In science and video production, if a base isn't written, it is assumed to be **base 10**.
* $\log(10) = 1$
* $\log(100) = 2$
* $\log(10,000) = 4$

---

## 2. The Three Golden Rules of Manipulation
To manipulate log curves, use these three rules to simplify complex math:

| Rule | Formula | Purpose |
| :--- | :--- | :--- |
| **Product** | $\log(A \cdot B) = \log(A) + \log(B)$ | Turns multiplication into addition. |
| **Quotient** | $\log(A / B) = \log(A) - \log(B)$ | Turns division into subtraction. |
| **Power** | $\log(A^p) = p \cdot \log(A)$ | Moves exponents to the front as multipliers. |

---

## 3. The Linear-to-Log Conversion Formula
To "squeeze" real-world linear light into a digital file range of **0 to 1**, we use a normalized formula.

### The Components
* **$x$**: The input linear light.
* **$M$**: The maximum light range (e.g., 10 or 100).
* **$a$**: The sensitivity/intensity of the curve (the "bend").
* **$g$ (Gamma)**: The pivot used to anchor the midtones.
* **$c$**: The slope constant used to normalize the ceiling to 1.0.

### The "Indestructible" Formula
This formula stays anchored at $(0,0)$ and $(M,1)$ regardless of your settings:

$$y = \left( c \cdot \log_{10}\left( a \cdot \frac{x}{M} + 1 \right) \right)^g$$

Where:
$$c = \frac{1}{\log_{10}(a + 1)}$$

---

## 4. Professional Anchoring (Middle Gray)
In cinematography, we must anchor **18% Gray (0.18)** to a specific digital signal value (often **0.45**).

### Desmos Implementation
To find the exact value of $g$ needed to hit your target, use a **Regression** line in Desmos:

1.  **Define Target:** $y_{target} = 0.45$
2.  **Define Point:** $x_{mid} = 0.18 \cdot M$
3.  **Regression Line:**
    `0.45 ~ (c * log10(a * (x_mid / M) + 1))^g`

This forces Desmos to calculate the value of $g$ that keeps middle gray perfectly exposed, even if you change the dynamic range ($a$) or the max light ($M$).

---

## 5. Visualizing the Curve (Desmos Setup)
To keep the graph visual and accurate:
1.  **X-Axis:** Set bounds from `0` to `M`.
2.  **Y-Axis:** Set bounds from `0` to `1`.
3.  **The "Knee":** Notice how the curve is steep in the shadows (0.0 to 0.2) and flattens out in the highlights. This "compression" is what allows Log footage to preserve highlight detail that would otherwise be clipped in a linear image.

---

## 6. Real-World Applications
* **Offset:** Shifts the whole curve. Subtraction "crushes" blacks; addition "lifts" them.
* **Slope:** Changes the steepness/contrast.
* **Inverse Log:** The exponential math used to "undo" the log look and return the image to linear for viewing.
