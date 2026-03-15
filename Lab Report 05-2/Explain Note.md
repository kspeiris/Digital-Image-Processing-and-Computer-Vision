
---

# Digital Image Processing & Computer Vision

# Practical Note — Image Filtering

---

# 1. Introduction to Image Filtering

Image filtering is a fundamental technique in **Digital Image Processing (DIP)** used to:

* Remove noise
* Smooth images
* Enhance edges
* Detect structures
* Extract useful information

Filtering works by applying a **kernel (mask)** to each pixel in an image.

A **kernel** is a small matrix used to process pixels.

Example kernel:

[
\begin{bmatrix}
1 & 1 & 1 \
1 & 1 & 1 \
1 & 1 & 1
\end{bmatrix}
]

Each pixel value is recalculated using the neighboring pixel values.

---

# 2. Types of Filters

Image filters are generally classified into two main categories:

## 1. Linear Filters

These perform operations using **linear combinations of pixels**.

Examples:

* Mean filter
* Gaussian filter
* Sobel filter

## 2. Non-Linear Filters

These perform **non-linear operations** on pixels.

Example:

* Median filter

Non-linear filters are often better for **removing noise while preserving edges**.

---

# 3. Mean Filter (Average Filter)

## Concept

The **Mean Filter** replaces each pixel value with the **average of its surrounding pixels**.

It is mainly used for:

* Noise reduction
* Image smoothing

However, it can **blur edges**.

---

## Mathematical Representation

For a kernel size (k \times k):

[
g(x,y) = \frac{1}{k^2} \sum_{i=-n}^{n} \sum_{j=-n}^{n} f(x+i, y+j)
]

Where

* (f(x,y)) = original image
* (g(x,y)) = filtered image
* (k) = kernel size

---

## Example Kernel (3×3)

[
\frac{1}{9}
\begin{bmatrix}
1 & 1 & 1 \
1 & 1 & 1 \
1 & 1 & 1
\end{bmatrix}
]

---

## Characteristics

Advantages

* Simple
* Fast
* Reduces Gaussian noise

Disadvantages

* Blurs edges
* Reduces image details

---

# 4. Gaussian Filter

## Concept

The **Gaussian filter** is a smoothing filter based on the **Gaussian distribution**.

It gives **higher weight to the center pixel** and lower weight to distant pixels.

This preserves edges better than the mean filter.

---

## Gaussian Function

[
G(x,y) =
\frac{1}{2\pi\sigma^2}
e^{-\frac{x^2 + y^2}{2\sigma^2}}
]

Where

* (x,y) = pixel coordinates
* (\sigma) = standard deviation controlling smoothing

---

## Characteristics

Advantages

* Smooths image while preserving structure
* Removes Gaussian noise
* Better than mean filter

Disadvantages

* Still causes some blur
* Computationally heavier than mean filter

---

# 5. Sobel Filter (Edge Detection)

## Concept

The **Sobel filter** is used for **edge detection**.

Edges represent **significant intensity changes** in the image.

The Sobel operator calculates **image gradients**.

---

## Sobel Kernels

Horizontal kernel:

[
G_x =
\begin{bmatrix}
-1 & 0 & 1 \
-2 & 0 & 2 \
-1 & 0 & 1
\end{bmatrix}
]

Vertical kernel:

[
G_y =
\begin{bmatrix}
-1 & -2 & -1 \
0 & 0 & 0 \
1 & 2 & 1
\end{bmatrix}
]

---

## Gradient Magnitude

[
G = \sqrt{G_x^2 + G_y^2}
]

or

[
G = |G_x| + |G_y|
]

---

## Applications

* Edge detection
* Object detection
* Feature extraction
* Computer vision tasks

---

# 6. Median Filter

## Concept

The **Median filter** is a **non-linear filter**.

Instead of averaging pixels, it selects the **median value** in a neighborhood.

This is very effective for removing **salt-and-pepper noise**.

---

## Example

Pixel neighborhood:

```
10  12  255
9   11  13
8   10  12
```

Sorted values:

