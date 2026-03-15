Your uploaded lab file contains a **Digital Image Processing practical about Histogram Equalization using OpenCV**. I carefully reviewed the notebook. Below is a **complete study note for your practical exam**, including **theory + algorithm + code explanation** so you can understand everything clearly.

---

# Digital Image Processing & Computer Vision

## Practical Study Note — Histogram Equalization using OpenCV

---

# 1. Introduction to Digital Image Processing

**Digital Image Processing (DIP)** is the field of computer science that focuses on processing and analyzing images using algorithms.

An **image** is represented in a computer as a **matrix of pixel values**.

Example:

```
Image = matrix of numbers
```

For a grayscale image:

```
Pixel value range = 0 → 255
```

| Value | Meaning        |
| ----- | -------------- |
| 0     | Black          |
| 255   | White          |
| 1–254 | Shades of gray |

For color images, each pixel has **3 channels**:

```
R (Red)
G (Green)
B (Blue)
```

---

# 2. What is Computer Vision?

**Computer Vision** is the field that enables computers to **understand and analyze images or videos automatically**.

Common tasks include:

* Face detection
* Object recognition
* Medical image analysis
* Self-driving car vision systems
* Security surveillance

Computer Vision relies heavily on **image processing techniques**.

---

# 3. Image Histogram

A **histogram** is a graph that shows how pixel intensities are distributed in an image.

It tells us:

* How many pixels are **dark**
* How many are **bright**
* Overall **contrast of the image**

Example histogram:

```
X axis → Pixel intensity (0 – 255)
Y axis → Number of pixels
```

Types of histograms:

| Histogram Shape | Meaning       |
| --------------- | ------------- |
| Left heavy      | Dark image    |
| Right heavy     | Bright image  |
| Narrow range    | Low contrast  |
| Wide range      | High contrast |

---

# 4. What is Histogram Equalization?

**Histogram Equalization** is a technique used to **improve image contrast**.

It redistributes pixel values so that the histogram becomes more **uniformly distributed**.

### Goal

Spread pixel intensities across the full range **0–255**.

### Benefits

* Enhances contrast
* Reveals hidden details
* Improves visibility in low-contrast images

---

# 5. Steps of Histogram Equalization

1. Calculate image histogram
2. Compute **Cumulative Distribution Function (CDF)**
3. Normalize CDF
4. Map old pixel values to new values
5. Generate enhanced image

Mathematically:

[
s_k = (L-1) \times \sum_{j=0}^{k} p_r(r_j)
]

Where:

| Symbol | Meaning                    |
| ------ | -------------------------- |
| (L)    | number of intensity levels |
| (p_r)  | probability distribution   |
| (s_k)  | new pixel value            |

---

# 6. Libraries Used in the Lab

The notebook uses the following libraries.

| Library      | Purpose                   |
| ------------ | ------------------------- |
| OpenCV (cv2) | Image processing          |
| NumPy        | Numerical operations      |
| Matplotlib   | Display images and graphs |
| OS           | File path checking        |

---

# 7. Importing Libraries

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt
import os
```

### Explanation

**cv2**

OpenCV library used for:

* Reading images
* Image processing
* Histogram equalization

---

**numpy**

Used for:

* Mathematical operations
* Array manipulation

Images are stored as **NumPy arrays**.

---

**matplotlib.pyplot**

Used to:

* Display images
* Plot histograms
* Visualize results

---

**os**

Used to:

* Check if image file exists
* Handle file paths

---

# 8. Reading an Image

```python
image_path = "/content/sample_data/Girl.jpg"
```

Defines the location of the image file.

---

### Checking if image exists

```python
if not os.path.exists(image_path):
    print("Image not found")
