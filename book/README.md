# Chapter 3 (Detailed): Intensity Transformations and Spatial Processing

## Part 1: Basic Intensity Transformations

Intensity transformation functions are of the form `s = T(r)`, where `r` is the input intensity and `s` is the output intensity. For these functions to be valid, they must generally be **monotonically increasing** to preserve the order of intensity levels and prevent artifacts.

### 1. Contrast Stretching
Low-contrast images often result from poor lighting or limited sensor dynamic range. Contrast stretching expands the range of intensity levels so they span the full display range.

- **Transformation Function**: A piecewise linear function controlled by points `(r₁, s₁)` and `(r₂, s₂)`.
- **Special Case — Thresholding**: If `r₁ = r₂ = m`, `s₁ = 0`, and `s₂ = L−1`, the function becomes a binary thresholding function.

### 2. Intensity-Level Slicing
Highlights a specific range `[A, B]` of intensities. Useful for enhancing features like water in satellite imagery or flaws in X-rays.

- **Binary Slicing**: Displays the range of interest in one value (e.g., white) and all others in another (e.g., black).
- **Background-Preserving Slicing**: Brightens/darkens the desired range while leaving other levels unchanged.

> **Numerical Example — Thresholding a 3-bit Image:** For a 3-bit image (L=8) with threshold `m=4`:
> `T(r) = 0` if `r < 4`, and `T(r) = 7` if `r ≥ 4`.
> Pixels with intensity 0–3 become black; pixels with intensity 4–7 become white.

### 3. Bit-Plane Slicing
Examines the contribution of specific bits in an 8-bit image (8 one-bit planes).

- **Higher-Order Planes**: Bit planes 7 and 8 (most significant bits) contain the bulk of visually significant data.
- **Lower-Order Planes**: Contribute subtle intensity details.
- **Application**: Useful for image compression — reconstructing from only the 4 highest-order planes saves 50% storage.

---

## Part 2: Histogram Processing Foundations

A histogram is a powerful tool for analyzing the intensity characteristics of an image. For a digital image with `L` possible intensity levels in `[0, L−1]`:

- **Unnormalized histogram**: `h(rₖ) = nₖ` (count of pixels with intensity `rₖ`).
- **Normalized histogram**: `p(rₖ) = nₖ / (MN)` (probability estimate for each intensity level; sums to 1).

### Histogram Shape and Image Appearance

| Histogram Shape | Image Appearance |
|----------------|-----------------|
| Concentrated at low end | Dark image |
| Concentrated at high end | Light/bright image |
| Narrow, near center | Low contrast (dull, gray) |
| Wide, spread uniformly | High contrast |

---

## Part 3: Histogram Equalization (Continuous Case)

Histogram equalization is an **automatic** method for contrast enhancement. It finds a transformation `s = T(r)` that produces an output with a **uniform PDF**.

**Requirements for T(r)** (to prevent artifacts):
1. **Monotonically increasing** over `[0, L−1]`.
2. Output range: `0 ≤ T(r) ≤ L−1`.

**The Transformation Function** (the CDF of input intensities):

$$s = T(r) = (L-1) \int_0^r p_r(w)\, dw$$

Applying this CDF-based transformation to any input PDF always yields a **uniform output PDF**: `p_s(s) = 1/(L−1)`.

> **Numerical Example:** For `p_r(r) = 2r / (L−1)²`:
> `s = T(r) = r² / (L−1)`.
> Squaring and normalizing yields a perfectly uniform distribution.

---

## Part 4: Discrete Histogram Equalization

In the digital world, integrals become summations:

$$s_k = T(r_k) = \frac{L-1}{MN} \sum_{j=0}^{k} n_j$$

> **Numerical Example** (3-bit image, 64×64, L=8, MN=4096):
>
> | `rₖ` | `nₖ` | `p(rₖ)` | `sₖ` (rounded) |
> |------|-------|---------|----------------|
> | 0 | 790 | 0.19 | **1** |
> | 1 | 1023 | 0.25 | **3** |
> | 2 | 850 | 0.21 | **5** |
> | 3 | 656 | 0.16 | **6** |
> | 4 | 329 | 0.08 | **6** |
> | 5 | 245 | 0.06 | **7** |
> | 6 | 122 | 0.03 | **7** |
> | 7 | 81 | 0.02 | **7** |
>
> **Key Observations:**
> - `r₃` and `r₄` both map to 6; `r₅`, `r₆`, `r₇` all map to 7 (**intensity merging**).
> - Result has only 5 distinct levels (1, 3, 5, 6, 7) — contrast is enhanced.
> - A **perfectly flat histogram** is rarely achieved in the discrete case; the goal is to *spread* intensities across a wider range.

