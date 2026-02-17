# Digital Image Processing Fundamentals
Notes based on **Chapter 2** of Gonzalez and Woods: *Digital Image Fundamentals*.

## 1. Fundamental Steps in Digital Image Processing

Digital Image Processing (DIP) involves several stages to convert an image into a form suitable for specific applications. The workflow typically follows this hierarchy:

1.  **Image Acquisition**: The first step is capturing the image using a sensor (e.g., a camera). It involves digitizing a continuous signal into a discrete format.
2.  **Image Enhancement**: The process of manipulating an image so that the result is *more suitable* than the original for a specific application (e.g., adjusting contrast or brightness).
3.  **Image Restoration**: Unlike enhancement, which is subjective, restoration is **objective** and aims to improve an image based on mathematical or probabilistic models of image degradation (e.g., removing blur or noise).
4.  **Color Image Processing**: Deals with color models and processing in the color domain, which is vital due to the increased use of digital images over the internet.
5.  **Wavelets and Multiresolution Processing**: Represents images in various degrees of resolution, useful for data compression and pyramidal representation.
6.  **Compression**: Reducing the storage required to save an image or the bandwidth required to transmit it.
7.  **Morphological Processing**: Deals with tools for extracting image components that are useful in the representation and description of shape (e.g., skeletons, convex hulls).
8.  **Segmentation**: The process of partitioning an image into its constituent parts or objects (e.g., finding a person in a crowd).
9.  **Representation and Description**: Follows segmentation; it transforms raw data into a form suitable for computer processing (e.g., describing a shape by its boundary or regional properties).
10. **Object Recognition**: Assigning a label to an object based on its descriptors (e.g., identifying an object as a "vehicle").

### The Image Processing System
To perform these tasks, a specialized "general purpose" system is required, consisting of:
*   **Image Sensors**: These sit at the front, interacting with the "Problem Domain" to capture light or energy.
*   **Specialized Hardware**: Often dedicated boards for high-speed processing.
*   **Computer & Software**: The brain and the instructions that run the algorithms.
*   **Storage**: "Mass storage" is critical because images are data-hungry.
*   **Output**: This includes image displays for viewing and hardcopy devices (printers) for physical results.

---

## 2. Elements of Visual Perception: The Human Eye
Why study the eye in a computer science context? Because the goal is often to create images for humans to view. We need to understand the "receiver" of the data.

### Physical Components
The eye is a complex biological camera with several key components:
*   **Cornea and Sclera**: The tough, transparent outer cover and the white of the eye.
*   **Choroid**: A layer that contains blood vessels and helps absorb stray light.
*   **Iris**: Acts like a camera's aperture, contracting and expanding to control light entry through the pupil. It can vary the pupil diameter from approximately **2 mm to 8 mm** to adapt to lighting conditions.
*   **Lens**: Unlike a glass camera lens, the human lens is flexible. It changes shape (controlled by ciliary muscles) to focus.
*   **Retina**: The innermost membrane where the actual "imaging" happens.

![Structure of Human Eye](assets/structure%20of%20human%20eye.jpeg)

### Light Receptors
The retina is covered in two distinct classes of receptors: **Cones** and **Rods**.

**1. Cones (Photopic Vision)**
*   There are approximately **6–7 million** per eye.
*   Primarily located in the **fovea**, the center of the retina.
*   Highly sensitive to **color** and **fine detail**.
*   Known as "bright-light vision".

**2. Rods (Scotopic Vision)**
*   There are far more rods—between **75 and 150 million**—distributed over the retinal surface.
*   Sensitive to **low levels of illumination**.
*   **Not involved in color vision**.
*   Known as "dim-light vision".

**The Blind Spot**: The region where the optic nerve leaves the eye. Because there are no receptors there, you effectively cannot see light that hits that specific spot.

### Resolving Power
The eye's "resolving power" is comparable to modern electronic sensors. The fovea is roughly a square sensor array of $1.5 \text{ mm} \times 1.5 \text{ mm}$. With a density of 150,000 elements/mm², the fovea has about **337,000 elements**. A modern Charge-Coupled Device (CCD) can match this resolving power in a space no larger than $5 \text{ mm} \times 5 \text{ mm}$.

