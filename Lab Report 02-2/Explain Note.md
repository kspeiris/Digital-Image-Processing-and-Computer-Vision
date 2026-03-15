
---

# Digital Image Processing and Computer Vision

## Complete Practical Study Notes

---

# 1. Introduction to Digital Image Processing

## What is Digital Image Processing?

**Digital Image Processing (DIP)** is the technique of using computer algorithms to analyze, enhance, manipulate, and extract information from digital images.

A **digital image** is represented as a **matrix of pixel values**.

Example:

| Pixel  | Value      |
| ------ | ---------- |
| Bright | High value |
| Dark   | Low value  |

For grayscale images:

[
Pixel\ Range = 0 \rightarrow 255
]

* **0** → Black
* **255** → White

For color images:

Each pixel contains **three channels**:

* Red (R)
* Green (G)
* Blue (B)

---

# 2. Computer Vision

## What is Computer Vision?

**Computer Vision** is a field of Artificial Intelligence that enables computers to **interpret and understand visual information from images or videos**.

Examples:

* Face detection
* Object detection
* Medical image analysis
* Autonomous vehicles
* Image classification

Computer Vision systems rely heavily on **image processing techniques**.

---

# 3. Python Libraries Used in the Lab

The practical uses the following libraries:

| Library    | Purpose                              |
| ---------- | ------------------------------------ |
| OpenCV     | Image processing and computer vision |
| NumPy      | Matrix and numerical operations      |
| Matplotlib | Image visualization                  |
| MediaPipe  | Computer vision pipelines            |

### Installation

```python
!pip install mediapipe opencv-python --quiet
```

### Explanation

* `pip install` installs Python libraries.
* `mediapipe` → AI vision models (Google).
* `opencv-python` → OpenCV library.
* `--quiet` → reduces installation messages.

Note:

In this lab **MediaPipe is installed but not used in the main processing steps**.

---

# 4. Import Required Libraries

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt
```

### Explanation

| Library    | Function                   |
| ---------- | -------------------------- |
| cv2        | OpenCV functions           |
| numpy      | numerical array operations |
| matplotlib | image plotting             |

Additional settings:

```python
plt.rcParams['figure.figsize'] = (10,6)
plt.rcParams['image.cmap'] = 'gray'
```

These control:

* default figure size
* grayscale display style.

---

# 5. Loading an Image

```python
img = cv2.imread('Girl.jpg')
```

### Explanation

`cv2.imread()` reads the image from disk.

Important:

OpenCV loads images in **BGR format**, not RGB.

---

# 6. BGR vs RGB Color Format

OpenCV default:

```
BGR
```

Matplotlib expects:

```
RGB
```

Therefore we convert the image.

```python
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
```

### Explanation

`cv2.cvtColor()` converts color spaces.

This ensures **correct color display in Matplotlib**.

---

# 7. Displaying an Image

```python
plt.imshow(img_rgb)
plt.title("RGB Image")
plt.axis('off')
plt.show()
```

### Explanation

* `imshow()` displays the image.
* `title()` adds title.
* `axis('off')` removes axis lines.

---

# 8. Understanding Image Channels

A color image contains **3 channels**:

| Channel | Meaning                  |
| ------- | ------------------------ |
| Red     | intensity of red color   |
| Green   | intensity of green color |
| Blue    | intensity of blue color  |

Each channel stores pixel values from **0–255**.

---

# 9. Image Histogram

## What is a Histogram?

An **image histogram** shows the distribution of pixel intensities.

Example interpretation:

| Histogram Type | Meaning       |
| -------------- | ------------- |
| Left side      | dark image    |
| Right side     | bright image  |
| Spread         | high contrast |

---

# 10. Calculating Histogram Using OpenCV

```python
hist = cv2.calcHist([img_rgb], [i], None, [256], [0,256])
```

### Parameters

| Parameter   | Meaning        |
| ----------- | -------------- |
| `[img_rgb]` | image input    |
| `[i]`       | channel index  |
| `None`      | no mask        |
| `[256]`     | number of bins |
| `[0,256]`   | pixel range    |

---

# 11. Plotting RGB Histograms

```python
colors = ('r','g','b')
channel_names = ['Red','Green','Blue']

