
---

# Digital Image Processing and Computer Vision

## Practical Exam Complete Study Notes

---

# 1. Digital Image Processing (DIP)

**Digital Image Processing** is the use of computer algorithms to manipulate and analyze digital images.

A digital image is a **matrix of pixel values**.

For example:

* A grayscale image → 2D matrix
* A color image → 3D matrix (RGB)

Example structure:

```
Image = Height × Width × Channels
```

Example:

```
512 × 512 × 3
```

Channels:

* Red
* Green
* Blue

### Applications

* Medical imaging
* Face recognition
* Autonomous vehicles
* Satellite imaging
* Security systems
* Object detection

---

# 2. Computer Vision

Computer Vision allows computers to **interpret visual information from images or videos**.

Examples:

* Face detection
* Object recognition
* Motion tracking
* Medical diagnosis
* Image classification

### Difference

| Digital Image Processing | Computer Vision      |
| ------------------------ | -------------------- |
| Image enhancement        | Understanding images |
| Filtering                | Object recognition   |
| Histogram analysis       | Scene interpretation |

---

# 3. Required Python Libraries

The lab uses the following libraries.

### OpenCV

OpenCV is the most popular computer vision library.

Used for:

* image reading
* image processing
* filtering
* object detection

Install:

```python
pip install opencv-python
```

Import:

```python
import cv2
```

---

### NumPy

Used for matrix operations.

```python
import numpy as np
```

---

### Matplotlib

Used for visualization.

```python
import matplotlib.pyplot as plt
```

---

# 4. Import Libraries

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

plt.rcParams['figure.figsize'] = (10,6)
plt.rcParams['image.cmap'] = 'gray'
```

Purpose:

* OpenCV → image processing
* NumPy → matrix operations
* Matplotlib → display images

---

# 5. Reading an Image

OpenCV reads images using:

```python
img = cv2.imread("Girl.jpg")
```

Important:

OpenCV loads images in **BGR format**, not RGB.

Example:

```
BGR = Blue, Green, Red
```

Check if the image loaded correctly:

```python
if img is None:
    raise FileNotFoundError("Image not found")
```

---

# 6. Displaying an Image

```python
plt.imshow(img)
plt.title("Image")
plt.axis("off")
```

Problem:

The colors appear incorrect because OpenCV uses **BGR**.

---

# 7. Convert BGR → RGB

To fix the color issue:

```python
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
```

Display:

```python
plt.imshow(img_rgb)
plt.title("RGB Image")
plt.axis("off")
```

---

# 8. Image Histogram

A histogram represents the **distribution of pixel intensities**.

Pixel values range:

```
0 – 255
```

Where:

| Value | Meaning |
| ----- | ------- |
| 0     | black   |
| 255   | white   |

Histogram shows how many pixels exist for each intensity.

---

# 9. RGB Channel Histogram

Each color channel has its own histogram.

Code:

```python
colors = ('r','g','b')
channel_names = ['Red','Green','Blue']

for i, col in enumerate(colors):
    hist = cv2.calcHist([img_rgb], [i], None, [256], [0,256])
    plt.plot(hist, color=col)

plt.title("RGB Channel Histogram")
plt.xlabel("Pixel Intensity")
plt.ylabel("Frequency")
plt.show()
```

Explanation:

* `cv2.calcHist()` calculates histogram
* 256 bins represent values from **0–255**

---

# 10. Splitting RGB Channels

We can separate color channels.

```python
r_channel, g_channel, b_channel = cv2.split(img_rgb)
```

Now we have:

```
Red channel
Green channel
Blue channel
```

Each channel is a **2D matrix**.

---

# 11. Display Each Channel

```python
plt.subplot(1,3,1)
plt.imshow(r_channel, cmap='Reds')

plt.subplot(1,3,2)
plt.imshow(g_channel, cmap='Greens')

plt.subplot(1,3,3)
plt.imshow(b_channel, cmap='Blues')
```

This shows the intensity contribution of each color.

---

# 12. Image Shape

Check dimensions:

```python
print(img_rgb.shape)
```

Example output:

```
(512, 512, 3)
```

Meaning:

| Value | Meaning      |
| ----- | ------------ |
| 512   | height       |
| 512   | width        |
| 3     | RGB channels |

---

# 13. Histogram for Each Channel

```python
hist_r = cv2.calcHist([r_channel],[0],None,[256],[0,256])
hist_g = cv2.calcHist([g_channel],[0],None,[256],[0,256])
hist_b = cv2.calcHist([b_channel],[0],None,[256],[0,256])
```

Plot:

```python
plt.plot(hist_r, color='r')
plt.plot(hist_g, color='g')
plt.plot(hist_b, color='b')
```

---

# 14. Statistical Analysis of Channels

We can compute statistics such as:

* Mean
* Standard deviation
* Peak intensity

Example function:

```python
def channel_stats(channel):

    mean = np.mean(channel)
    std = np.std(channel)
    max_val = np.max(channel)

    print("Mean:", mean)
    print("Std:", std)
    print("Max:", max_val)
```

Usage:

```python
channel_stats(r_channel)
channel_stats(g_channel)
channel_stats(b_channel)
```

---

# 15. Mean Intensity

Mean intensity represents the **average brightness** of a channel.

Formula:

```
Mean = Sum(pixel values) / Number of pixels
```

Higher mean → brighter image.

---

# 16. Standard Deviation

Standard deviation measures **contrast variation**.

High std → high contrast
Low std → flat image

Formula:

```
σ = √( Σ(x - μ)² / N )
```

Where:

* μ = mean
* N = number of pixels

---

# 17. Practical Workflow (Exam Important)

Typical DIP workflow:

```
1. Import libraries
2. Read image
3. Convert BGR → RGB
4. Display image
5. Split RGB channels
6. Calculate histograms
7. Plot histograms
8. Analyze statistics
```

---

# 18. Common Practical Exam Questions

### Q1

Load an image using OpenCV.

```python
img = cv2.imread("image.jpg")
```

---

### Q2

Convert BGR to RGB.

```python
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
```

---

### Q3

Split RGB channels.

```python
r,g,b = cv2.split(img_rgb)
```

---

### Q4

Compute histogram.

```python
cv2.calcHist([img],[0],None,[256],[0,256])
```

---

### Q5

Display image.

```python
plt.imshow(img_rgb)
```

---

# 19. Key Concepts to Remember

* OpenCV loads images in **BGR**
* Histograms show **pixel intensity distribution**
* RGB images have **3 channels**
* Pixel range = **0–255**
* NumPy handles **matrix operations**
* Matplotlib is used for **visualization**

---