### Brightness Adaptation and Discrimination
The Human Visual System (HVS) is incredibly flexible. It can adapt to an enormous range of light intensity levels—roughly on the order of $10^{10}$. However, the eye cannot see this entire range *simultaneously*.

*   **Brightness Adaptation Level**: At any given moment, the eye operates at a specific "current sensitivity".
*   **Subjective Brightness**: Our perception of brightness is a **logarithmic function** of the light intensity incident on the eye.
*   **Simultaneous Range**: At any given adaptation level, the range of intensities the eye can discriminate is actually quite small compared to the total $10^{10}$ range.

![Brightness Adaptation](assets/brightness%20adaptation%20and%20discrimination%20graph.png)

#### The Weber Ratio
To measure how well the eye can detect changes in light, researchers use the **Weber Ratio** ($\Delta I_c / I$), where $\Delta I_c$ is the increment of illumination discriminable 50% of the time against a background $I$.
*   **Small Weber Ratio**: Means **good** brightness discrimination; a small percentage change in intensity is easily noticed (occurs at high illumination/cones).
*   **Large Weber Ratio**: Means **poor** brightness discrimination; a large percentage change is required before the eye notices a difference (occurs at low illumination/rods).

**Crucial Observation**: In a typical monochrome image, a person can typically see only about **1 to 2 dozen different intensities** at any single point in time.

### Visual Perception Phenomena
Our eyes don't just act like light meters; they interpret data in ways that can be "tricked."
*   **Mach Bands**: Our visual system tends to undershoot or overshoot around the boundaries of regions with different intensities. This makes the edges look sharper than they actually are.
*   **Simultaneous Contrast**: A region's perceived brightness depends heavily on its background. A gray square looks much darker against a white background than it does against a black one.
*   **Optical Illusions**: The eye often "fills in" non-existing information or wrongly perceives geometrical properties (e.g., misjudging parallel lines).

![Mach Band Effect](assets/mach%20band%20effect.png)
![Simultaneous Contrast](assets/Simultaneous%20Contrast.png)
![Optical Illusions](assets/Some%20Optical%20Illusions.png)

---

## 3. Light and the Electromagnetic Spectrum
Visible light is just a tiny sliver of the much broader **Electromagnetic (EM) Spectrum**. This spectrum ranges from high-energy gamma rays with incredibly short wavelengths to low-energy radio waves with very long wavelengths.

### Key Relationships
*   **Wavelength ($\lambda$)**: Measured in meters, microns ($\mu m$), or nanometers (nm).
*   **Frequency ($\nu$)**: Measured in Hertz (Hz).
*   **Relationship**: Wavelength and frequency are inversely related by the speed of light ($c \approx 2.998 \times 10^8 \text{ m/s}$):
$$ \lambda = \frac{c}{\nu} $$
*   **Energy ($E$)**: The energy of a photon is proportional to its frequency: $E = h\nu$, where $h$ is Planck's constant.

### The Visible Spectrum
The portion of the spectrum that humans can actually see spans from approximately **0.43 µm (violet)** to **0.79 µm (red)**.
*   **Violet**: $\sim 400 \text{ nm}$
*   **Green**: $\sim 500 \text{ nm}$
*   **Yellow**: $\sim 600 \text{ nm}$
*   **Red**: $\sim 700 \text{ nm}$

![EM Spectrum](assets/em%20spectrum.png)
![Visible Light](assets/visible%20light.png)

### Key Light Properties
*   **Monochromatic (Achromatic) Light**: Light "void of color," having only one attribute: **intensity** or gray level. The range of these measured values is called the **gray scale**.
*   **Chromatic Light**: Light with color, characterized by three factors:
    1.  **Radiance**: The total amount of energy (in Watts, W) flowing from the light source.
    2.  **Luminance**: A measure of how much energy an observer *perceives* from the source, measured in lumens (lm).
    3.  **Brightness**: A purely **subjective** descriptor of light perception. It is practically impossible to measure directly because it depends on the human viewer.

---

## 4. Image Sensing and Acquisition
How do we turn EM energy into a digital image? It requires a physical process of **Combination** (illumination source + reflection/absorption), **Transformation** (sensor converts energy to voltage), and **Digitization**.

