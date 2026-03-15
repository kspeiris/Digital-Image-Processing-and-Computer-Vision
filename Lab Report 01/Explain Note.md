---

# Digital Image Processing & Computer Vision

## Practical Lab Notes (Complete Study Guide)

---

# 1. Introduction to Digital Image Processing

**Digital Image Processing (DIP)** is the use of computer algorithms to process and analyze digital images.

A **digital image** is represented as a **matrix of pixels**, where each pixel contains intensity values.

Example:

| Pixel Matrix |
| ------------ |
| 255 200 150  |
| 180 120 90   |
| 60 40 10     |

Each number represents **pixel intensity**.

For **color images**, each pixel contains **3 values**:

* Red (R)
* Green (G)
* Blue (B)

---

# 2. Computer Vision

**Computer Vision** is a field of Artificial Intelligence that enables computers to interpret and understand visual information from images or videos.

Applications include:

* Face recognition
* Medical imaging
* Self-driving cars
* Object detection
* Gesture recognition
* Surveillance systems

---

# 3. Python Libraries Used

## 1️⃣ OpenCV (cv2)

OpenCV is a popular library for **image processing and computer vision tasks**.

Common uses:

* Image reading
* Image transformation
* Feature detection
* Object detection

Installation:

```python
pip install opencv-python
```

---

## 2️⃣ NumPy

NumPy is used for **numerical operations on arrays**.

Images in Python are stored as **NumPy arrays**.

Example:

```python
import numpy as np
```

---

## 3️⃣ Matplotlib

Matplotlib is used to **display images and plots**.

Example:

```python
import matplotlib.pyplot as plt
```

---

# 4. Reading an Image

Images are loaded using OpenCV.

```python
img = cv2.imread('Girl.jpg')
```

### Important

OpenCV loads images in **BGR format**, not RGB.

Meaning:

| Channel | Order  |
| ------- | ------ |
| Blue    | First  |
| Green   | Second |
| Red     | Third  |

---

# 5. Converting BGR to RGB

Matplotlib expects images in **RGB format**.

Therefore we convert the image:

```python
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
```

This rearranges the channels:

BGR → RGB

---

# 6. Displaying Images

Images are displayed using Matplotlib.

```python
plt.imshow(img_rgb)
plt.title("RGB Image")
plt.axis('off')
plt.show()
```

Important commands:

| Command     | Purpose       |
| ----------- | ------------- |
| imshow()    | display image |
| title()     | add title     |
| axis('off') | remove axes   |
| show()      | render plot   |

---

# 7. Image Representation

A color image has **3 dimensions**.

Example:

```
image_rgb.shape
```

Output example:

```
(512, 512, 3)
```

Meaning:

| Value | Meaning        |
| ----- | -------------- |
| 512   | image height   |
| 512   | image width    |
| 3     | color channels |

---

# 8. Splitting Color Channels

An RGB image can be separated into:

* Red channel
* Green channel
* Blue channel

Using OpenCV:

```python
r_channel, g_channel, b_channel = cv2.split(image_rgb)
```

Each channel is a **grayscale image representing color intensity**.

---

# 9. Visualizing Color Channels

Each channel can be displayed separately.

Example:

```python
plt.imshow(r_channel, cmap='Reds')
plt.title("Red Channel")
```

Example layout:

| Red           | Green           | Blue           |
| ------------- | --------------- | -------------- |
| red intensity | green intensity | blue intensity |

---

# 10. Image Histogram

A **histogram** shows the distribution of pixel intensities.

Range:

```
0 → black
255 → white
```

Histogram axis:

| Axis   | Meaning         |
| ------ | --------------- |
| X-axis | pixel intensity |
| Y-axis | frequency       |

---

# 11. Calculating Histogram

OpenCV function:

```python
cv2.calcHist()
```

Example:

```python
hist = cv2.calcHist([image_rgb], [0], None, [256], [0,256])
```

Parameters:

| Parameter     | Meaning     |
| ------------- | ----------- |
| image         | input image |
| channel index | 0,1,2       |
| mask          | None        |
| bins          | 256         |
| range         | 0–256       |

---

