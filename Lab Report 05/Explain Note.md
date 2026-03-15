
---

# Digital Image Processing & Computer Vision

# Complete Practical Study Notes (Lab 05)

---

# 1. Introduction to Digital Image Processing

Digital Image Processing (DIP) is the use of algorithms and computer techniques to manipulate and analyze digital images.

A **digital image** is a 2D matrix composed of **pixels**.

Each pixel contains an intensity value.

For grayscale images:

* Pixel values range from **0 – 255**
* 0 → Black
* 255 → White

Digital Image Processing is widely used in:

* Medical imaging
* Autonomous vehicles
* Facial recognition
* Object detection
* Security systems

Computer Vision systems rely heavily on image processing techniques for **feature extraction and enhancement**.

**Reference:**
Gonzalez & Woods, *Digital Image Processing*, Pearson.

---

# 2. Image Filtering

Image filtering is a technique used to modify or enhance an image.

Filters are applied using a **kernel (convolution matrix)**.

The kernel moves across the image and calculates new pixel values.

Filtering is mainly used for:

* Noise reduction
* Edge detection
* Image smoothing
* Feature extraction

Two main categories:

### 1. Linear Filters

Use convolution and weighted averages.

Examples:

* Mean filter
* Gaussian filter
* Sobel filter

### 2. Non-Linear Filters

Use nonlinear operations.

Example:

* Median filter

**Reference:**
OpenCV Documentation – Image Filtering.

---

# 3. Mean Filter (Average Filter)

## Concept

The **Mean Filter** is a smoothing filter that replaces each pixel value with the **average of neighboring pixels**.

It reduces image noise but may blur edges.

This filter is a **linear filter**.

---

## Mean Filter Kernel Example

3×3 Kernel

[
\frac{1}{9}
\begin{bmatrix}
1 & 1 & 1 \
1 & 1 & 1 \
1 & 1 & 1
\end{bmatrix}
]

Each pixel becomes the **average of the surrounding pixels**.

---

## Algorithm Steps

1. Select kernel size (3×3, 5×5, etc.)
2. Move kernel over image
3. Multiply kernel values with pixel values
4. Compute average
5. Replace center pixel with the result

---

## Applications

* Removing random noise
* Preprocessing images
* Image smoothing

---

## Advantages

* Simple
* Easy to implement

## Disadvantages

* Blurs edges
* Removes image details

---

# 4. Gaussian Filter

## Concept

The **Gaussian Filter** smooths an image using a **Gaussian distribution function**.

Pixels closer to the center get higher weight.

This preserves edges better than the mean filter.

Gaussian filters are widely used in:

* Computer vision
* Image preprocessing
* Noise reduction

---

## Gaussian Function

The 2D Gaussian equation:

[
G(x,y)=\frac{1}{2\pi\sigma^2}e^{-\frac{x^2+y^2}{2\sigma^2}}
]

Where:

* (x,y) = pixel position
* (\sigma) = standard deviation

Higher σ → more smoothing.

**Reference:**
OpenCV GaussianBlur documentation.

---

## Gaussian Kernel Example

[
\begin{bmatrix}
1 & 2 & 1 \
2 & 4 & 2 \
1 & 2 & 1
\end{bmatrix}
]

Normalized by dividing by 16.

---

## Applications

* Image smoothing
* Noise removal
* Preprocessing for edge detection
* Blur effects

---

## Advantages

* Reduces noise effectively
* Preserves edges better than mean filter

## Disadvantages

* Slight image blur still occurs

---

# 5. Sobel Filter

## Concept

The **Sobel Filter** is used for **edge detection**.

Edges represent sharp intensity changes in images.

The Sobel operator calculates **image gradients**.

It detects edges in:

* Horizontal direction
* Vertical direction

---

## Sobel Kernels

### Horizontal Gradient (Gx)

[
\begin{bmatrix}
-1 & 0 & 1 \
-2 & 0 & 2 \
-1 & 0 & 1
\end{bmatrix}
]

### Vertical Gradient (Gy)

[
\begin{bmatrix}
-1 & -2 & -1 \
0 & 0 & 0 \
1 & 2 & 1
\end{bmatrix}
]