### Image Formation Model
We represent images as a two-dimensional function $f(x,y)$, where the value at any spatial coordinate is the positive scalar **intensity**.
$$ f(x,y) = i(x,y) \cdot r(x,y) $$
*   **Illumination ($i$)**: The range is $0 < i(x,y) < \infty$.
*   **Reflectance ($r$)**: The range is $0 < r(x,y) < 1$. A value of 0 indicates total absorption (black), while 1 indicates total reflectance.
    *   *Examples*: Black velvet $\approx 0.01$, Stainless steel $\approx 0.65$, Snow $\approx 0.93$.

### Types of Imaging Sensors
Sensors are typically made of silicon (e.g., photodiodes) with filters for selectivity.
1.  **Single Sensor**: Combined with mechanical motion (like a lead screw) to scan a 2-D area. Inexpensive but slow.
2.  **Sensor Strips**: A line of sensors captures one image line at a time. Common in flatbed scanners and airborne imaging.
3.  **Sensor Arrays**: The most prevalent arrangement for digital cameras. An entire 2-D grid of sensors captures the scene simultaneously.

![Single Sensor](assets/Image%20Acquisition%20using%20Single%20Sensor.png)
![Sensor Strips](assets/Image%20Acquisition%20using%20Sensor%20Strips.png)
![Sensor Arrays](assets/Image%20Acquisition%20using%20Sensor%20Arrays.png)

### CCD vs. CMOS Technology
Most modern digital cameras use one of two competing sensor array technologies:

| Feature | CCD (Charge-Coupled Device) | CMOS (Complementary Metal-Oxide) |
| :--- | :--- | :--- |
| **Readout** | One row at a time, transferred to a serial output register. | Active pixel sensors that can provide data faster. |
| **Power** | Higher power consumption. | Uses less power. |
| **Size** | Generally larger system footprint. | Can be significantly smaller. |
| **Maturity** | More mature technology with high quality. | Integrated with fewer components. |

![CCD vs CMOS](assets/ccd%20vs%20cmos%20image%20sensor.png)

### Sampling and Quantization
To process the continuous image $f(x,y)$ on a computer, it must be digitized:
1.  **Sampling**: Discretizing the **spatial coordinates** (the $x$ and $y$ values). The number of sensors in an array usually determines the sampling limits.
2.  **Quantization**: Discretizing the **amplitude values** (the intensity or gray levels).

---

## 5. Digital Image Representation
After sampling and quantization, the image is represented as a discrete 2-D array $f(x,y)$ with $M$ rows and $N$ columns.

### Matrix Representation
For computational efficiency, we write the image as a matrix:
$$
f(x,y) = \begin{bmatrix}
f(0,0) & f(0,1) & \cdots & f(0,N-1) \\
f(1,0) & f(1,1) & \cdots & f(1,N-1) \\
\vdots & \vdots & \ddots & \vdots \\
f(M-1,0) & f(M-1,1) & \cdots & f(M-1,N-1)
\end{bmatrix}
$$

### Intensity Levels and Storage
*   The number of discrete intensity levels, $L$, is typically a power of 2: $L = 2^k$.
*   **Dynamic Range**: The ratio of the maximum to minimum detectable intensity. Low dynamic range causes a "washed-out" look.
*   **Storage Requirements**: The number of bits ($b$) to store an image is:
$$ b = M \times N \times k $$
    *   An image with $2^8 = 256$ intensity values is called an **8-bit image**.

![Storage Bits](assets/Storage%20bits%20for%20Various%20Values%20of%20N%20and%20K.png)

### Resolution
*   **Spatial Resolution**: The smallest discernible detail in an image, measured in line pairs per unit distance or dots per inch (dpi).
*   **Intensity Resolution**: The smallest discernible change in intensity level.
*   **False Contouring**: A visual artifact in smooth areas caused by insufficient intensity levels (quantization noise).

---

## 6. Image Interpolation
Interpolation is the process of using known data to **estimate values at unknown locations**. It is essential for zooming, shrinking, rotating, and geometric corrections.

