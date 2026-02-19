# Histogram Equalization Techniques on Thermal Images
> Based on: *"Performance Evaluation of Various Histogram Equalization Techniques on Thermal Images"* — Singh et al., IEEE CVMI 2023

---

## Why Thermal Images Need Enhancement

- Thermal cameras capture **infrared radiation** (not visible light), giving temperature info
- They work in **poor lighting & cluttered backgrounds** — useful for autonomous vehicles, drones
- **Key problem**: Thermal cameras have *much lower resolution* than RGB cameras, making object detection harder
- Pre-processing with histogram equalization improves contrast **before** feeding into deep learning models → reduces training time, boosts accuracy

---

## 1. The Core Concept: The Transfer Function

To change the brightness of an image, we use a transformation function, $T(r)$. The new intensity level $s_k$ is calculated based on the old intensity level $r_k$ using the **Cumulative Distribution Function (CDF)**:

$$s_k = T(r_k) = (L-1) \sum_{j=0}^{k} p_r(r_j)$$

### Key Terms
| Symbol | Meaning |
|:---|:---|
| $L-1$ | Maximum intensity level (e.g., $L=256$ for 8-bit images, so $L-1=255$) |
| $p_r(r_j)$ | Probability of a pixel having intensity $r_j$ (normalized histogram value) |
| $\sum$ | Cumulative sum of probabilities from darkest level up to level $k$ |

**PDF (Probability Distribution Function):**
$$p(y_k) = \frac{n_k}{n}$$
where $n_k$ = number of pixels at intensity $k$, $n$ = total pixels.

**CDF:**
$$F(y_k) = \sum_{i=0}^{k} p(y_i)$$
Note: $F(y_{L-1}) = 1$ always.

---

## 2. Calculation Example

Given $p_r(r_0) = 0.19$, $p_r(r_1) = 0.25$, and $L=8$ (so $L-1=7$):

### Level 0 ($s_0$)
$$s_0 = 7 \times 0.19 = 1.33$$

### Level 1 ($s_1$)
$$s_1 = 7 \times (0.19 + 0.25) = 7 \times 0.44 = 3.08$$

---

## 3. Mapping to New Intensities

Since digital images use **integer values**, we round the calculated $s_k$ to the nearest integer.

| Original $s_k$ | Rounded New Intensity |
|:---|:---|
| 1.33 | **1** |
| 3.08 | **3** |
| 4.55 | **5** |
| 5.67 | **6** |
| 7.00 | **7** |

> **Note**: By the final level ($s_7$), cumulative probability = 1.0, so $7 \times 1.0 = 7$. This ensures the full dynamic range is always utilized.

---

## 4. Pixel Redistribution

The "equalization" occurs because multiple original intensity levels may **map to the same new level**, and some new levels are skipped (creating gaps).

- **Original Level 0** → New Level 1
- **Original Level 1** → New Level 3
- **Original Level 2** → New Level 5
- **Original Level 3** → New Level 6
- **Original Levels 5, 6, 7** → New Level 7

### New Pixel Counts ($n_s$)
| New Level | Source | Pixel Count |
|:---|:---|:---|
| 1 | Old Level 0 | 790 |
| 3 | Old Level 1 | 1023 |
| 5 | Old Level 2 | 850 |
| 7 | Old Levels 5+6+7 | $245 + 122 + 81 = 448$ |

---

## 5. The Six HE Variants (Paper's Focus)

### 5.1 Global Histogram Equalization (GHE)
- Increases **global contrast** of the entire image uniformly
- Good for images that are **uniformly** dark or bright
- ⚠️ **Drawback**: Boosts background noise indiscriminately; loses local information

---

### 5.2 Adaptive Histogram Equalization (AHE)
- Breaks image into small **non-overlapping tiles**, equalizes each separately
- Uses a **local transformation function**:
$$T(r) = r_0 + (r_{L-1} - r_0) \cdot F(r)$$
- Output image:
$$s = T(r) \text{ for all } r(i,j) \in R$$
- Best for images with **uneven lighting** or low contrast regions
- ⚠️ **Drawback**: Can over-amplify noise in uniform regions

---

### 5.3 Contrast Limited Adaptive Histogram Equalization (CLAHE)
- Like AHE, but adds a **contrast-limiting step** — stops enhancement after a threshold
- Prevents over-amplification of noise
- Avoids **halos/artifacts** around object edges by fusing foreground & background separately
- Widely used in medical imaging preprocessing (e.g., diabetic retinopathy detection)

---

### 5.4 Brightness Preserving Bi-Histogram Equalization (BBHE)
- Splits histogram into **two halves** at the mean intensity value $\mu_Y$, then equalizes each half independently
- This preserves overall brightness while enhancing contrast

**Math:**
$$\mu_Y = \frac{\sum_{k=0}^{L-1} k \cdot p(y_k)}{\sum_{k=0}^{L-1} p(y_k)}$$

$$Y_L = \{y(i,j) \mid y(i,j) \leq \mu_Y\}, \quad Y_U = \{y(i,j) \mid y(i,j) > \mu_Y\}$$

- Works best when the input histogram has a **quasi-symmetrical distribution** around its mean

---

### 5.5 Plateau Limit Histogram Equalization (PLHE)
- Designed specifically for **infrared/thermal images**
- Addresses **plateau regions** — areas where pixels hit saturation and lose detail
- Uses **power-log transformation** to compress saturated regions while preserving non-saturated detail
- Prevents over-amplification of noise and avoids loss of local information

---

