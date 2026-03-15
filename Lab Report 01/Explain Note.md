# Complete Lab Notes: Digital Image Processing and Computer Vision
## Lab 01: Image Manipulation and Color Space Analysis

---

## Table of Contents
1. [Introduction to OpenCV and Image Handling](#introduction)
2. [Understanding Color Spaces](#color-spaces)
3. [RGB vs BGR: The OpenCV Quirk](#rgb-vs-bgr)
4. [Loading and Displaying Images](#loading-display)
5. [Converting Between Color Spaces](#conversion)
6. [Histogram Analysis](#histogram-analysis)
7. [Complete Code Walkthrough](#code-walkthrough)
8. [Key Concepts Summary](#key-concepts)
9. [Potential Exam Questions](#exam-questions)

---

## 1. Introduction to OpenCV and Image Handling {#introduction}

### What is OpenCV?
OpenCV (Open Source Computer Vision Library) is a library of programming functions mainly aimed at real-time computer vision. It provides tools for:
- Image and video processing
- Object detection and recognition
- Machine learning for vision tasks

### Key Libraries Used in This Lab
```python
import cv2          # OpenCV for image processing
import numpy as np  # Numerical operations on arrays
import matplotlib.pyplot as plt  # Display and plot images
```

**Why these libraries?**
- `cv2`: Core image operations (read, convert, display)
- `numpy`: Images are stored as arrays, numpy enables mathematical operations
- `matplotlib`: Better visualization and plotting capabilities

---

## 2. Understanding Color Spaces {#color-spaces}

### What is a Color Space?
A color space is a specific organization of colors that allows for reproducible representations of color.

### RGB Color Space
- **Red, Green, Blue** channels
- Each pixel has three values (R,G,B) typically 0-255 each
- Additive color model (light-based)
- Used in digital displays, cameras

### BGR Color Space
- Same as RGB but with Red and Blue channels swapped
- **Important**: OpenCV reads images in BGR format by default
- This is a historical quirk from OpenCV's early development

### Why Color Space Matters
- Different color spaces are suited for different tasks
- Converting between color spaces allows us to:
  - Correct color display issues
  - Separate color information from intensity
  - Perform color-based analysis

---

## 3. RGB vs BGR: The OpenCV Quirk {#rgb-vs-bgr}

### The Problem
```python
# OpenCV reads images in BGR format
img = cv2.imread('image.jpg')  # Result is BGR

# But matplotlib expects RGB format
plt.imshow(img)  # Colors will appear wrong!
```

### Why This Happens
- OpenCV was developed with BGR as default due to historical reasons
- Many display systems (including matplotlib) use RGB
- This creates a mismatch that must be corrected

### Visual Result
- If you display a BGR image with RGB display:
  - Red and Blue channels are swapped
  - Images appear with incorrect colors (e.g., sky looks red, faces look blue)

---

## 4. Loading and Displaying Images {#loading-display}

### Loading an Image
```python
# Basic image loading
img = cv2.imread('Girl.jpg')

# Check if image loaded successfully
if img is None:
    print("Error: Could not load image")
    exit()
```

### `cv2.imread()` Parameters
- First parameter: filename (string)
- Second parameter (optional): flag for color mode
  - `cv2.IMREAD_COLOR` (1): Load color image (default)
  - `cv2.IMREAD_GRAYSCALE` (0): Load as grayscale
  - `cv2.IMREAD_UNCHANGED` (-1): Load with alpha channel

### Displaying with Matplotlib
```python
plt.figure(figsize=(12, 4))  # Create figure with specific size
plt.imshow(img)               # Display the image
plt.title('Image Title')      # Add title
plt.show()                    # Render the display
```

### `plt.figure()` Parameters
- `figsize=(width, height)`: Size in inches
- Larger figures show more detail but take more space

---

## 5. Converting Between Color Spaces {#conversion}

### The Conversion Function
```python
# Convert from BGR to RGB
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
```

### `cv2.cvtColor()` Parameters
- First parameter: source image
- Second parameter: conversion code
  - `cv2.COLOR_BGR2RGB`: BGR → RGB
  - `cv2.COLOR_RGB2BGR`: RGB → BGR
  - `cv2.COLOR_BGR2GRAY`: BGR → Grayscale
  - Many others for HSV, LAB, etc.

### Visual Comparison
- **BGR Image**: Colors appear swapped (red appears blue, etc.)
- **RGB Image**: Colors appear as expected

---

## 6. Histogram Analysis {#histogram-analysis}

### What is an Image Histogram?
A histogram is a graphical representation of the pixel intensity distribution in an image.

### Color Channel Histograms
```python
colors = ('r', 'g', 'b')
channel_names = ['Red', 'Green', 'Blue']

for i, col in enumerate(colors):
    hist = cv2.calcHist([img_rgb], [i], None, [256], [0,256])
    plt.plot(hist, color=col, label=f'{channel_names[i]} Channel')
```

### `cv2.calcHist()` Parameters
- `[img_rgb]`: List of images (must be in list format)
- `[i]`: Channel index (0=blue, 1=green, 2=red in BGR; different in RGB!)
- `None`: Mask (None = use entire image)
- `[256]`: Number of bins (0-255 gives 256 bins)
- `[0,256]`: Range of pixel values

### Interpreting Histograms

| Histogram Feature | What It Indicates |
|-------------------|-------------------|
| Peaks on left (0-50) | Dark image, shadows |
| Peaks in middle (100-150) | Well-exposed, balanced |
| Peaks on right (200-255) | Bright image, highlights |
| Wide distribution | High contrast |
| Narrow distribution | Low contrast |

### RGB Histogram Analysis
- **Red channel** (red line): Distribution of red intensities
- **Green channel** (green line): Distribution of green intensities
- **Blue channel** (blue line): Distribution of blue intensities

### What We Learn from Histograms
1. **Color balance**: Are all channels similarly distributed?
2. **Exposure**: Is the image properly exposed?
3. **Contrast**: Is there a good spread of intensities?
4. **Color cast**: Does one channel dominate?

---

## 7. Complete Code Walkthrough {#code-walkthrough}

### Step 1: Install Required Libraries
```python
!pip install mediapipe opencv-python --quiet
```
- `mediapipe`: Google's ML solutions (not used in this lab but installed)
- `opencv-python`: OpenCV for Python
- `--quiet`: Suppress installation output

### Step 2: Import Libraries
```python
import cv2
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

plt.rcParams['figure.figsize'] = (10,6)
plt.rcParams['image.cmap'] = 'gray'
```
- `%matplotlib inline`: Display plots directly in notebook
- `plt.rcParams`: Set default figure parameters

### Step 3: Load and Display Original (BGR) Image
```python
img = cv2.imread('Girl.jpg')
plt.figure(figsize=(12,4))
plt.imshow(img)
plt.title('GBR Image')
plt.show()
```
- **Note**: Colors appear incorrect because OpenCV loads as BGR

### Step 4: Convert and Display RGB Image
```python
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
plt.figure(figsize=(12,4))
plt.imshow(img_rgb)
plt.title('RGB Image(original)')
plt.show()
```
- Colors now appear correctly

### Step 5: Generate and Plot Histograms
```python
colors = ('r', 'g', 'b')
channel_names = ['Red', 'Green', 'Blue']

plt.figure(figsize=(10,6))

for i, col in enumerate(colors):
    hist = cv2.calcHist([img_rgb], [i], None, [256], [0,256])
    plt.plot(hist, color=col, label=f'{channel_names[i]} Channel')
    plt.xlim([0,256])

plt.title('RGB Channel Histograms - Girl.png')
plt.xlabel('Pixel Intensity (0–255)')
plt.ylabel('Frequency')
plt.legend()
plt.grid(alpha=0.3)
plt.show()
```

---

## 8. Key Concepts Summary {#key-concepts}

### Image Fundamentals
- Images are 3D arrays (height × width × channels)
- Each pixel has intensity values (0-255 per channel)
- Color images have 3 channels (RGB or BGR)

### OpenCV vs Matplotlib
| Library | Default Color Space | Use Case |
|---------|---------------------|----------|
| OpenCV | BGR | Image processing |
| Matplotlib | RGB | Display and plotting |

### Color Space Conversion
- Always convert BGR → RGB before displaying with matplotlib
- Use `cv2.cvtColor()` with appropriate conversion code

### Histograms
- Show distribution of pixel intensities
- Can analyze each color channel separately
- Useful for exposure and color balance analysis

---

## 9. Potential Exam Questions {#exam-questions}

### Short Answer Questions

1. **Why do images loaded with OpenCV appear with incorrect colors when displayed with matplotlib?**

   *Answer:* OpenCV loads images in BGR (Blue-Green-Red) format by default, while matplotlib expects RGB (Red-Green-Blue) format. When displayed without conversion, the red and blue channels are swapped, causing color distortion.

2. **What function is used to convert between color spaces in OpenCV? What are its parameters?**

   *Answer:* `cv2.cvtColor(src, code)` where `src` is the source image and `code` specifies the conversion (e.g., `cv2.COLOR_BGR2RGB`).

3. **What information does a histogram provide about an image?**

   *Answer:* A histogram shows the distribution of pixel intensities. It reveals exposure (dark/bright), contrast (spread of intensities), and color balance (distribution across channels).

4. **What are the parameters of `cv2.calcHist()` and what do they mean?**

   *Answer:* 
   - `images`: List of images to analyze
   - `channels`: Channel indices to analyze
   - `mask`: Region of interest (None for whole image)
   - `histSize`: Number of bins (typically 256)
   - `ranges`: Pixel value range (typically [0,256])

### Practical Questions

5. **Write code to load an image, convert it from BGR to RGB, and display it with proper colors.**

   ```python
   img = cv2.imread('image.jpg')
   img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
   plt.imshow(img_rgb)
   plt.show()
   ```

6. **Write code to generate and plot histograms for all three RGB channels.**

   ```python
   colors = ('r', 'g', 'b')
   for i, col in enumerate(colors):
       hist = cv2.calcHist([img_rgb], [i], None, [256], [0,256])
       plt.plot(hist, color=col)
   plt.show()
   ```

7. **How would you check if an image was loaded successfully?**

   ```python
   if img is None:
       print("Error: Could not load image")
   else:
       # Process image
   ```

### True/False Questions

8. **OpenCV loads images in RGB format by default.** (False - it loads in BGR)

9. **Matplotlib can display BGR images correctly without conversion.** (False - colors will be wrong)

10. **A histogram with peaks in the middle range indicates a well-exposed image.** (True)

### Multiple Choice Questions

11. **Which OpenCV function converts BGR to RGB?**
    a) `cv2.convertColor()`
    b) `cv2.cvtColor()`
    c) `cv2.colorConvert()`
    d) `cv2.changeColor()`
    
    **Answer:** b) `cv2.cvtColor()`

12. **What does a narrow histogram distribution indicate?**
    a) High contrast
    b) Low contrast
    c) Good exposure
    d) Color cast
    
    **Answer:** b) Low contrast

---

## Quick Reference Card

| Operation | Code |
|-----------|------|
| Read image | `cv2.imread('file.jpg')` |
| Convert BGR→RGB | `cv2.cvtColor(img, cv2.COLOR_BGR2RGB)` |
| Display with matplotlib | `plt.imshow(img_rgb)` |
| Calculate histogram | `cv2.calcHist([img], [channel], None, [256], [0,256])` |
| Plot histogram | `plt.plot(hist, color='r')` |
| Set figure size | `plt.figure(figsize=(width, height))` |
| Add title | `plt.title('Title')` |
| Add legend | `plt.legend()` |
| Add grid | `plt.grid(alpha=0.3)` |

---