1.  **Nearest Neighbor Interpolation**: Assigns the intensity of the *closest* pixel to the new location.
    *   *Pros*: Simple and fast.
    *   *Cons*: Strong tendency to produce artifacts like "jagged" edges.
2.  **Bilinear Interpolation**: Uses the **four nearest neighbors** to estimate intensity.
    *   *Model*: $v(x,y) = ax + by + cxy + d$
    *   *Result*: Much smoother than nearest neighbor.
3.  **Bicubic Interpolation**: Uses the **16 nearest neighbors**.
    *   *Model*: $v(x,y) = \sum_{i=0}^3 \sum_{j=0}^3 a_{ij} x^i y^j$
    *   *Result*: The standard for high-quality commercial image editing; sharper and smoother than bilinear.

---

## 7. Relationships Between Pixels
To develop algorithms, we define how pixels relate to one another.

### Neighbors of a Pixel $p(x,y)$
1.  **4-neighbors ($N_4(p)$)**: Horizontal and vertical neighbors: $(x+1,y), (x-1,y), (x,y+1), (x,y-1)$.
2.  **Diagonal neighbors ($N_D(p)$)**: The four diagonal neighbors: $(x+1,y+1), (x+1,y-1), (x-1,y+1), (x-1,y-1)$.
3.  **8-neighbors ($N_8(p)$)**: The union of $N_4(p)$ and $N_D(p)$.

### Adjacency
Two pixels are adjacent if they are neighbors and their intensity levels satisfy a specific similarity criterion.
*   **m-adjacency (mixed adjacency)**: A specialized type used to eliminate **multiple-path ambiguities** found in 8-adjacency. Two pixels $p$ and $q$ are m-adjacent if:
    *   They are 4-adjacent, OR
    *   They are diagonally adjacent AND their 4-neighbor sets do not intersect.

### Distance Function
A **distance function** is a function of two points, \( p \) and \( q \), in space that satisfies three criteria:
1. **Non-negativity**  
   \( D(p, q) \geq 0 \)
2. **Symmetry**  
   \( D(p, q) = D(q, p) \)
3. **Triangle Inequality**  
   \( D(p, z) \leq D(p, q) + D(q, z) \)

### Distance Measures
For pixels $p(x,y)$ and $q(s,t)$:
*   **Euclidean Distance ($D_e$)**: The "straight-line" distance (circular radius).
$$ D_e(p,q) = \sqrt{(x-s)^2 + (y-t)^2} $$
*   **City-Block (Manhattan) Distance ($D_4$)**: Measured along horizontal and vertical paths (diamond shape).
$$ D_4(p,q) = |x-s| + |y-t| $$
*   **Chessboard Distance ($D_8$)**: The maximum difference between coordinates (square shape).
$$ D_8(p,q) = \max(|x-s|, |y-t|) $$

### Basic Definitions
*   **Path**: A sequence of distinct pixels where each consecutive pair is adjacent.
*   **Connected Component**: All pixels in a subset connected to a given pixel.
*   **Edge**: A path of pixels that separates two regions with significantly different gray levels.


---

---

## 8. Arithmetic Operations

Arithmetic operations are performed on a pixel-by-pixel basis between two images of the same size.

*   **Addition**: Used for **Image Averaging**. By averaging different noisy images of the same scene, we can significantly reduce random noise. This is a fundamental technique in fields like **astronomy**.
*   **Subtraction**: Used for **Image Subtraction** to enhance the differences between two images. A common application is **Mask Mode Radiography**, where a "mask" image is subtracted from a live image to highlight specific changes, such as blood flow in an angiogram.
*   **Multiplication and Division**: Often used for **Shading Correction**. For instance, if an image has uneven lighting (shading), multiplying the original by the reciprocal of the shading pattern produces a corrected, uniform result. Multiplication is also used for **Masking** (Region of Interest or ROI), where a binary mask (1s for object, 0s for background) is multiplied with an image to isolate specific parts (e.g., teeth in a dental X-ray).

---

## 9. Set and Logical Operations

These operations treat images—particularly binary images—as sets of pixel coordinates.

*   **Set Operations**: Include **Union**, **Intersection**, **Complement**, and **Difference**.
    *   For gray-scale images, the **Union** is formed by taking the maximum intensity at each corresponding pixel location.