---

## Part 5: Histogram Matching (Specification)

Instead of forcing a uniform histogram, we can "match" the image to any **desired target PDF** `pz(z)`.

**The Procedure:**
1. **Equalize the Input**: Compute `s = T(r)` from the input image's histogram.
2. **Define Target**: Compute `G(z) = (L−1) ∫₀ᶻ pz(v) dv` from the desired histogram.
3. **Map Backwards**: Since `G(z) = s`, find `z = G⁻¹(s)`.

In practice (discrete), a **look-up table** finds the `zq` for each `sₖ` such that `G(zq)` is closest to `sₖ`.

---

## Part 6: Fundamentals of Spatial Filtering

Spatial filtering modifies each pixel by computing a function over its **neighborhood** using a **kernel** (also called a mask, template, or window).

### 1. Linear Filtering (Correlation)
For a kernel of size `m×n` (where `m = 2a+1`, `n = 2b+1`):

$$g(x,y) = \sum_{s=-a}^{a} \sum_{t=-b}^{b} w(s,t)\, f(x+s,\, y+t)$$

- `w(s,t)`: Kernel coefficients.
- `f(x+s, y+t)`: Pixel values under the kernel.

### 2. Correlation vs. Convolution

| Operation | Description |
|-----------|-------------|
| **Correlation** | Kernel slides over image; sum of products computed at each location. |
| **Convolution** | Identical, but the kernel is **rotated 180°** before the operation. |

*If the kernel is symmetric, both operations yield the same result.*

### 3. Padding Strategies
When the kernel overlaps the image boundary:

- **Zero Padding**: Surround image with zeros.
- **Mirror Padding**: Reflect the image across the border.
- **Replicate Padding**: Extend border pixel values outward.

---

## Part 7: Smoothing (Lowpass) Filters

Smoothing filters reduce noise and blur fine detail by **attenuating high frequencies** (sharp transitions).

### 1. Box Filter
Simplest lowpass filter — all kernel coefficients equal `1`, normalized by `1/mn`.

- **Effect**: Larger kernels produce stronger blurring.

### 2. Gaussian Filter
An **isotropic** (circularly symmetric) filter — response is independent of orientation.

$$G(s,t) = K \cdot e^{-(s^2+t^2)/(2\sigma^2)}$$

- **σ**: Controls the spread/degree of blurring.
- **Kernel Size Rule of Thumb**: Set kernel size to approximately `6σ × 6σ`.

> **Numerical Example — 3×3 Box Filter:**
>
> Input neighborhood:
> ```
> | 10 | 20 | 10 |
> | 20 | 50 | 20 |
> | 10 | 20 | 10 |
> ```
> Normalized kernel = (1/9) × all-ones matrix.
>
> `g(x,y) = (1/9) × (170) = 170/9 ≈ **18.89**`
>
> The center pixel's intensity drops from **50** to approximately **19**.

---

## Part 8: Sharpening (Highpass) Spatial Filters

Sharpening uses **spatial differentiation** to highlight intensity transitions (edges, fine detail, noise).

### 1. Foundation of Derivatives
- **First-Order Derivative**: `f(x+1) − f(x)`. Non-zero at onset of ramps/steps.
- **Second-Order Derivative**: `f(x+1) + f(x−1) − 2f(x)`. Zero along ramps; non-zero at their onset/end.
- **Key**: The second-order derivative enhances fine detail *much better* than the first.

### 2. The Laplacian (∇²f)
The simplest **isotropic** (rotation-invariant) second-order derivative operator.

- **Discrete Equation**: `∇²f(x,y) = f(x+1,y) + f(x−1,y) + f(x,y+1) + f(x,y−1) − 4f(x,y)`
- **Basic kernel** (center = −4): Cardinal neighbors only.
- **Extended kernel** (center = −8): All 8 neighbors including diagonals.

### 3. Image Sharpening with the Laplacian

$$g(x,y) = f(x,y) + c\, [\nabla^2 f(x,y)]$$