# 12. Plotting RGB Histograms

Example:

```python
colors = ('r','g','b')

for i, col in enumerate(colors):
    hist = cv2.calcHist([image_rgb], [i], None, [256], [0,256])
    plt.plot(hist, color=col)
```

Output shows **three curves**:

* Red histogram
* Green histogram
* Blue histogram

This helps analyze **color distribution in the image**.

---

# 13. Channel Statistical Analysis

Statistical analysis helps understand **image brightness and variation**.

Important metrics:

### Mean Intensity

Average brightness of pixels.

Formula:

```
Mean = Σ(pixel values) / N
```

Python:

```python
np.mean(channel)
```

---

### Standard Deviation

Measures how spread pixel values are.

High std → high contrast.

Python:

```python
np.std(channel)
```

---

### Peak Intensity

Most frequent pixel value.

Python:

```python
peak = np.argmax(hist)
```

---

# 14. Channel Statistics Function

Example function:

```python
def channel_stats(channel):
    mean = np.mean(channel)
    std = np.std(channel)

    hist = cv2.calcHist([channel],[0],None,[256],[0,256])
    peak = np.argmax(hist)

    return mean, std, peak
```

Output example:

```
Red channel | Mean: 132 | Std: 45 | Peak: 120
Green channel | Mean: 140 | Std: 40 | Peak: 135
Blue channel | Mean: 120 | Std: 38 | Peak: 110
```

---

# 15. Image Pixel Intensity

Pixel intensity range:

| Value | Meaning          |
| ----- | ---------------- |
| 0     | black            |
| 255   | white            |
| 0-255 | grayscale levels |

For color images each pixel has:

```
[R, G, B]
```

Example:

```
[255,0,0] → Red
[0,255,0] → Green
[0,0,255] → Blue
```

---

# 16. Importance of Histograms

Histograms help to:

* analyze brightness
* detect overexposed images
* perform contrast enhancement
* image thresholding
* color balancing

Applications include:

* medical imaging
* satellite imagery
* surveillance systems

---

# 17. Practical Workflow Used in the Lab

Step-by-step process:

### Step 1

Install libraries

```
pip install opencv-python mediapipe
```

---

### Step 2

Import libraries

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt
```

---

### Step 3

Load image

```python
image = cv2.imread("Girl.jpg")
```

---

### Step 4

Convert BGR → RGB

```python
image_rgb = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
```

---

### Step 5

Display image

```python
plt.imshow(image_rgb)
```

---

### Step 6

Split channels

```python
r,g,b = cv2.split(image_rgb)
```

---

### Step 7

Compute histograms

```python
cv2.calcHist()
```

---

### Step 8

Plot histogram

```python
plt.plot()
```

---

### Step 9

Compute statistics

```
mean
std
peak intensity
```

---

# 18. Real-World Applications

Digital image processing is used in:

### Medical Imaging

* tumor detection
* X-ray analysis

### Security

* face recognition
* biometric authentication

### Autonomous Vehicles

* road detection
* obstacle recognition

### Agriculture

* crop monitoring
* disease detection

### Robotics

* object tracking
* navigation systems

---

# 19. Key Practical Exam Questions

You may be asked:

### Q1

What is Digital Image Processing?

### Q2

Explain RGB and BGR formats.

### Q3

What is an image histogram?

### Q4

How do you read an image using OpenCV?

### Q5

Explain the function `cv2.calcHist()`.

### Q6

What is the purpose of converting BGR to RGB?

### Q7

Explain mean and standard deviation in image analysis.

---

# 20. Important Commands (Quick Revision)

| Function       | Purpose               |
| -------------- | --------------------- |
| cv2.imread()   | read image            |
| cv2.cvtColor() | color conversion      |
| cv2.split()    | split channels        |
| cv2.calcHist() | histogram calculation |
| np.mean()      | average intensity     |
| np.std()       | pixel variation       |
| plt.imshow()   | display image         |

---

✅ **Tip for your practical exam**

Always remember this sequence:

```
Read Image
↓
Convert BGR → RGB
↓
Display Image
↓
Split Channels
↓
Compute Histogram
↓
Analyze Statistics
```

---