*   **Logical Operations**: Operations like **AND**, **OR**, **NOT**, **XOR**, and **AND-NOT** are applied to binary images where white pixels represent 1s (foreground) and black pixels represent 0s (background).
    *   A **NOT** operation on a gray-scale image produces its **Negative**.

---

## 10. Spatial Operations

Spatial operations modify pixel values based on their location or their relationship to nearby pixels.

*   **Single-Pixel Operations**: Alter a pixel's value based *only* on its current intensity, such as applying a transformation function to obtain an image negative.
*   **Neighborhood Operations**: The value of a pixel in the output is determined by the values of pixels in a neighborhood around the corresponding input pixel. A classic example is **Local Averaging**, where each pixel is replaced by the average of its neighbors to "blur" or smooth the image.
*   **Geometric Spatial Transformations**: Often called **"rubber-sheet" transformations**, consisting of two steps:
    1.  **Spatial Transformation of Coordinates**: Moving pixels to new locations using **Affine Transforms** (scaling, rotation, translation, shearing).
    2.  **Intensity Interpolation**: Assigning intensity values to those new locations using interpolation methods (Nearest Neighbor, Bilinear, Bicubic).

---

## 11. Image Registration

Image registration is a critical process used to align two or more images of the same scene taken at different times, from different viewpoints, or by different sensors.

*   **The Problem**: Often, you have an "input" image and a "reference" image, but the specific geometric transformation between them is unknown.
*   **The Solution**: We estimate a transformation function to "register" the input against the reference.
*   **Tie Points (Control Points)**: The most common approach. You identify corresponding points in both images whose locations are known precisely—like the corners of a building or a specific road intersection.

---

## 12. Vector and Matrix Operations

In many advanced applications, we don't just look at a single pixel value; we treat a pixel as part of a vector.

*   **RGB and Multispectral Images**: For a standard color image, each pixel is actually a vector of three values: Red, Green, and Blue.
*   **Vector Formation**: A vector **z** is formed by stacking the corresponding pixel values from the different color components (Component 1 = Red, Component 2 = Green, Component 3 = Blue).

---

## 13. Image Transforms

Sometimes, a task that is very difficult to do in the standard spatial domain (the actual $x, y$ coordinates) becomes much easier in a different mathematical "space" called the **Linear Transform Domain**.

The general approach follows these steps:
1.  **Transform**: Convert the spatial domain image $f(x, y)$ into a transform domain $T(u, v)$.
2.  **Operation**: Perform the specific image processing task (Operation $R$) within this new domain.
3.  **Inverse Transform**: Apply the inverse transform to bring the data back into the spatial domain to get the final processed image $g(x, y)$.


---

## 14. Image Domains

Image processing techniques are broadly divided into two domains:

1.  **The Spatial Domain**: Operates directly on the pixels of an image.
    *   **Point Processing**: Operations on a single pixel (e.g., contrast adjustment).
    *   **Neighborhood Processing**: Operations that consider a group of pixels (e.g., blurring).
    *   **Pipeline**: Expressed as $g(x,y) = T[f(x,y)]$, where $f$ is the input, $g$ is the output, and $T$ is the transformation.
2.  **The Transform Domain**: Uses mathematical transforms (like Fourier) to convert the image into coefficients representing "frequencies". These are modified and then inversely transformed to create the output.

### Neighborhood Processing in Spatial Domain
To generate an output pixel at $(x,y)$, a **neighborhood** (e.g., $3 \times 3$ grid) is defined around the corresponding input pixel. The transformation $T$ is applied to the pixels in this window to calculate the new value.

---

## 15. Basic Intensity Transformation Functions (Point Processing)

The simplest spatial domain operations where transformation $T$ depends only on the intensity of a single pixel: $s = T(r)$, where $r$ is input intensity and $s$ is output intensity.

1.  **Image Negatives**: Flips the intensity scale ($s = L-1-r$).
    *   **Application**: Enhancing white/gray details embedded in dark regions (e.g., mammograms).
2.  **Log Transformations**: $s = c \log(1+r)$.
    *   **Function**: Maps a narrow range of low-intensity inputs to a wider set of output levels.
    *   **Application**: Visualizing the **Fourier Transform**, expanding dark pixels to reveal structural detail.