- `c = −1` when the kernel center coefficient is negative.
- `c = +1` when the kernel center coefficient is positive.

---

## Part 9: Unsharp Masking and Highboost Filtering

A classic sharpening technique using a **blurred mask**.

**The Process:**
1. **Blur** the original image to get `f*(x,y)`.
2. **Create mask**: `g_mask(x,y) = f(x,y) − f*(x,y)`.
3. **Add weighted mask**: `g(x,y) = f(x,y) + k · g_mask(x,y)`.
   - `k = 1`: Standard **Unsharp Masking**.
   - `k > 1`: **Highboost Filtering** (further emphasizes edges).

> **Numerical Example — Laplacian Sharpening:**
>
> Input 3×3 segment:
> ```
> | 10 | 10 | 10 |
> | 10 | 100| 10 |
> | 10 | 10 | 10 |
> ```
> Laplacian kernel:
> ```
> |  0 |  1 |  0 |
> |  1 | -4 |  1 |
> |  0 |  1 |  0 |
> ```
> **Step 1** — Compute ∇²f:
> `∇²f = 10+10+10+10 + (−4×100) = 40 − 400 = **−360**`
>
> **Step 2** — Apply sharpening formula (`c = −1`):
> `g(center) = 100 − (−360) = **460**`
>
> The center pixel's intensity is boosted from **100** to **460**.

---

## Part 10: First-Order Derivatives (The Gradient)

The **gradient** gives us a 2D vector pointing in the direction of steepest intensity change.

$$\nabla f = \begin{bmatrix} g_x \\ g_y \end{bmatrix} = \begin{bmatrix} \partial f/\partial x \\ \partial f/\partial y \end{bmatrix}$$

**Gradient Magnitude** (rate of change in the gradient direction):

$$M(x,y) = \sqrt{g_x^2 + g_y^2}$$

Approximated for efficiency: `M(x,y) ≈ |gx| + |gy|`

### The Sobel Operators
The most popular 3×3 kernels for approximating partial derivatives:

**x-direction (gx)** — derivative along rows:
```
| -1 |  0 |  1 |
| -2 |  0 |  2 |
| -1 |  0 |  1 |
```

**y-direction (gy)** — derivative along columns:
```
| -1 | -2 | -1 |
|  0 |  0 |  0 |
|  1 |  2 |  1 |
```

*The center coefficient weight of 2 provides a smoothing effect to reduce noise sensitivity.*

---

## Part 11: Case Study — Combining Enhancement Methods

Single techniques are rarely sufficient. Classic example: enhancing a **nuclear bone scan** (low contrast, high noise).

**Strategy Pipeline:**
1. **Laplacian** → highlight fine details (small bone structures).
2. **Sobel Gradient** → enhance prominent edges.
3. **5×5 Box Filter** on the gradient image → reduce noise.
4. **Masking** → multiply Laplacian by smoothed gradient (preserve detail at edges, suppress noise in flat areas).
5. **Addition** → add result back to original image.
6. **Power-Law Transform** (γ < 1, e.g., γ = 0.5) → expand dynamic range.

> **Numerical Example — Roberts Cross Gradient:**
> The Roberts Cross operators are a simpler **2×2** version of the gradient.
>
> For a 2×2 segment:
> ```
> | 50 | 10 |
> | 10 | 10 |
> ```
> Kernels: `wx = [[-1, 0], [0, 1]]`, `wy = [[0, 1], [-1, 0]]`
>
> - `gx = (1·10) + (−1·50) = −40`
> - `gy = (1·10) + (−1·10) = 0`
> - `M(x,y) = √(40² + 0²) = **40**`

---

## Part 12: Computational Advantage of Separable Kernels

Many filters (Box, Gaussian) are **separable** — expressible as the outer product of two 1-D vectors: `w = v · wᵀ`.

**Benefit:** Instead of one 2-D convolution, perform two 1-D convolutions (first along rows, then along columns of the result).

**Identification:** A matrix is separable if its **rank is 1**.

### Efficiency Comparison

For an image of size `M×N` and kernel of size `m×n`:

| Method | Operations per pixel |
|--------|---------------------|
| Standard 2-D convolution | `mn` |
| Separable 1-D convolutions | `m + n` |

**Computational Advantage:** `C = mn / (m+n)`