```
8, 9, 10, 10, 11, 12, 12, 13, 255
```

Median value:

```
11
```

So the center pixel becomes **11**.

---

## Characteristics

Advantages

* Removes impulse noise
* Preserves edges
* Better than mean filter for noise

Disadvantages

* Computationally expensive
* Not ideal for Gaussian noise

---

# 7. Morphological Operations

Morphological operations are used to **process binary images based on shape**.

They require a **structuring element**.

Example structuring element:

[
\begin{bmatrix}
1 & 1 & 1 \
1 & 1 & 1 \
1 & 1 & 1
\end{bmatrix}
]

---

## Common Morphological Operations

### 1. Erosion

Removes small objects.

Effect:

* Shrinks objects
* Removes noise

Operation:

[
A \ominus B
]

Where

* (A) = image
* (B) = structuring element

---

### 2. Dilation

Expands object boundaries.

Effect:

* Fills holes
* Connects nearby objects

Operation:

[
A \oplus B
]

---

### 3. Opening

Opening = **Erosion followed by Dilation**

Used to:

* Remove small objects
* Smooth boundaries

---

### 4. Closing

Closing = **Dilation followed by Erosion**

Used to:

* Fill small holes
* Connect objects

---

# 8. Structuring Element

A **structuring element** defines how morphological operations interact with the image.

Common shapes:

* Square
* Rectangle
* Circle
* Ellipse

Example in OpenCV:

```python
kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (5,5))
```

Different sizes produce different results.

Large kernels → stronger effect.

---

# 9. Noise in Images

Noise refers to **random variation in pixel values**.

Common noise types:

### Gaussian Noise

Random intensity variation.

### Salt-and-Pepper Noise

Random white and black pixels.

### Speckle Noise

Multiplicative noise often in medical images.

---

# 10. Filter Comparison

| Filter          | Type           | Best For          | Problem            |
| --------------- | -------------- | ----------------- | ------------------ |
| Mean Filter     | Linear         | Smoothing         | Blurs edges        |
| Gaussian Filter | Linear         | Noise reduction   | Slight blur        |
| Median Filter   | Non-linear     | Salt-pepper noise | Slower             |
| Sobel Filter    | Edge detection | Detect edges      | Sensitive to noise |

---

# 11. Libraries Used in the Practical

The practical uses several Python libraries.

### OpenCV (`cv2`)

Used for:

* Image reading
* Image processing
* Filtering
* Morphological operations

Example:

```python
img = cv2.imread("image.jpg")
```

---

### NumPy

Used for:

* Matrix operations
* Kernel creation
* Pixel manipulation

Example:

```python
kernel = np.ones((3,3))
```

---

### Matplotlib

Used to **display images and graphs**.

Example:

```python
plt.imshow(image, cmap="gray")
plt.show()
```

---

# 12. Workflow of the Practical

Typical steps performed in the lab:

1. Import required libraries

2. Load image

3. Apply filters

* Mean filter
* Gaussian filter
* Sobel filter
* Median filter

4. Compare results

5. Apply morphological operations

6. Use different kernel sizes

7. Analyze output images

---

# 13. Key Concepts for Practical Exam

You should remember:

### Core Topics

* Image filtering
* Linear vs non-linear filters
* Mean filter
* Gaussian filter
* Median filter
* Sobel edge detection
* Morphological operations
* Structuring elements

### Python Libraries

* OpenCV
* NumPy
* Matplotlib

### Concepts

* Kernel
* Convolution
* Image smoothing
* Noise removal
* Edge detection

---

# 14. Applications of Image Filtering

Image filtering is widely used in:

* Medical imaging
* Face detection
* Autonomous vehicles
* Security surveillance
* Object recognition
* Satellite imaging
* Computer vision systems

---



```
# Complete Full Explanation of the Code
## 1 Install Libraries
## 2 Import Libraries
## 3 Load Image
## 4 Mean Filter
## 5 Gaussian Filter
## 6 Sobel Filter
## 7 Median Filter
## 8 Morphological Operations
```