3.  **Power-Law (Gamma) Transformations**: $s = c r^\gamma$.
    *   **$\gamma < 1$**: Expands dark regions, compresses bright regions.
    *   **$\gamma > 1$**: Compresses dark regions, expands bright regions.
    *   **$\gamma = 1$**: Identity transformation.
    *   **Application (Gamma Correction)**: Correcting the power-law response of display devices (e.g., CRT monitors) to ensure images appear naturally to the human eye.

![Gamma Correction](assets/gamma%20correction.png)

---

## 16. Piecewise-Linear Transformation Functions

Transformations made of linear segments connected at specific points, allowing surgical control over intensities.

1.  **Contrast Stretching**: Expands the range of intensity levels in a low-contrast image to span the full scale (black to white).
    *   **Thresholding**: An extreme case where the function becomes a vertical line, converting a grayscale image to binary (black and white).

![Contrast Stretching](assets/contrast%20streching.png)
2.  **Intensity-Level Slicing**: Highlights a specific range of gray levels $[A, B]$.
    *   **Binary Slicing**: Sets pixels in range to white, others to black.
    *   **Non-destructive**: Highlights range but preserves other gray levels.
3.  **Bit-Plane Slicing**: analyzing an 8-bit image as 8 separate "bit-planes".
    *   **Bit-Plane 8 (MSB)**: Contains structural information.
    *   **Bit-Plane 1 (LSB)**: Contains fine detail/noise.
    *   **Applications**: Image reconstruction (compression) and data hiding (watermarking).

![Bit Plane Slicing Example](assets/bit%20plane%20slicing%20-%20example.png)
![Bit Plane Slicing Working](assets/bit%20plane%20slicing%20-%20working.png)

---

## 17. Histogram Processing

Analysis based on the statistical distribution of pixel intensities. A **histogram** is a discrete function $h(r_k) = n_k$, where $n_k$ is the count of pixels with intensity $r_k$.

### Histogram Analysis
*   **Dark Image**: Components concentrated on the low (left) side.
*   **Light Image**: Biased toward the high (right) side.
*   **Low-Contrast**: Narrow, centered distribution ("gray" look).
*   **High-Contrast**: Broad, uniform distribution covering the full scale.

![Image Histogram](assets/image%20histogram.png)
![Histogram Processing](assets/histogram%20processing.png)

### Techniques
1.  **Histogram Equalization**: An automatic method to "stretch" the histogram to be uniform.
    *   **Formula**: $s_k = T(r_k) = (L-1) \sum_{j=0}^{k} p_r(r_j)$
    *   **Result**: Increases dynamic range and contrast, revealing hidden details.
2.  **Histogram Matching (Specification)**: Modifying an image so its histogram matches a specific specified shape (not necessarily uniform).

---

## 18. Spatial Filtering (Neighborhood Processing)

A pixel's new value is determined by its neighboring pixels using a **Filter** (Mask/Kernel).

### Mechanics
*   **Alignment**: Center of filter placed over pixel $(x,y)$.
*   **Calculation**: Mathematical operation using filter coefficients and covered pixels.

### Linear Spatial Filtering
A weighted average of the neighborhood (sum-of-products):
$$ g(x,y) = \sum_{s=-a}^{a} \sum_{t=-b}^{b} w(s,t)f(x+s, y+t) $$

### Correlation vs. Convolution
*   **Correlation**: Sliding the mask and calculating the sum of products.
*   **Convolution**: Rotating the filter by 180° *before* sliding. (Same result if filter is symmetric).

![Correlation](assets/correlation%20of%202d%20filter.png)
![Convolution](assets/CONVOLUTION%20of%202d%20filter.png)

### Edge Handling
When filters hang off the image edge:
*   **Ignore edges**: Output image is smaller.
*   **Padding**: Add rows/columns of zeros (or other values) around the border.



---

## 19. Smoothing Spatial Filters

Smoothing (Lowpass) Filters are used primarily for blurring and noise reduction.