> **Numerical Example — 11×11 Gaussian:**
> - Standard: `11 × 11 = 121` operations/pixel
> - Separable: `11 + 11 = 22` operations/pixel
> - **Advantage: C = 121/22 = 5.5×**
>
> For a 512×512 kernel, the advantage is **262:1** — a task that takes 2.2 hours with a standard 2-D kernel takes only **30 seconds** with separable convolutions.

---

# Chapter 4: Filtering in the Frequency Domain

## Part 1: Foundations of the Frequency Domain

In the frequency domain, we don't look at pixels — we look at **sinusoids**. Any image can be expressed as the sum of sinusoids of different frequencies, amplitudes, and phases.

### 1. The Role of the Fourier Transform
- **Forward Transform**: Converts an image from the **spatial domain** `(x,y)` to the **frequency domain** `(u,v)`.
- **Inverse Transform**: Converts processed frequency data back to the spatial domain for display.

### 2. Frequency Characteristics and Image Appearance

| Frequency Band | Visual Feature |
|---------------|----------------|
| **Low frequencies** | Slowly varying intensities (smooth backgrounds, flat regions) |
| **High frequencies** | Sharp intensity transitions (edges, fine details, noise) |

- Reducing **high** frequencies → image becomes **blurred**.
- Reducing **low** frequencies → edges and details are **highlighted**.

---

## Part 2: The Duality of Filtering (Convolution Theorem)

A fundamental principle of linear system theory:

> **Convolution in the spatial domain ≡ Multiplication in the frequency domain** (and vice versa).

This gives us **two equivalent ways** to apply any filter:

1. **Spatial Filtering**: Convolve the image with a filter kernel (e.g., 3×3, 5×5).
2. **Frequency Domain Filtering**: Multiply the Fourier transform of the image by a filter **transfer function** `H(u,v)`, then take the inverse Fourier transform.

### Ideal Lowpass Filters (ILPF)
An ILPF completely eliminates all frequencies above a cut-off `u₀` and passes all below it unchanged.

- **Problem**: ILPFs are not physically realizable and cause **ringing artifacts** in digital images.

> **Numerical Concept — The Impulse:**
> - A single impulse (a `1` in a field of `0`s) in the spatial domain has a Fourier transform that is a **constant across all frequencies** (it contains all frequencies equally).
> - Conversely, a constant (flat gray image) in the spatial domain is an **impulse at the origin** in the frequency domain.

---

## Part 3: The 1D Discrete Fourier Transform (DFT)

For a digital signal `f(x)` with `M` samples (`x = 0, 1, …, M−1`):

$$F(u) = \sum_{x=0}^{M-1} f(x)\, e^{-j2\pi ux/M}$$

- `u`: Discrete frequency variable, `u = 0, 1, …, M−1`.
- `e^{-j2πux/M} = cos(2πux/M) − j·sin(2πux/M)` (Euler's formula).
- `F(u)`: The resulting complex value — the **Fourier Spectrum**.

### The Inverse 1D DFT (IDFT)

$$f(x) = \frac{1}{M} \sum_{u=0}^{M-1} F(u)\, e^{j2\pi ux/M}$$

Any digital signal can be reconstructed by summing complex sinusoids weighted by `F(u)`.

### Key Concepts: Magnitude and Phase

Since `F(u) = R(u) + jI(u)` is complex:

- **Magnitude (Spectrum)**: `|F(u)| = √(R(u)² + I(u)²)` — tells us *how much* of each frequency is present.
- **Phase Angle**: `φ(u) = arctan(I(u)/R(u))` — tells us the *alignment/displacement* of those frequencies.

> **Numerical Example — 4-Point DFT:**
> Signal: `f(x) = {1, 2, 4, 4}`, `M = 4`. Find the DC component (`u = 0`):
>
> `F(0) = 1 + 2 + 4 + 4 = **11**`
>
> Average intensity = 11/4 = **2.75** (mirrors the global mean from histogram processing).

---

## Part 4: Why Use 1D DFT in Image Processing?

Even though images are 2D, the 1D DFT is fundamental because:

- **Separability**: A 2D DFT can be computed by taking the 1D DFT of every **row**, then the 1D DFT of every **column** of that result (analogous to separable spatial kernels) — dramatically reducing computation.
- **Filter Design**: We often design a 1-D spatial filter with a specific frequency response and then **rotate it** to create a circular 2D kernel.