```

This prevents errors if the file path is incorrect.

---

### Loading the image

```python
img = cv2.imread(image_path)
```

OpenCV reads the image.

Important:

OpenCV loads images in **BGR format**.

```
B = Blue
G = Green
R = Red
```

---

# 9. Converting Image to Grayscale

Histogram equalization usually works on **grayscale images**.

```python
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
```

Explanation:

```
cv2.cvtColor()
```

Converts the color space.

```
COLOR_BGR2GRAY
```

Converts BGR image to grayscale.

---

# 10. Histogram Equalization

```python
equalized = cv2.equalizeHist(gray)
```

This function:

* Computes histogram
* Computes cumulative distribution
* Redistributes pixel intensities

Result:

```
Enhanced contrast image
```

---

# 11. Plotting the Images

Using Matplotlib to visualize results.

```python
plt.figure(figsize=(10,5))
```

Creates a figure window.

---

### Display original image

```python
plt.subplot(1,2,1)
plt.imshow(gray, cmap='gray')
plt.title("Original Image")
plt.axis('off')
```

Explanation:

```
subplot(1,2,1)
```

1 row, 2 columns, first image.

```
cmap='gray'
```

Displays grayscale image.

---

### Display enhanced image

```python
plt.subplot(1,2,2)
plt.imshow(equalized, cmap='gray')
plt.title("Equalized Image")
plt.axis('off')
```

Shows improved contrast.

---

# 12. Display Histograms

Histogram of original image:

```python
plt.hist(gray.ravel(), 256, [0,256])
```

Explanation:

```
gray.ravel()
```

Flattens the image into 1-D array.

```
256
```

Number of intensity levels.

```
[0,256]
```

Intensity range.

---

Histogram of equalized image:

```python
plt.hist(equalized.ravel(), 256, [0,256])
```

This histogram is usually **more evenly distributed**.

---

# 13. Expected Output

The program produces:

1️⃣ Original Image
2️⃣ Equalized Image
3️⃣ Original Histogram
4️⃣ Equalized Histogram

Observation:

| Image     | Result        |
| --------- | ------------- |
| Original  | Low contrast  |
| Equalized | High contrast |

---

# 14. Applications of Histogram Equalization

Histogram equalization is used in:

### Medical imaging

Enhancing X-ray or MRI images.

---

### Satellite imagery

Improving terrain visibility.

---

### Surveillance systems

Enhancing low-light camera footage.

---

### Face recognition

Improving facial feature detection.

---

# 15. Advantages

✔ Improves image contrast
✔ Reveals hidden details
✔ Simple and fast algorithm
✔ Widely used in computer vision

---

# 16. Limitations

❌ Can amplify noise
❌ Not ideal for color images
❌ May over-enhance some regions

---

# 17. Practical Exam Tips

If asked in exam:

### What is histogram equalization?

Answer:

> Histogram equalization is an image processing technique used to improve image contrast by redistributing pixel intensity values so that the histogram becomes more uniform.

---

### Why grayscale is used?

Because histogram equalization works on **single intensity channels**.

---

### Why OpenCV uses BGR?

OpenCV internally stores images in **Blue-Green-Red order instead of RGB**.

---

# 18. Full Workflow of the Lab

```
Load Image
      ↓
Check File Path
      ↓
Convert to Grayscale
      ↓
Apply Histogram Equalization
      ↓
Display Images
      ↓
Plot Histograms
```

---

# 19. Key Functions You Must Remember

| Function             | Purpose                |
| -------------------- | ---------------------- |
| `cv2.imread()`       | Read image             |
| `cv2.cvtColor()`     | Convert color space    |
| `cv2.equalizeHist()` | Histogram equalization |
| `plt.imshow()`       | Show image             |
| `plt.hist()`         | Plot histogram         |

---

# 20. Short Summary

This lab demonstrates **histogram equalization**, a contrast enhancement technique used in digital image processing. The program reads an image using OpenCV, converts it to grayscale, applies histogram equalization, and displays the improved image along with its histogram using Matplotlib.

---

If you want, I can also give you the **FULL FINAL PRACTICAL CODE with detailed comments (line-by-line explanation)** exactly like your previous lab notes so you can **write it easily in the exam**.
