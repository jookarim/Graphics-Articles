# Perspective Projection

In perspective projection, $\tan(\theta)$ represents the angle of the perspective triangle.  
However, we do not directly use the angle — we want a **scalar value** that scales coordinates.  
So we use:

$$
\frac{1}{\tan(\theta)}
$$

This acts as a scaling factor.

For example:

$$
\frac{1}{45} > \frac{1}{60}
$$

So smaller angles produce stronger magnification.

---

## Aspect Ratio

We also have something called **aspect ratio**:

$$
\text{aspect} = \frac{\text{window width}}{\text{window height}}
$$

The window is usually a rectangle, not a square.  
Without aspect ratio correction, objects would appear stretched.

In the projection transformation:

- $x$ is multiplied by the aspect ratio  
- $x$ and $y$ are multiplied by $\frac{1}{\tan(\text{FOV}/2)}$

This ensures:

- Bigger FOV → objects appear smaller  
- Smaller FOV → objects appear bigger  

---

## Near and Far Plane Normalization

To normalize depth into the range $[-1, 1]$, we introduce two constants:

$$
A = \frac{f + n}{f - n}
$$

$$
B = \frac{2fn}{f - n}
$$

Where:

- $n$ = near plane  
- $f$ = far plane  

These terms come directly from the perspective projection matrix.

---

## Clip Space Z

After multiplying the projection matrix with a vertex $[X, Y, Z, W]$, the clip-space Z becomes:

$$
Z_{\text{clip}} = A Z + B
$$

In perspective projection:

$$
W = -Z
$$

So after the perspective divide:

$$
Z_{\text{ndc}} = \frac{A Z + B}{-Z}
$$

We can split it:

$$
Z_{\text{ndc}} = \frac{A Z}{-Z} + \frac{B}{-Z}
$$

$$
Z_{\text{ndc}} = -A - \frac{B}{Z}
$$

Now we clearly see two things:

- $A$ and $B$ are constants used for normalization  
- Depth depends on $\frac{1}{Z}$  

This is where non-linear depth appears.

---

## Why Depth Is Non-Linear

Perspective projection scales geometry proportionally to:

$$
\frac{1}{Z}
$$

Imagine this:

- If a tree is 3 meters away and moves 1 meter, you clearly see the change.
- If a tree is 50 meters away and moves 1 meter, you barely see the difference.

Because perspective shrinks geometry like $\frac{1}{Z}$, depth must also behave like $\frac{1}{Z}$.

If depth were linear:

- Far objects would collapse into very small depth differences  
- This would cause severe Z-fighting  

Non-linear depth:

- Gives very high precision near the camera  
- Gives less precision far away  
- Matches how perspective compresses space  

This is why GPUs use non-linear depth in perspective projection.