### The Purpose of Blurring
*   **Object Extraction**: Removes small details before extracting large objects.
*   **Bridging Gaps**: Connects small gaps in lines or curves.
*   **Noise Reduction**: Smooths out sharp transitions (often random noise).
*   **Reducing False Contouring**: Blends transitions in images with few gray levels.

### 1. Linear Smoothing Filters (Averaging)
Replaces every pixel with the average intensity of its neighborhood.
*   **Box Filter**: All coefficients have the same value (e.g., 1), multiplied by a normalizing constant.
*   **Weighted Average Filter**: Center pixels are given higher weight to reduce edge blurring.

### 2. Non-Linear Smoothing Filters (Order-Statistics)
Based on ranking pixels in the neighborhood rather than mathematical sums.
*   **Median Filter**: Replaces the center pixel with the *median* value. Excellent for removing "salt-and-pepper" noise while preserving edges.
*   **Max Filter**: Replaces center with the brightest value (finds bright points).
*   **Min Filter**: Replaces center with the darkest value.

---

## 20. Sharpening Spatial Filters

Sharpening (Highpass) Filters highlight transitions in intensity (edges, noise, fine detail) using **spatial differentiation**.

### The Role of Derivatives
*   **First Derivative**: $\frac{\partial f}{\partial x} = f(x+1) - f(x)$. Non-zero at the onset of a step/ramp.
*   **Second Derivative**: $\frac{\partial^2 f}{\partial x^2} = f(x+1) + f(x-1) - 2f(x)$. Non-zero at the start and end of a step/ramp; zero along the ramp.
*   **Key**: The second derivative enhances fine detail much better than the first.

### 1. The Laplacian (Second-Order Derivative)
An isotropic (rotation invariant) operator.
*   **Formula**: $\nabla^2 f(x,y) = f(x+1,y) + f(x-1,y) + f(x,y+1) + f(x,y-1) - 4f(x,y)$
*   **Sharpening**: Subtract (or add) the Laplacian from the original image:
$$ g(x,y) = f(x,y) + c[\nabla^2 f(x,y)] $$

### 2. Unsharp Masking and Highboost Filtering
Sharpening using a blurred mask.
1.  **Blur** the original image ($f^*$).
2.  **Create Mask**: Subtract blurred image from original: $g_{mask}(x,y) = f(x,y) - f^*(x,y)$.
3.  **Add Mask**: $g(x,y) = f(x,y) + k \cdot g_{mask}(x,y)$.
    *   **$k=1$**: Unsharp Masking.
    *   **$k>1$**: Highboost Filtering.

### 3. The Gradient (First-Order Derivative)
Points in the direction of the greatest rate of change. We typically use the **Gradient Magnitude**:
$$ M(x,y) \approx |g_x| + |g_y| $$
*   **Roberts Cross-Gradient**: Uses $2 \times 2$ masks.
*   **Sobel Operators**: Uses $3 \times 3$ masks (provides smoothing + derivative).

---

## 21. Introduction to the Frequency Domain

Based on the Fourier Transform (FT), which represents any periodic function as a weighted sum of sines and cosines.

### The "New Neighborhood"
*   **Spatial Domain ($f$)**: Intensity at specific $(x,y)$ locations.
*   **Frequency Domain ($F$)**: The rate at which intensities change across the image.

### Advantages
1.  **Removing Periodic Noise**: Distinct noise patterns appear as "bursts" in the frequency domain, which can be easily masked out.
2.  **Computational Efficiency**: Convolution in the spatial domain is equivalent to simple **multiplication** in the frequency domain (Convolution Theorem).

---

## 22. Sampling and the Nyquist Rate

To use FT on computers, continuous signals must be **sampled** at intervals $\Delta T$.

### The Sampling Theorem
*   **Band-limited Functions**: Fourier transform is zero outside $[-\mu_{max}, \mu_{max}]$.
*   **The Nyquist Rate**: To perfectly recover a signal, the sampling rate must be more than twice the highest frequency:
$$ \frac{1}{\Delta T} > 2\mu_{max} $$

### Aliasing
The consequence of under-sampling (sampling slower than Nyquist rate). High-frequency components "fold over" and appear as lower frequencies, causing artifacts like **Moiré patterns** or jagged edges.



