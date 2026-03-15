

---

# Digital Image Processing and Computer Vision

## Complete Practical Exam Notes

---

# 1. Digital Image Processing (DIP)

## Definition

Digital Image Processing is the use of **computer algorithms to process digital images** in order to improve image quality or extract useful information.

A **digital image** is a 2-D function:

[
f(x,y)
]

Where

* **x, y** → spatial coordinates
* **f(x,y)** → pixel intensity

If **x, y and f are finite values**, the image is digital.

### Pixel

A **pixel** is the smallest unit of a digital image.

Example grayscale values:

| Pixel Value | Meaning |
| ----------- | ------- |
| 0           | Black   |
| 255         | White   |

---

# 2. Computer Vision

## Definition

Computer Vision is a field of AI that enables computers to **understand and interpret images and videos**.

### Examples

* Face detection
* Object recognition
* Medical image analysis
* Autonomous driving

### Relationship

| Digital Image Processing    | Computer Vision                 |
| --------------------------- | ------------------------------- |
| Focuses on improving images | Focuses on understanding images |
| Low-level processing        | High-level interpretation       |

---

# 3. Python Libraries Used in the Labs

## OpenCV

OpenCV (Open Source Computer Vision Library) is used for:

* Image reading
* Image processing
* Computer vision algorithms

Example import:

```python
import cv2
```

---

## NumPy

NumPy is used for numerical operations and array manipulation.

```python
import numpy as np
```

Images in OpenCV are stored as **NumPy arrays**.

---

## Matplotlib

Used for displaying images and graphs.

```python
import matplotlib.pyplot as plt
```

---

# 4. Installing Required Libraries

Example installation in notebooks:

```python
!pip install mediapipe opencv-python --quiet
```

### Explanation

| Component       | Purpose                        |
| --------------- | ------------------------------ |
| `!`             | Runs shell command in notebook |
| `pip install`   | Installs Python package        |
| `opencv-python` | Computer vision library        |
| `mediapipe`     | AI vision models               |
| `--quiet`       | Reduces console output         |

---

# 5. Reading and Displaying Images

## Reading Image

```python
image = cv2.imread("image.jpg")
```

### Explanation

* Reads image from file
* Image stored as NumPy array
* Default format is **BGR**

---

## Display Image using OpenCV

```python
cv2.imshow("Image", image)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

---

## Display using Matplotlib

```python
plt.imshow(image)
plt.title("Image")
plt.show()
```

Note:

OpenCV uses **BGR** format while Matplotlib uses **RGB**.

Conversion:

```python
image_rgb = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
```

---

# 6. Image Representation

Images can be represented in different formats.

---

## Grayscale Image

Single channel image.

Pixel range:

```
0 – 255
```

Conversion:

```python
gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
```

---

## RGB Image

Three channels:

* Red
* Green
* Blue

Shape example:

```
(Height, Width, 3)
```

---

# 7. Image Histogram

## Definition

Histogram shows the **distribution of pixel intensity values** in an image.

Used to analyze:

* brightness
* contrast

---

## Calculate Histogram

```python
hist = cv2.calcHist([gray],[0],None,[256],[0,256])
```

---

## Plot Histogram

```python
plt.plot(hist)
plt.title("Histogram")
plt.show()
```

---

# 8. Image Noise

Noise is **random variation in pixel values**.

Types of noise:

| Noise Type      | Description                   |
| --------------- | ----------------------------- |
| Gaussian noise  | Random normal distribution    |
| Salt and Pepper | Random white and black pixels |
| Speckle noise   | Multiplicative noise          |

Noise degrades image quality.

---

# 9. Image Filtering

Filtering is used to:

* remove noise
* smooth images
* enhance features

---

# 10. Mean Filter (Average Filter)

Mean filter replaces each pixel with the **average of neighboring pixels**.

Example:

```python
blur = cv2.blur(image,(5,5))
```

Purpose:

* Smooth image
* Reduce noise

---

# 11. Gaussian Filter

Gaussian filtering uses a **Gaussian function** to smooth the image.

```python
blur = cv2.GaussianBlur(image,(5,5),0)
```

Advantages:

* Better smoothing
* Preserves edges better than mean filter

---

# 12. Median Filter

Median filter replaces pixel with **median value of neighbors**.

```python
median = cv2.medianBlur(image,5)
```

Best for:

Salt-and-pepper noise.

---

# 13. Convolution

Filtering is implemented using **convolution**.

Convolution uses a **kernel (filter matrix)**.

Example kernel:

[
\begin{bmatrix}
1 & 1 & 1 \
1 & 1 & 1 \
1 & 1 & 1
\end{bmatrix}
]

Applied over image to compute new pixel values.

---

# 14. Kernel

A kernel is a **small matrix used to modify images**.

Example:

| Kernel Size | Use                |
| ----------- | ------------------ |
| 3×3         | small smoothing    |
| 5×5         | stronger smoothing |

---

# 15. Image Edge Detection

Edge detection finds boundaries in images.

Common methods:

* Sobel
* Canny
* Laplacian

Example:

```python
edges = cv2.Canny(image,100,200)
```

Edges represent areas of **high intensity change**.

---

# 16. Image Processing Pipeline

Typical DIP workflow:

```
Image Input
     ↓
Preprocessing
     ↓
Noise Removal
     ↓
Filtering
     ↓
Feature Extraction
     ↓
Computer Vision Task
```

---

# 17. Applications of Digital Image Processing

### Medical Imaging

* MRI analysis
* Tumor detection

### Security

* Face recognition
* Fingerprint detection

### Autonomous Vehicles

* Lane detection
* Object detection

### Satellite Imaging

* Weather analysis
* Earth monitoring

---

# 18. Advantages of Digital Image Processing

* Improves image quality
* Extracts useful information
* Enables automation
* Supports AI applications

---

# 19. Limitations

* Requires high computational power
* Sensitive to noise
* Large datasets required

---

# 20. Key Practical Exam Points

You must know:

✔ Installing OpenCV
✔ Reading images
✔ Converting BGR → RGB
✔ Grayscale conversion
✔ Histogram plotting
✔ Noise types
✔ Mean filter
✔ Gaussian filter
✔ Median filter
✔ Edge detection basics

---