for i, col in enumerate(colors):
    hist = cv2.calcHist([img_rgb], [i], None, [256], [0,256])
    plt.plot(hist, color=col)
```

### Explanation

Each channel histogram shows **color intensity distribution**.

Example:

Red histogram → red intensity distribution.

---

# 12. Channel Splitting

We can separate the RGB channels.

```python
r_channel, g_channel, b_channel = cv2.split(img_rgb)
```

### Explanation

This extracts:

* red matrix
* green matrix
* blue matrix

Each matrix represents **intensity values for that color**.

---

# 13. Statistical Analysis of Image Channels

To analyze brightness characteristics we calculate:

* Mean intensity
* Standard deviation

Example:

```python
mean_intensity = np.average(np.arange(256), weights=hist)
```

### Mean Intensity

Average brightness of the image.

High mean → bright image
Low mean → dark image

---

### Standard Deviation

Measures **contrast variation**.

High STD → high contrast
Low STD → low contrast

---

# 14. Practical Applications

These techniques are used in:

### Medical Imaging

* tumor detection
* X-ray analysis

### Face Recognition

* biometric authentication

### Autonomous Driving

* lane detection
* object recognition

### Security Systems

* surveillance
* motion detection

### Agriculture

* crop disease detection

---

# 15. Common Image Processing Operations

In practical exams you may also be asked about:

| Operation              | Description       |
| ---------------------- | ----------------- |
| Image resizing         | change resolution |
| Image filtering        | remove noise      |
| Edge detection         | detect boundaries |
| Thresholding           | segment objects   |
| Color space conversion | RGB → grayscale   |

---

# 16. Grayscale Conversion

```python
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
```

Result:

Single channel image.

Pixel range:

```
0 - 255
```

---

# 17. Importance of Histograms in Computer Vision

Histograms help to:

* analyze brightness
* adjust contrast
* perform histogram equalization
* improve object detection

---

# 18. Workflow of the Lab

The practical workflow is:

```
Install libraries
        ↓
Import libraries
        ↓
Load image
        ↓
Convert BGR → RGB
        ↓
Display image
        ↓
Compute histograms
        ↓
Plot RGB histograms
        ↓
Split channels
        ↓
Statistical analysis
```

---

# 19. Key Functions Used

| Function       | Purpose           |
| -------------- | ----------------- |
| cv2.imread()   | load image        |
| cv2.cvtColor() | color conversion  |
| cv2.calcHist() | compute histogram |
| cv2.split()    | separate channels |
| plt.imshow()   | display image     |
| plt.plot()     | plot histogram    |

---

# 20. Important Exam Points

For your practical exam remember:

✔ OpenCV loads images in **BGR format**
✔ Convert to **RGB for Matplotlib**
✔ Histograms show **pixel intensity distribution**
✔ Color images have **three channels (R,G,B)**
✔ Statistical analysis helps measure **brightness and contrast**

---

# Short Practical Summary (Very Important)

Digital image processing uses computer algorithms to analyze and manipulate digital images. In this lab, Python libraries such as OpenCV, NumPy, and Matplotlib are used to load and process images. Images are first loaded using OpenCV, which reads them in BGR format. Since Matplotlib displays images in RGB format, the image is converted using `cv2.cvtColor()`. The image is then displayed and analyzed. RGB histograms are computed using `cv2.calcHist()` to study the distribution of color intensities. The image channels are also split into red, green, and blue components using `cv2.split()`. Statistical measures such as mean intensity and standard deviation are calculated to analyze brightness and contrast of the image.

---