### 5.6 Minimum Mean Brightness Error Bi-Histogram Equalization (MMBEBHE) ⭐ Best Performer
- Extension of BBHE — instead of splitting at the mean, splits at a **threshold $T$** that minimizes the **Absolute Mean Brightness Error (AMBE)**
- Recursive, integer-based solution

**Steps:**
1. Calculate AMBE for every possible threshold level
2. Find the threshold $T^*$ that gives the **minimum** AMBE
3. Split histogram at $T^*$, then apply BBHE-style equalization to each half

- ⚠️ **Drawback**: Computationally intensive due to recursive AMBE calculation → higher computation time

---

## 6. Performance Metrics (How to Judge Quality)

| Metric | Full Name | What it Measures | Better = |
|:---|:---|:---|:---|
| **BRISQUE** | Blind/Referenceless Image Spatial Quality Evaluator | Perceptual quality without needing a reference image. Range: [0, 150] | **Lower** |
| **EDES** | Edge Detection Entropy Score | Information richness in edges (Canny + Shannon Entropy). Higher = more detail | **Higher** |
| **PSNR** | Peak Signal-to-Noise Ratio | Quality of reproduced image vs original | **Higher** |
| **Computation Time** | — | Time taken to run the algorithm (seconds) | **Lower** |

> BRISQUE is preferred for **real-time** applications because it needs no reference image and has low computational complexity.

---

## 7. Experimental Results

**Dataset**: FLIR public thermal dataset — tested on 30 images.

### Results for Image 1

| Method | BRISQUE ↓ | PSNR ↑ | EDES ↑ | Time (s) |
|:---|:---|:---|:---|:---|
| Original | 152.46 | — | 0.278 | — |
| GHE | 26.95 | 29.14 | 0.309 | 0.725 |
| AHE | 146.84 | 28.01 | 0.321 | 0.748 |
| CLAHE | 143.99 | 28.26 | 0.282 | 0.761 |
| BBHE | 26.12 | 27.92 | 0.329 | 0.768 |
| PLHE | 48.12 | 27.90 | 0.292 | 0.759 |
| **MMBEBHE** | **26.09** | **29.50** | **0.332** | 0.817 |

### Results for Image 2

| Method | BRISQUE ↓ | PSNR ↑ | EDES ↑ | Time (s) |
|:---|:---|:---|:---|:---|
| Original | 139.04 | — | 0.064 | — |
| GHE | 41.21 | 28.38 | 0.070 | 0.335 |
| AHE | 170.59 | 28.85 | 0.070 | 0.369 |
| CLAHE | 142.24 | 28.06 | 0.334 | 0.404 |
| BBHE | 40.20 | 35.93 | 0.066 | 0.397 |
| PLHE | 40.20 | 35.67 | 0.070 | 0.384 |
| **MMBEBHE** | **40.08** | 35.52 | **0.071** | 0.432 |

### Average Across 30 Images

| Method | BRISQUE ↓ | PSNR ↑ | EDES ↑ | Time (s) |
|:---|:---|:---|:---|:---|
| GHE | 42.759 | 28.498 | 0.179 | 0.418 |
| AHE | 145.058 | 28.153 | 0.181 | 0.453 |
| CLAHE | 135.529 | 28.384 | 0.312 | 0.473 |
| PLHE | 46.871 | 29.463 | 0.181 | 0.449 |
| BBHE | 42.561 | 29.679 | 0.182 | 0.459 |
| **MMBEBHE** | **42.251** | **30.132** | **0.189** | 0.486 |

---

## 8. Resulting Histogram Characteristics

The equalized histogram has two key traits:

1. **Spread** — Intensity levels are no longer clumped together. Dark values spread across the full range.
2. **Gaps** — Because we map discrete integers to another discrete set, some levels have zero pixels. This can cause **grainy artifacts** or **false contouring**.

---

## 9. Ranking of Techniques (Best → Worst by Perceptual Quality)

$$\text{MMBEBHE} > \text{BBHE} > \text{PLHE} > \text{CLAHE} > \text{AHE}$$

> GHE is excluded from this ranking because it operates globally and isn't directly comparable in the same local-contrast context.

---

## 10. Key Conclusions

| Finding | Detail |
|:---|:---|
| 🏆 Best overall method | **MMBEBHE** — lowest BRISQUE (42.25), highest EDES (0.189), best PSNR (30.13) |
| ⏱️ Speed trade-off | MMBEBHE is slowest due to recursive AMBE calculation |
| 🩺 Best for local contrast | CLAHE — especially useful for uneven lighting |
| 🔄 Real-time friendly | BRISQUE metric itself is fast; MMBEBHE algorithm is slower |
| 🔮 Future work | Hardware realization of MMBEBHE + super-resolution on thermal images |

---

## 11. Implementation Notes

### Lookup Table (LUT)
In practice, the mapping is computed **once** for all intensity levels, stored in a LUT, then applied to every pixel — making it efficient.

### Verification Checkpoints (for manual calculations)
When solving problems by hand, always verify:
1. **Monotonicity** — $s_k$ must never decrease as $r_k$ increases
2. **Range** — Final $s_{max}$ must equal $L-1$
3. **Pixel Conservation** — Total pixel count must remain constant before and after

---

## Quick Reference Cheat Sheet
```
GHE    → Simple, global, fast. Bad: boosts noise.
AHE    → Local tiles. Good for uneven lighting. Bad: amplifies noise.
CLAHE  → AHE + clip limit. Avoids artifacts. Great for medical images.
BBHE   → Splits at mean. Preserves brightness. Needs symmetric histogram.
PLHE   → Power-log for IR plateau regions. Balanced enhancement.
MMBEBHE → Splits at optimal threshold. Best quality. Slowest.
```