---

## Gradient Magnitude

Edge strength is calculated as:

[
G = \sqrt{G_x^2 + G_y^2}
]

Where:

* (G_x) = horizontal gradient
* (G_y) = vertical gradient

---

## Applications

* Object detection
* Feature extraction
* Boundary detection
* Computer vision preprocessing

---

## Advantages

* Detects edges clearly
* Highlights boundaries

## Disadvantages

* Sensitive to noise

---

# 6. Median Filter

## Concept

The **Median Filter** is a **non-linear filter** used to remove noise from images.

Instead of averaging pixels, it replaces the center pixel with the **median value of neighboring pixels**.

This is very effective for **salt-and-pepper noise**.

---

## Example

Pixel values in a 3×3 region:

```
120 125 130
110 255 128
122 126 124
```

Sorted values:

```
110 120 122 124 125 126 128 130 255
```

Median value = **125**

The center pixel becomes **125**.

---

## Applications

* Removing impulse noise
* Medical imaging
* Image restoration

---

## Advantages

* Preserves edges
* Removes salt-and-pepper noise

## Disadvantages

* Slower than linear filters

---

# 7. Padding in Image Processing

When filters are applied at image borders, pixels outside the image are required.

This problem is solved using **padding**.

Common padding methods:

### Zero Padding

Border pixels are filled with **0**.

### Replication Padding

Border pixels are repeated.

### Reflection Padding

Border is mirrored.

In OpenCV:

```
cv2.copyMakeBorder()
```

is used to pad images.

---

# 8. Kernel (Convolution Matrix)

A **kernel** is a small matrix used to perform convolution.

Example:

3×3 Kernel

```
[ a b c
  d e f
  g h i ]
```

The kernel slides over the image and performs **convolution operations**.

Convolution formula:

[
I'(x,y)=\sum_{i=-k}^{k}\sum_{j=-k}^{k}I(x+i,y+j)K(i,j)
]

Where:

* (I) = original image
* (K) = kernel
* (I') = filtered image

**Reference:**
Szeliski, *Computer Vision: Algorithms and Applications.*

---

# 9. Libraries Used in the Practical

### OpenCV (cv2)

Open Source Computer Vision Library.

Used for:

* Image reading
* Image processing
* Filtering
* Edge detection

Documentation:
[https://opencv.org](https://opencv.org)

---

### NumPy

Used for:

* Matrix operations
* Numerical computation
* Kernel creation

Documentation:
[https://numpy.org](https://numpy.org)

---

# 10. Typical Practical Workflow

In this lab, the workflow is usually:

1. Import libraries
2. Load image
3. Convert image to grayscale
4. Apply filters
5. Display filtered images
6. Analyze results

---

# 11. Comparison of Filters

| Filter          | Type       | Purpose              | Edge Preservation |
| --------------- | ---------- | -------------------- | ----------------- |
| Mean Filter     | Linear     | Noise reduction      | Poor              |
| Gaussian Filter | Linear     | Smooth image         | Moderate          |
| Sobel Filter    | Linear     | Edge detection       | High              |
| Median Filter   | Non-linear | Remove impulse noise | Excellent         |

---

# 12. Practical Exam Tips

Focus on understanding:

### Important Concepts

* Convolution
* Kernel
* Padding
* Image gradients
* Edge detection

### Important Filters

* Mean Filter
* Gaussian Filter
* Sobel Filter
* Median Filter

### Important Libraries

* OpenCV
* NumPy

---

# 13. Common Viva Questions

### What is a digital image?

A digital image is a matrix of pixels representing intensity values.

### What is filtering?

Filtering modifies image pixels using convolution operations.

### Difference between Mean and Median filter?

| Mean Filter  | Median Filter   |
| ------------ | --------------- |
| Uses average | Uses median     |
| Blurs edges  | Preserves edges |

### Why Gaussian filter is better than mean filter?

Gaussian filter gives **weighted smoothing**, preserving edges better.

---

# 14. Real-World Applications

These techniques are used in:

* Self-driving cars
* Medical imaging
* Security surveillance
* Face recognition
* Object detection

---

