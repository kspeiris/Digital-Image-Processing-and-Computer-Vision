

---

# Complete Full Explanation of the Code

## Digital Image Processing and Computer Vision

## Lab01 → Lab05

---

# LAB 01 — RGB Image Loading and Histogram Analysis

---

## 1. Install required libraries

```python
!pip install mediapipe opencv-python --quiet
```

### Explanation

This line installs the external Python packages needed for the notebook.

### What each part means

* `!`
  Runs a shell command inside Jupyter Notebook or Google Colab.

* `pip install`
  Installs Python libraries.

* `mediapipe`
  A computer vision library by Google. In this lab, it is installed but not actually used in the visible code.

* `opencv-python`
  Installs OpenCV, which is the main library used for image processing.

* `--quiet`
  Reduces installation messages.

### Why this is used

Before using OpenCV functions such as reading images, converting colors, and computing histograms, the package must be installed.

---

## 2. Import libraries and set notebook display options

```python
from io import IncrementalNewlineDecoder
import cv2
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

plt.rcParams['figure.figsize'] = (10,6)
plt.rcParams['image.cmap'] = 'gray'
```

### Explanation

This block imports the required libraries and sets some default plotting options.

### What each line does

* `from io import IncrementalNewlineDecoder`
  Imports a Python utility. It does not play a visible role later in the code.

* `import cv2`
  Imports OpenCV.

* `import numpy as np`
  Imports NumPy for numerical operations and arrays.

* `import matplotlib.pyplot as plt`
  Imports Matplotlib for image and graph display.

* `%matplotlib inline`
  Displays plots directly inside the notebook.

* `plt.rcParams['figure.figsize'] = (10,6)`
  Sets the default figure size.

* `plt.rcParams['image.cmap'] = 'gray'`
  Sets the default image color map to grayscale.

### Why this is used

This makes the notebook ready for image processing and cleaner image display.

---

## 3. Read the image and convert BGR to RGB

```python
img_bgr = cv2.imread('Girl.jpg')
img_bgr = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)

plt.figure(figsize=(6,6))
plt.imshow(img_bgr)
plt.title('RGB Image(original)')
```

### Explanation

This block loads the image and converts it into RGB format for correct display with Matplotlib.

### Step by step

* `cv2.imread('Girl.jpg')`
  Reads the image from disk.

* OpenCV loads color images in **BGR** order, not RGB.

* `cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)`
  Converts the image from BGR to RGB.

* `plt.imshow(img_bgr)`
  Displays the image.

### Why conversion is needed

Matplotlib expects images in **RGB** format.
If the conversion is not done, colors appear incorrect.

---

## 4. Display the original image without conversion

```python
img = cv2.imread('Girl.jpg')  # created a variable and imported the image

plt.figure(figsize=(12,4))    # defined the figure size of the image
plt.imshow(img)               # to show the image
plt.title('GBR Image')
```

### Explanation

This block displays the image directly after loading it with OpenCV.

### Important note

Because OpenCV reads images in **BGR** format, displaying `img` directly using Matplotlib causes the colors to look wrong.

### Why this section exists

This demonstrates the difference between:

* raw OpenCV color order (**BGR**)
* display-ready color order (**RGB**)

---

## 5. Correctly convert and display the RGB image

```python
img = cv2.imread('Girl.jpg')
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)  # converted the image to RGB

plt.figure(figsize=(12,4))
plt.imshow(img_rgb)
plt.title('RGB Image(original)')
```

### Explanation

This is the correct way to display a color image read by OpenCV.

### What happens

* `img` stores the original BGR image
* `img_rgb` stores the converted RGB image
* `plt.imshow(img_rgb)` shows the correct colors

---

## 6. Plot RGB channel histograms

```python
colors = ('r','g','b')
channel_names = ['Red','Green','Blue']

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

### Explanation

This block calculates and plots the histogram of each RGB channel.

### What a histogram means

A histogram shows how many pixels belong to each intensity value from `0` to `255`.

### What each argument means in `cv2.calcHist()`

* `[img_rgb]`
  Input image

* `[i]`
  Channel index:

  * `0` = Red
  * `1` = Green
  * `2` = Blue

* `None`
  No mask is used

* `[256]`
  Number of bins

* `[0,256]`
  Range of intensity values

### Why this is useful

RGB histograms help analyze:

* brightness
* contrast
* channel dominance
* exposure characteristics

---

## 7. Import libraries again for the structured analysis section

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt
```

### Explanation

This cell re-imports the main libraries.
It is redundant, but harmless.

### Why it may appear

Sometimes notebook authors repeat imports when creating a new section to make it self-contained.

---

## 8. Load image safely with error handling

```python
image = cv2.imread('Girl.jpg')

if image is None:
    raise FileNotFoundError("❌ Could not find 'Girl.jpg'. Please upload it to the Colab working directory.")
```

### Explanation

This block safely reads the image and checks if loading failed.

### Why this is important

If the file path is wrong or the image does not exist, `cv2.imread()` returns `None`.

The code stops with a clear message instead of failing later.

---

## 9. Convert loaded image to RGB

```python
image_rgb = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
```

### Explanation

This converts the OpenCV image from BGR to RGB for correct plotting.

---

## 10. Display the original RGB image

```python
plt.figure(figsize=(6,6))
plt.imshow(image_rgb)
plt.title("Original Image - Girl.png (RGB)")
plt.axis('off')
plt.show()
```

### Explanation

This shows the correctly converted RGB image.

### Important part

* `plt.axis('off')` removes x and y axes for a cleaner image display.

---

## 11. Plot separate RGB histograms again

```python
colors = ('r', 'g', 'b')
channel_names = ['Red', 'Green', 'Blue']

plt.figure(figsize=(10,6))

for i, col in enumerate(colors):
    hist = cv2.calcHist([image_rgb], [i], None, [256], [0,256])
    plt.plot(hist, color=col, label=f'{channel_names[i]} Channel')
    plt.xlim([0,256])

plt.title('RGB Channel Histograms - Girl.png')
plt.xlabel('Pixel Intensity (0–255)')
plt.ylabel('Frequency')
plt.legend()
plt.grid(alpha=0.3)
plt.show()
```

### Explanation

This repeats the RGB histogram analysis using the safely loaded image.

### Why repeated

This appears to be a cleaner, more structured version of the earlier code.

---

## 12. Compute mean and standard deviation for each color channel

```python
for i, name in enumerate(channel_names):
    hist = cv2.calcHist([image_rgb], [i], None, [256], [0,256])
    hist = hist.flatten()
    mean_intensity = np.average(np.arange(256), weights=hist)
    std_intensity = np.sqrt(np.average((np.arange(256) - mean_intensity) ** 2, weights=hist))

    print(f" {name} Channel Analysis:")
    print(f"   Mean Intensity     : {mean_intensity:.2f}")
    print(f"   Standard Deviation : {std_intensity:.2f}")
```

### Explanation

This block calculates statistical information for each channel.

### Mean intensity

The **mean** tells the average brightness of a channel.

### Standard deviation

The **standard deviation** tells how spread out the intensity values are.

### Important operations

* `hist.flatten()`
  Converts the histogram into a 1D array.

* `np.average(..., weights=hist)`
  Computes the weighted average intensity.

* `np.sqrt(...)`
  Computes the standard deviation.

### Why this is useful

These values help quantify the histogram instead of only viewing it visually.

---

## 13. Split the image into separate R, G, and B channels

```python
r_channel, g_channel, b_channel = cv2.split(image_rgb)
```

### Explanation

This splits the RGB image into three separate single-channel images.

### Output

* `r_channel` → red intensities only
* `g_channel` → green intensities only
* `b_channel` → blue intensities only

---

## 14. Print image and channel shapes

```python
print(f"Original RGB Image Shape : {image_rgb.shape}")
print(f"Red Channel Shape        : {r_channel.shape}")
print(f"Green Channel Shape      : {g_channel.shape}")
print(f"Blue Channel Shape       : {b_channel.shape}")
```

### Explanation

This displays the dimensions of the full image and each individual channel.

### Expected behavior

* RGB image shape: `(height, width, 3)`
* each channel shape: `(height, width)`

### Why this matters

It shows that a color image has three layers, while each channel is only one layer.

---

## 15. Calculate separate histograms for split channels

```python
hist_r = cv2.calcHist([r_channel], [0], None, [256], [0,256])
hist_g = cv2.calcHist([g_channel], [0], None, [256], [0,256])
hist_b = cv2.calcHist([b_channel], [0], None, [256], [0,256])
```

### Explanation

This computes the histogram of each individual channel separately.

### Why do this after splitting?

It gives the same histogram idea, but now directly from each channel image.

---

## 16. Plot separate channel histograms together

```python
plt.figure(figsize=(10,6))
plt.plot(hist_r, color='r', label='Red Channel')
plt.plot(hist_g, color='g', label='Green Channel')
plt.plot(hist_b, color='b', label='Blue Channel')
plt.title('RGB Channel Histograms using cv2.calcHist()')
plt.xlabel('Pixel Intensity (0–255)')
plt.ylabel('Frequency')
plt.legend()
plt.grid(alpha=0.3)
plt.show()
```

### Explanation

This displays the three separate histograms on the same graph.

### Why this helps

It makes comparison easier between red, green, and blue distributions.

---

## 17. Display each channel as an image

```python
plt.figure(figsize=(15,5))
plt.subplot(1,3,1)
plt.imshow(r_channel, cmap='Reds')
plt.title('Red Channel')
plt.axis('off')

plt.subplot(1,3,2)
plt.imshow(g_channel, cmap='Greens')
plt.title('Green Channel')
plt.axis('off')

plt.subplot(1,3,3)
plt.imshow(b_channel, cmap='Blues')
plt.title('Blue Channel')
plt.axis('off')

plt.show()
```

### Explanation

This shows the visual content of each channel separately.

### Why color maps are used

* `cmap='Reds'` gives red-tinted display
* `cmap='Greens'` gives green-tinted display
* `cmap='Blues'` gives blue-tinted display

These are only for visualization.
The actual channel arrays are grayscale intensity maps.

---

## 18. Define a reusable channel statistics function

```python
def channel_stats(channel, name, color):
    """
    Prints basic statistics for a color channel:
    mean, standard deviation, and peak intensity value.
    """
    mean = np.mean(channel)
    std = np.std(channel)

    # Calculate histogram to find peak intensity (most frequent pixel value)
    hist = cv2.calcHist([channel], [0], None, [256], [0, 256])
    peak = np.argmax(hist)

    print(f"{name:5} channel | Mean: {mean:6.1f} | Std: {std:5.1f} | Peak Intensity: {peak:3}")
    return mean, std, peak

print("\n" + "="*70)
print("RGB CHANNEL STATISTICS")
print("-"*70)

r_stats = channel_stats(r_channel, "Red", "r")
g_stats = channel_stats(g_channel, "Green", "g")
b_stats = channel_stats(b_channel, "Blue", "b")

print("="*70)
```

### Explanation

This function computes three important properties for each channel:

* mean
* standard deviation
* peak intensity

### Peak intensity

`np.argmax(hist)` finds the intensity value that occurs most often.

### Why this is useful

It gives a compact numerical summary of each color channel.

---

# LAB 02 — Extended Histogram Analysis, Grayscale, HSV, YUV, and Exposure/Brightness

> The beginning of Lab02 repeats much of Lab01. The new parts mainly extend the histogram analysis.

---

## 1. Plot a single histogram quickly

```python
import matplotlib.pyplot as plt
plt.plot(hist_r)
```

### Explanation

This is a quick plot of the red-channel histogram only.

### Why this might be used

To inspect one channel alone before building more complex comparisons.

---

## 2. Extract mean values from earlier channel statistics

```python
mean_r = r_stats[0]
mean_g = g_stats[0]
mean_b = b_stats[0]
```

### Explanation

Each of `r_stats`, `g_stats`, and `b_stats` contains:

* mean
* standard deviation
* peak intensity

This code extracts only the mean values.

---

## 3. Display channel-only images and histograms with intensity zones

```python
plt.figure(figsize=(16,10))

channels = [
    (r_channel, hist_r, 'Red', 'red', mean_r),
    (g_channel, hist_g, 'Green', 'green', mean_g),
    (b_channel, hist_b, 'Blue', 'blue', mean_b)
]

for i, (ch, hist, name, color, mean_val) in enumerate(channels):

    plt.subplot(2, 3, i+1)
    masked = np.zeros_like(img_rgb)
    masked[:, :, i] = ch
    plt.imshow(masked)
    plt.title(f'{name} Channel Only', fontsize=14, fontweight='bold', color=color)
    plt.axis('off')

    plt.subplot(2, 3, i+4)

    plt.axvspan(0, 85, color='darkred', alpha=0.15, label='Low')
    plt.axvspan(86, 170, color='grey', alpha=0.15, label='Mid')
    plt.axvspan(171, 255, color='yellow', alpha=0.15, label='High')

    plt.plot(hist, color=color, linewidth=2, label=f'{name} Histogram')
    plt.fill_between(range(256), hist.flatten(), color=color, alpha=0.3)

    plt.axvline(mean_val, color='black', linestyle='--', linewidth=2,
                label=f'Mean = {mean_val:.1f}')

    plt.xlabel('Pixel Intensity (0–255)')
    plt.ylabel('Pixel Count')
    plt.grid(True, alpha=0.3)
    plt.xlim([0, 255])
    plt.legend()

plt.suptitle('RGB Channel Analysis - Lena Image\nDigital Image Processing Laboratory, Sri Lanka',
             fontsize=20, fontweight='bold', color='#2c3e50',
             y=0.98, fontfamily='DejaVu Sans')
plt.tight_layout()
plt.show()
```

### Explanation

This block creates a detailed visualization for each channel.

### Upper row

Shows each channel isolated as a color-only image.

### Lower row

Shows each channel histogram with:

* low intensity region: `0–85`
* mid intensity region: `86–170`
* high intensity region: `171–255`

### Mean line

The black dashed line marks the mean intensity of the channel.

### Why this is useful

This makes interpretation easier by separating histogram regions visually.

---

## 4. Overlay all three histograms in one graph

```python
plt.figure(figsize=(12, 6))

plt.plot(hist_r, color='red', alpha=0.8, label='Red Channel', linewidth=2)
plt.plot(hist_g, color='green', alpha=0.8, label='Green Channel', linewidth=2)
plt.plot(hist_b, color='blue', alpha=0.8, label='Blue Channel', linewidth=2)

plt.title('Overlay: R, G, B Channel Histograms (Lena)', fontsize=16, fontweight='bold')
plt.xlabel('Intensity (0-255)')
plt.ylabel('Pixel Count')
plt.grid(True, alpha=0.3)
plt.xlim(0, 255)
plt.legend()

plt.gca().set_facecolor('#f8f9fa')
plt.gca().spines['top'].set_visible(False)
plt.gca().spines['right'].set_visible(False)

plt.tight_layout()
plt.show()
```

### Explanation

This plots all three channel histograms together in one clean graph.

### Styling added

* background color
* hidden top and right borders
* thicker lines

### Why this helps

It is useful for quick comparison of the full RGB distribution.

---

## 5. Overlay filled histograms and mean lines

```python
plt.figure(figsize=(14, 7))

x = range(256)
plt.fill_between(x, hist_r.flatten(), alpha=0.4, color='red', label='Red Channel')
plt.fill_between(x, hist_g.flatten(), alpha=0.4, color='green', label='Green Channel')
plt.fill_between(x, hist_b.flatten(), alpha=0.4, color='blue', label='Blue Channel')

plt.plot(hist_r, color='red', alpha=0.9, linewidth=2.5)
plt.plot(hist_g, color='green', alpha=0.9, linewidth=2.5)
plt.plot(hist_b, color='blue', alpha=0.9, linewidth=2.5)

plt.axvline(mean_r, color='red', linestyle='--', alpha=0.7, linewidth=1.5)
plt.axvline(mean_g, color='green', linestyle='--', alpha=0.7, linewidth=1.5)
plt.axvline(mean_b, color='blue', linestyle='--', alpha=0.7, linewidth=1.5)

plt.title('■ RGB Channels Overlay Histogram - Lena Image',
          fontsize=18, fontweight='bold', pad=20)
plt.xlabel('Pixel Intensity (0-255)', fontsize=12, fontweight='medium')
plt.ylabel('Pixel Frequency', fontsize=12, fontweight='medium')
plt.grid(True, alpha=0.2)
plt.xlim(0, 255)

plt.legend(loc='upper right', framealpha=0.9, edgecolor='#cccccc')

plt.gca().set_facecolor('#fafafa')
for spine in plt.gca().spines.values():
    spine.set_color('#dddddd')

plt.tight_layout()
plt.show()
```

### Explanation

This is a more polished histogram overlay.

### New feature here

* Filled color under each histogram
* Mean intensity lines for each channel

### Why this is important

The mean lines help indicate the average brightness location of each channel.

---

## 6. Convert RGB image to grayscale and compute histogram

```python
gray_cv = cv2.cvtColor(image_rgb, cv2.COLOR_RGB2GRAY)
hist = cv2.calcHist([gray_cv], [0], None, [256], [0, 256])

plt.figure(figsize=(10, 5))
plt.plot(hist, color='black')
plt.title('Grayscale Histogram')
plt.xlabel('Intensity (0–255)')
plt.ylabel('Number of Pixels')
plt.xlim([0, 256])
plt.grid(True, alpha=0.3)
plt.show()
```

### Explanation

This converts the RGB image into grayscale and plots its histogram.

### Why grayscale is important

A grayscale image has only one intensity channel, which makes brightness analysis simpler.

---

## 7. Compute and plot the cumulative distribution function (CDF)

```python
cdf = hist.cumsum()
cdf_normalized = cdf * hist.max() / cdf.max()

plt.figure(figsize=(10, 5))
plt.plot(hist, color='black', alpha=0.7, label='Histogram')
plt.plot(cdf_normalized, color='red', label='CDF (normalized)')
plt.title('Histogram and Cumulative Distribution Function')
plt.xlabel('Intensity')
plt.ylabel('Count')
plt.legend()
plt.grid(True, alpha=0.3)
plt.show()
```

### Explanation

This code computes the cumulative distribution function from the histogram.

### What `cdf` means

`cdf[i]` = total number of pixels with intensity less than or equal to `i`.

### Why normalize it

The CDF is scaled so it can be shown on the same plot as the histogram.

### Why this is useful

CDF is very important in histogram equalization.

---

## 8. Convert image to HSV and plot Hue, Saturation, and Value histograms

```python
hsv = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2HSV)
h, s, v = cv2.split(hsv)

fig, ax = plt.subplots(1, 3, figsize=(15, 4))
channels = [h, s, v]
names = ['Hue', 'Saturation', 'Value']
colors = ['red', 'green', 'blue']

for i in range(3):
    hist_ch = cv2.calcHist([channels[i]], [0], None, [256], [0, 256])
    ax[i].plot(hist_ch, color=colors[i])
    ax[i].set_title(f'{names[i]} Histogram')
    ax[i].set_xlim([0, 256])
    ax[i].grid(True, alpha=0.3)

plt.tight_layout()
plt.show()
```

### Explanation

This converts the image into HSV color space and plots the histogram of each component.

### HSV components

* **Hue (H)** → color type
* **Saturation (S)** → color strength
* **Value (V)** → brightness

### Why use HSV

HSV separates brightness from color better than RGB.

---

## 9. Convert image to YUV and compare grayscale with Y channel

```python
yuv = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2YUV)
y, u, v = cv2.split(yuv)

hist_y = cv2.calcHist([y], [0], None, [256], [0, 256])

plt.figure(figsize=(10, 5))
plt.plot(hist, color='black', label='Grayscale (RGB)')
plt.plot(hist_y, color='orange', label='Y-Channel (YUV)')
plt.title('Grayscale vs Y-Channel Histogram')
plt.xlabel('Intensity')
plt.ylabel('Count')
plt.legend()
plt.grid(True, alpha=0.3)
plt.show()
```

### Explanation

This compares the grayscale histogram with the luminance `Y` channel in YUV.

### Why this matters

Both grayscale and the Y channel represent brightness information, but they may not be exactly identical depending on the conversion formula.

---

## 10. Create high, good, and low exposure images

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

img_bgr = cv2.imread('Girl.jpg')

if img_bgr is None:
    raise FileNotFoundError("❌ Could not find 'Girl.jpg'. Please upload it to the working directory.")

img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
gray    = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2GRAY)

plt.figure(figsize=(6,6))
plt.imshow(img_rgb)
plt.title('RGB Image (Original)')
plt.axis('off')
plt.show()

def adjust_exposure(img, factor):
    return np.clip(img * factor, 0, 255).astype('uint8')

good = gray.copy()
high = adjust_exposure(gray, 2.0)
low  = adjust_exposure(gray, 0.4)
```

### Explanation

This block creates images with different exposure levels.

### Exposure simulation

* `factor = 2.0` → brighter / overexposed
* `factor = 0.4` → darker / underexposed

### Why `np.clip()` is used

Pixel values must remain in the valid range `0–255`.

---

## 11. Compute histograms for different exposure levels

```python
def compute_hist(img):
    return cv2.calcHist([img], [0], None, [256], [0, 256])

hist_good = compute_hist(good)
hist_high = compute_hist(high)
hist_low  = compute_hist(low)
```

### Explanation

This calculates histograms for the normal, high-exposure, and low-exposure grayscale images.

---

## 12. Compute exposure statistics

```python
def exposure_stats(img, name):
    mean = np.mean(img)
    shadows = np.sum(img < 50) / img.size * 100
    highlights = np.sum(img > 200) / img.size * 100

    if highlights > 35:
        status = "Overexposed"
    elif shadows > 35:
        status = "Underexposed"
    else:
        status = "Good"

    print(f"{name:12} | Mean: {mean:6.1f} | Shadows: {shadows:5.1f}% | Highlights: {highlights:5.1f}% | → {status}")
```

### Explanation

This function labels the image exposure based on dark and bright pixel percentages.

### Logic used

* too many highlights → overexposed
* too many shadows → underexposed
* otherwise → good exposure

---

## 13. Create high, good, and low brightness images

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

img_bgr = cv2.imread('Girl.jpg')
img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
gray = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2GRAY)

def adjust_brightness(img, delta):
    """
    Add constant to all pixels to change brightness
    delta > 0 → brighter, delta < 0 → darker
    """
    return np.clip(img.astype(np.int16) + delta, 0, 255).astype('uint8')

good = gray.copy()
high = adjust_brightness(gray, +100)
low = adjust_brightness(gray, -100)
```

### Explanation

This changes image brightness by adding or subtracting a constant value.

### Difference from exposure scaling

* **Exposure adjustment** multiplies values
* **Brightness adjustment** adds or subtracts a fixed amount

---

## 14. Compute brightness statistics

```python
def brightness_stats(img, name):
    mean = np.mean(img)
    median = np.median(img)
    shadows = np.sum(img < 50) / img.size * 100
    highlights = np.sum(img > 200) / img.size * 100

    if mean < 70:
        label = "Low Brightness (Dark)"
    elif mean < 180:
        label = "Good Brightness"
    else:
        label = "High Brightness (Over-bright)"

    print(f"{name:14} | Mean: {mean:6.1f} | Median: {median:6.1f} | Shadows: {shadows:5.1f}% | Highlights: {highlights:5.1f}% | → {label}")
    return mean, median, shadows, highlights, label
```

### Explanation

This block classifies brightness levels using:

* mean
* median
* shadow percentage
* highlight percentage

### Why median is included

Median is less affected by extreme intensity values than mean.

---

# LAB 03 — Histogram Equalization

---

## 1. Import libraries and load the image safely

```python
import cv2
import matplotlib.pyplot as plt
import os

image_path = "/content/sample_data/Girl.jpg"

if not os.path.exists(image_path):
    print(f"Error: Image file not found at '{image_path}'")
    print("\nPlease update the image_path variable to point to a valid image file.")
    print("Example: image_path = 'path/to/your/image.jpg'")
else:
    img = cv2.imread(image_path)

    if img is None:
        print(f"Error: Could not read image from '{image_path}'")
        print("Make sure the file is a valid image format (PNG, JPG, etc.)")
```

### Explanation

This block checks whether the image path is valid and whether the image can be loaded.

### Why both checks are useful

* `os.path.exists()` checks whether the file exists
* `cv2.imread()` verifies the file can actually be read as an image

---

## 2. Convert to RGB and grayscale, then apply grayscale histogram equalization

```python
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

img_gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

img_gray_eq = cv2.equalizeHist(img_gray)
```

### Explanation

This performs grayscale histogram equalization.

### What histogram equalization does

It redistributes the intensity values so that the contrast becomes more spread out across the full range.

### Result

Dark regions and low-contrast areas often become clearer.

---

## 3. Apply color histogram equalization using YUV color space

```python
img_yuv = cv2.cvtColor(img_rgb, cv2.COLOR_RGB2YUV)

img_yuv[:, :, 0] = cv2.equalizeHist(img_yuv[:, :, 0])

img_yuv_eq = cv2.cvtColor(img_yuv, cv2.COLOR_YUV2RGB)
```

### Explanation

This performs histogram equalization on a color image.

### Why YUV is used

Instead of equalizing all RGB channels separately, only the **Y channel** is equalized.

* `Y` = luminance / brightness
* `U`, `V` = chrominance / color information

### Why this is better

Equalizing only brightness improves contrast while preserving more natural colors.

---

## 4. Display original and equalized images and histograms

```python
plt.figure(figsize=(14, 6))

plt.subplot(2, 4, 1)
plt.imshow(img_rgb)
plt.title('Original Image (RGB)')
plt.axis('off')

plt.subplot(2, 4, 2)
plt.imshow(img_gray_eq, cmap='gray')
plt.title('Grayscale + Hist Equalization')
plt.axis('off')

plt.subplot(2, 4, 3)
plt.hist(img_gray.flatten(), bins=256, color='black', alpha=0.7)
plt.title('Original Grayscale Histogram')
plt.xlabel('Pixel Intensity')
plt.ylabel('Frequency')

plt.subplot(2, 4, 4)
plt.hist(img_gray_eq.flatten(), bins=256, color='black', alpha=0.7)
plt.title('Equalized Grayscale Histogram')
plt.xlabel('Pixel Intensity')
plt.ylabel('Frequency')

plt.subplot(2, 4, 5)
plt.imshow(img_rgb)
plt.title('Original Color Image')
plt.axis('off')

plt.subplot(2, 4, 6)
plt.imshow(img_yuv_eq)
plt.title('Color Hist Equalization (YUV)')
plt.axis('off')

plt.subplot(2, 4, 7)
plt.imshow(img_gray, cmap='gray')
plt.title('Original Grayscale')
plt.axis('off')

plt.subplot(2, 4, 8)
colors = ('r', 'g', 'b')
for i, color in enumerate(colors):
    hist = cv2.calcHist([img_rgb], [i], None, [256], [0, 256])
    plt.plot(hist, color=color, alpha=0.7)
plt.title('Original RGB Histogram')
plt.xlabel('Pixel Intensity')
plt.ylabel('Frequency')

plt.tight_layout()
plt.show()
```

### Explanation

This block compares:

* original RGB image
* original grayscale image
* equalized grayscale image
* equalized color image
* original and equalized histograms

### Key observation

After equalization, the histogram usually becomes more spread out, showing increased contrast.

---

## 5. Improved version with Y-channel before/after comparison

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt
import os

image_path = "/content/sample_data/Girl.jpg"

if not os.path.exists(image_path):
    print(f"❌ Error: Image file '{image_path}' not found!")
    print("\nTroubleshooting:")
    print("1. Make sure 'Girl.png' is in the same directory as your notebook")
    print("2. Or provide the full path, e.g., '/content/Girl.png' or 'path/to/Girl.png'")
    print("\nFiles in current directory:")
    print([f for f in os.listdir('.') if f.endswith(('.png', '.jpg', '.jpeg'))])
else:
    img = cv2.imread(image_path)

    if img is None:
        print(f"❌ Error: Could not read '{image_path}'")
        print("Make sure it's a valid image file (PNG, JPG, etc.)")
    else:
        img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

        img_gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
        img_gray_eq = cv2.equalizeHist(img_gray)

        img_yuv = cv2.cvtColor(img, cv2.COLOR_BGR2YUV)
        img_yuv_original = img_yuv.copy()
        img_yuv[:,:,0] = cv2.equalizeHist(img_yuv[:,:,0])

        img_color_eq = cv2.cvtColor(img_yuv, cv2.COLOR_YUV2RGB)
```

### Explanation

This version keeps a copy of the original YUV image before equalization.

### Why that matters

It allows direct comparison of the **Y channel histogram before and after equalization**.

---

## 6. Plot full histogram equalization results in a 2×4 layout

```python
plt.figure(figsize=(15,10))

plt.subplot(2,4,1)
plt.imshow(img_rgb)
plt.title('Original RGB Image')
plt.axis('off')

plt.subplot(2,4,2)
plt.imshow(img_gray, cmap='gray')
plt.title('Grayscale')
plt.axis('off')

plt.subplot(2,4,3)
plt.imshow(img_gray_eq, cmap='gray')
plt.title('Grayscale + Histogram Equalization')
plt.axis('off')

plt.subplot(2,4,4)
plt.imshow(img_color_eq)
plt.title('Color Equalizer (YUV)')
plt.axis('off')

plt.subplot(2,4,5)
plt.hist(
    img_gray.ravel(),
    bins=256,
    range=(0,256),
    color='gray',
    alpha=0.7,
    label='Original'
)
plt.title('Histogram (Grayscale Original)')
plt.xlabel('Pixel Intensity')
plt.ylabel('Frequency')
plt.legend()

plt.subplot(2,4,6)
plt.hist(
    img_gray_eq.ravel(),
    bins=256,
    range=(0,256),
    color='black',
    alpha=0.7,
    label='Equalized'
)
plt.title('Histogram (Grayscale Equalized)')
plt.xlabel('Pixel Intensity')
plt.ylabel('Frequency')
plt.legend()

plt.subplot(2,4,7)
plt.hist(
    img_yuv_original[:,:,0].ravel(),
    bins=256,
    range=(0,256),
    color='orange',
    alpha=0.7,
    label='Original Y'
)
plt.title('Histogram of Y channel (Before)')
plt.xlabel('Pixel Intensity')
plt.ylabel('Frequency')
plt.legend()

plt.subplot(2,4,8)
plt.hist(
    img_yuv[:,:,0].ravel(),
    bins=256,
    range=(0,256),
    color='red',
    alpha=0.7,
    label='Equalized Y'
)
plt.title('Histogram of Y channel (After)')
plt.xlabel('Pixel Intensity')
plt.ylabel('Frequency')
plt.legend()

plt.tight_layout()
plt.show()
```

### Explanation

This figure shows a very complete summary of histogram equalization.

### What is being compared

* original RGB image
* grayscale image
* equalized grayscale image
* equalized color image
* original grayscale histogram
* equalized grayscale histogram
* original Y histogram
* equalized Y histogram

---

## 7. Print additional summary statistics

```python
print("✓ Histogram equalization completed successfully!")
print(f"  Image shape: {img.shape}")
print(f"  Original image intensity range: {img_gray.min()} - {img_gray.max()}")
print(f"  Equalized image intensity range: {img_gray_eq.min()} - {img_gray_eq.max()}")
```

### Explanation

This prints useful summary information after processing.

### Why intensity range matters

A wider useful intensity range generally means better contrast distribution.

---

# LAB 04 — Image Smoothing and Padding in Convolution

---

## 1. Import libraries

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt
import os
import urllib.request

%matplotlib inline
plt.rcParams['figure.figsize'] = [10, 8]
```

### Explanation

This imports the libraries required for smoothing and convolution experiments.

### Why `urllib.request` may appear

It can be used to download images if needed, though it is not central to the visible logic.

---

## 2. Load the image and convert to grayscale

```python
image_path = 'Girl.jpg'

if not os.path.exists(image_path):
    print(f"Error: {image_path} not found!")
    print("Please ensure 'Girl.jpg' is available.")
else:
    img = cv2.imread(image_path)
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
```

### Explanation

This reads the image and creates a grayscale version for filtering.

### Why grayscale is used

Filtering examples are easier to understand on a single-channel image.

---

## 3. Implement a manual 3×3 averaging filter

```python
def average_filter_manual(image):
    h, w = image.shape
    output = np.zeros_like(image)

    padded = np.pad(image, pad_width=1, mode='constant', constant_values=0)

    for y in range(h):
        for x in range(w):
            region = padded[y:y+3, x:x+3]
            output[y, x] = np.mean(region)

    return output
```

### Explanation

This function performs averaging filter manually without using OpenCV’s ready-made filter functions.

### Step by step

* `np.pad(...)` adds a 1-pixel border
* the nested loops visit every pixel
* `region = padded[y:y+3, x:x+3]` gets the local 3×3 neighborhood
* `np.mean(region)` computes the average intensity

### Why this is important

It shows the real mathematical idea behind smoothing.

---

## 4. Apply the manual averaging filter

```python
smoothed_manual = average_filter_manual(gray)
print("Manual 3×3 averaging filter applied successfully!")
```

### Explanation

This applies the custom smoothing function to the grayscale image.

### What happens

Noise and small intensity variations become smoother.

---

## 5. Apply averaging using `cv2.filter2D()`

```python
kernel_3x3 = np.ones((3,3), np.float32) / 9.0

smoothed_filter2d = cv2.filter2D(gray, -1, kernel_3x3)
print("cv2.filter2D() smoothing completed!")
```

### Explanation

This performs the same averaging operation using OpenCV’s built-in convolution function.

### Kernel meaning

[
\frac{1}{9}
\begin{bmatrix}
1 & 1 & 1 \
1 & 1 & 1 \
1 & 1 & 1
\end{bmatrix}
]

This means every pixel becomes the average of itself and its 8 neighbors.

---

## 6. Apply fast smoothing using `cv2.blur()`

```python
smoothed_fast = cv2.blur(gray, (3,3))
```

### Explanation

This is OpenCV’s faster, simplified function for mean filtering.

### Equivalent idea

It performs the same type of 3×3 averaging as the manual and `filter2D` methods.

---

## 7. Add noise to the image and smooth it

```python
noise = np.random.randint(-30, 31, gray.shape, dtype=np.int16)

noisy = np.clip(gray.astype(np.int16) + noise, 0, 255).astype(np.uint8)

noisy_smoothed = cv2.blur(noisy, (3,3))

print("Noise added and smoothing applied to noisy image!")
```

### Explanation

This section demonstrates the effect of smoothing on a noisy image.

### What is happening

* random noise between `-30` and `+30` is generated
* noise is added to the grayscale image
* values are clipped to remain valid
* smoothing is applied to reduce noise

### Why this is useful

It shows the practical purpose of averaging filters.

---

## 8. Display original, smoothed, noisy, and kernel comparison results

```python
fig, axes = plt.subplots(2, 4, figsize=(20, 10))

axes[0, 0].imshow(gray, cmap='gray')
axes[0, 0].set_title('Original Grayscale')
axes[0, 0].axis('off')

axes[0, 1].imshow(smoothed_fast, cmap='gray')
axes[0, 1].set_title('Smoothed (3×3 Averaging)')
axes[0, 1].axis('off')

axes[0, 2].imshow(noisy, cmap='gray')
axes[0, 2].set_title('Original + Noise')
axes[0, 2].axis('off')

axes[0, 3].imshow(noisy_smoothed, cmap='gray')
axes[0, 3].set_title('Noisy → Smoothed (3×3 Average)')
axes[0, 3].axis('off')

kernel_display = axes[1, 0].imshow(kernel_3x3, cmap='jet', interpolation='nearest')
axes[1, 0].set_title('3×3 Averaging Kernel')
plt.colorbar(kernel_display, ax=axes[1, 0], fraction=0.046, pad=0.04)

diff = cv2.absdiff(gray, smoothed_fast)
diff_display = axes[1, 1].imshow(diff, cmap='hot', vmin=0, vmax=50)
axes[1, 1].set_title('Difference Image')
plt.colorbar(diff_display, ax=axes[1, 1], fraction=0.046, pad=0.04)
```

### Explanation

This creates a visual comparison of:

* original image
* smoothed image
* noisy image
* noisy then smoothed image
* the averaging kernel
* difference image

### Difference image

`cv2.absdiff(gray, smoothed_fast)` shows how much smoothing changed the original image.

---

## 9. Compare manual, `filter2D`, and `blur` methods numerically

```python
print("Difference between manual and cv2.filter2D():")
diff_methods = cv2.absdiff(smoothed_manual, smoothed_filter2d)
print(f"Max difference: {diff_methods.max()}")
print(f"Mean difference: {diff_methods.mean():.6f}")

print("\nDifference between cv2.filter2D() and cv2.blur():")
diff_methods2 = cv2.absdiff(smoothed_filter2d, smoothed_fast)
print(f"Max difference: {diff_methods2.max()}")
print(f"Mean difference: {diff_methods2.mean():.6f}")
```

### Explanation

This checks whether the three implementations produce similar results.

### Why this matters

It validates that:

* manual implementation
* general convolution
* optimized blur function

are doing almost the same task.

---

## 10. Create synthetic test patterns for padding experiments

```python
def create_checkerboard(size=8, num_tiles=8):
    re = np.r_[num_tiles*[0,1]]
    ro = np.r_[num_tiles*[1,0]]
    board = np.vstack(num_tiles*(re, ro))
    board = cv2.resize(board.astype("uint8")*255, (size*20, size*20),
                      interpolation=cv2.INTER_NEAREST)
    return board

checkerboard = create_checkerboard()
cv2.imwrite("checkerboard.png", checkerboard)

rectangle = np.ones((300, 300), dtype=np.uint8) * 255
rectangle[50:250, 80:220] = 0
cv2.imwrite("rectangle.png", rectangle)
```

### Explanation

This creates simple artificial images for studying border effects.

### Why checkerboard and rectangle are useful

They contain sharp edges and clear structures, which make padding effects easy to observe.

---

## 11. Load test image and define averaging kernel

```python
gray = cv2.imread('rectangle.png', cv2.IMREAD_GRAYSCALE)

if gray is None:
    print("Error: Could not load image. Using generated checkerboard instead.")
    gray = create_checkerboard()

kernel = np.ones((3, 3), np.float32) / 9
pad = 1
```

### Explanation

This loads the test image in grayscale and prepares the 3×3 averaging kernel.

### Why `pad = 1`

A `3×3` kernel needs 1 pixel around the center on all sides.

---

## 12. Define different padding methods

```python
padding_modes = [
    ("BORDER_CONSTANT (Zero)",     cv2.BORDER_CONSTANT,     0),
    ("BORDER_REPLICATE (Edge)",    cv2.BORDER_REPLICATE,    None),
    ("BORDER_REFLECT",             cv2.BORDER_REFLECT,      None),
    ("BORDER_REFLECT_101",         cv2.BORDER_REFLECT_101,  None),
    ("BORDER_WRAP",                cv2.BORDER_WRAP,         None),
    ("No Padding (Valid)",         None,                    None),
]
```

### Explanation

This list stores several ways of handling borders during convolution.

### Meaning of each method

* **BORDER_CONSTANT** → fill borders with zeros
* **BORDER_REPLICATE** → copy edge values outward
* **BORDER_REFLECT** → mirror image at border
* **BORDER_REFLECT_101** → mirror without repeating edge pixel
* **BORDER_WRAP** → wrap around opposite edge
* **No Padding** → ignore borders, output becomes smaller

---

## 13. Apply convolution using different padding methods

```python
results = []

for name, border_type, const_value in padding_modes:
    if border_type is None:
        full = cv2.filter2D(gray, -1, kernel, borderType=cv2.BORDER_CONSTANT)
        cropped = full[pad:-pad, pad:-pad]
        results.append((name, cropped))

    elif border_type == cv2.BORDER_CONSTANT:
        padded = cv2.copyMakeBorder(gray, pad, pad, pad, pad,
                                   borderType=cv2.BORDER_CONSTANT,
                                   value=const_value)
        filtered_full = cv2.filter2D(padded, -1, kernel,
                                    borderType=cv2.BORDER_CONSTANT)
        filtered = filtered_full[pad:-pad, pad:-pad]
        results.append((name, filtered))

    else:
        filtered = cv2.filter2D(gray, -1, kernel, borderType=border_type)
        results.append((name, filtered))
```

### Explanation

This is the main comparison loop.

### Case 1: No padding

The filtered result is cropped so the borders are removed.

### Case 2: Constant padding

The image is explicitly padded using zeros before filtering.

### Case 3: Other border methods

OpenCV handles padding internally.

### Why this experiment is important

It shows that border handling changes the filtered image, especially near the edges.

---

## 14. Visualize padding results

```python
plt.figure(figsize=(15, 12))

plt.subplot(3, 3, 1)
plt.imshow(gray, cmap='gray')
plt.title('Original Image', fontsize=14, fontweight='bold')
plt.xlabel(f'{gray.shape[1]}×{gray.shape[0]}', fontsize=10)
plt.axis('off')

for i, (title, img_out) in enumerate(results, start=2):
    if len(img_out.shape) == 3:
        img_out = cv2.cvtColor(img_out, cv2.COLOR_BGR2GRAY)

    plt.subplot(3, 3, i)
    plt.imshow(img_out, cmap='gray')
    plt.title(title, fontsize=12)
    plt.xlabel(f'{img_out.shape[1]}×{img_out.shape[0]}', fontsize=10)
    plt.axis('off')

plt.suptitle('Convolution Results with Different Padding Methods',
            fontsize=16, fontweight='bold', y=1.02)
plt.tight_layout()
plt.show()
```

### Explanation

This shows the original image plus all padding results in one figure.

### Why this is useful

It makes visual comparison straightforward.

---

## 15. Compare border areas closely

```python
zoom_y, zoom_x, size = 20, 20, 40

zero_padded = cv2.copyMakeBorder(gray, pad, pad, pad, pad,
                                borderType=cv2.BORDER_CONSTANT, value=0)
zero_full = cv2.filter2D(zero_padded, -1, kernel,
                        borderType=cv2.BORDER_CONSTANT)
zero_result = zero_full[pad:-pad, pad:-pad]

reflect_result = cv2.filter2D(gray, -1, kernel,
                             borderType=cv2.BORDER_REFLECT_101)
```

### Explanation

This prepares close-up views of the border region.

### Why zoom in

Padding effects are most visible near the image edges and corners.

---

## 16. Print summary of padding behavior

```python
print("=" * 60)
print("PADDING METHODS SUMMARY")
print("=" * 60)

print("\n1. BORDER_CONSTANT (Zero Padding):")
print("   - Adds zeros around the image")
print("   - Creates dark borders in filtered output")

print("\n2. BORDER_REPLICATE (Edge Padding):")
print("   - Replicates the edge pixels")
print("   - Preserves border intensity")

print("\n3. BORDER_REFLECT / BORDER_REFLECT_101:")
print("   - Mirrors the image at borders")
print("   - Good for natural continuation")

print("\n4. BORDER_WRAP:")
print("   - Wraps around from opposite edge")
print("   - Useful for periodic patterns")

print("\n5. No Padding (Valid Convolution):")
print("   - Output is smaller than input")
print("   - Loses border information")
```

### Explanation

This summarizes the practical meaning of each padding method.

---

# LAB 05 — Mean, Gaussian, Sobel, and Median Filters

---

## 1. Mean filter implementation

```python
import cv2
import numpy as np

def mean_filter(image, kernel_size):
    padded_image = cv2.copyMakeBorder(image,
                                      kernel_size//2,
                                      kernel_size//2,
                                      kernel_size//2,
                                      kernel_size//2,
                                      cv2.BORDER_CONSTANT)

    filtered_image = np.zeros_like(image)

    for y in range(image.shape[0]):
        for x in range(image.shape[1]):
            window = padded_image[y:y+kernel_size, x:x+kernel_size]
            mean_value = np.mean(window)
            filtered_image[y, x] = mean_value

    return filtered_image
```

### Explanation

This function applies a mean filter manually.

### Main idea

For every pixel:

1. take a local window around it
2. compute the average
3. replace the center pixel with that average

### Effect

* smooths the image
* reduces mild noise
* blurs edges

---

## 2. Gaussian filter implementation

```python
import cv2
import numpy as np

def gaussian_kernel(size, sigma=1):
    """
    Create a 2D Gaussian kernel.
    size: kernel size (must be odd)
    sigma: standard deviation of the Gaussian
    """
    ax = np.arange(-size // 2 + 1., size // 2 + 1.)
    xx, yy = np.meshgrid(ax, ax)
    kernel = np.exp(-(xx**2 + yy**2) / (2. * sigma**2))
    kernel = kernel / np.sum(kernel)
    return kernel

def convolve(image, kernel):
    """
    Perform convolution between an image and a kernel
    """
    pad = kernel.shape[0] // 2
    padded_image = cv2.copyMakeBorder(image, pad, pad, pad, pad, cv2.BORDER_CONSTANT)
    output = np.zeros_like(image, dtype=np.float32)

    for y in range(image.shape[0]):
        for x in range(image.shape[1]):
            window = padded_image[y:y+kernel.shape[0], x:x+kernel.shape[1]]
            output[y, x] = np.sum(window * kernel)

    return output
```

### Explanation

This section creates a Gaussian kernel and performs convolution manually.

### Gaussian kernel

Unlike the mean filter, the Gaussian filter gives:

* larger weight to center pixels
* smaller weight to farther pixels

### Why Gaussian is better than simple averaging

It usually smooths the image more naturally and preserves edges better.

---

## 3. Sobel edge detection

```python
import cv2
import numpy as np

image_path = 'girl.jpg'
image = cv2.imread(image_path, cv2.IMREAD_GRAYSCALE)

sobel_x_kernel = np.array([[-1, 0, 1],
                           [-2, 0, 2],
                           [-1, 0, 1]], dtype=np.float32)

sobel_y_kernel = np.array([[-1, -2, -1],
                           [ 0,  0,  0],
                           [ 1,  2,  1]], dtype=np.float32)

sobel_x = cv2.filter2D(image, cv2.CV_64F, sobel_x_kernel)
sobel_y = cv2.filter2D(image, cv2.CV_64F, sobel_y_kernel)

gradient_magnitude = np.sqrt(sobel_x**2 + sobel_y**2)

normalized_gradient = cv2.normalize(gradient_magnitude, None, 0, 255, cv2.NORM_MINMAX)
normalized_gradient = normalized_gradient.astype(np.uint8)

cv2.imshow('Original Image', image)
cv2.imshow('Sobel Edge Detection', normalized_gradient)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### Explanation

This applies the Sobel operator to detect edges.

### Sobel X

Detects horizontal intensity changes.

### Sobel Y

Detects vertical intensity changes.

### Gradient magnitude

[
\sqrt{G_x^2 + G_y^2}
]

This gives the overall edge strength.

### Why normalize

Gradient values may not naturally lie between `0` and `255`, so they are scaled for display.

---

## 4. Median filter implementation

```python
import cv2
import numpy as np

def custom_median_filter(image, kernel_size):
    """
    Apply median filter to an image
    """
    height, width = image.shape
    filtered_image = np.zeros_like(image)
    kernel_half = kernel_size // 2

    padded_image = cv2.copyMakeBorder(image,
                                      kernel_half, kernel_half,
                                      kernel_half, kernel_half,
                                      cv2.BORDER_CONSTANT)

    for i in range(height):
        for j in range(width):
            window = []
            for ki in range(-kernel_half, kernel_half + 1):
                for kj in range(-kernel_half, kernel_half + 1):
                    window.append(padded_image[i + ki + kernel_half, j + kj + kernel_half])

            window.sort()
            median_value = window[len(window) // 2]
            filtered_image[i, j] = median_value

    return filtered_image
```

### Explanation

This manually applies a median filter.

### Main idea

Instead of averaging neighborhood values, it:

1. collects all values in the window
2. sorts them
3. picks the middle one

### Why median filter is important

It is very effective for **salt-and-pepper noise** and preserves edges better than mean filtering.

---

## 5. Morphological operations with structuring element

```python
import cv2
import numpy as np
from google.colab.patches import cv2_imshow

img_color = cv2.imread('lena.jpg')

if img_color is None:
    print("Error: Could not read image file. Please check the path and filename.")
else:
    img_gray = cv2.imread('lena.jpg', cv2.IMREAD_GRAYSCALE)

    ret, img_binary = cv2.threshold(img_gray, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)

    print(f"Otsu's calculated threshold value: {ret}")
    cv2.imwrite('lena_binary.jpg', img_binary)

kernel = np.ones((3,3),np.uint8)

eroded_image = cv2.erode(img_binary, kernel, iterations = 1)
dilated_image = cv2.dilate(img_binary, kernel, iterations = 1)
```

### Explanation

This section performs basic morphology after converting the image to binary.

### Otsu thresholding

Automatically chooses the threshold value.

### Erosion

Shrinks white regions.

### Dilation

Expands white regions.

### Structuring element

`kernel = np.ones((3,3), np.uint8)` defines the neighborhood shape used in morphological operations.

---

## 6. Create a synthetic noisy binary image for morphology comparison

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

img = np.zeros((200, 200), dtype=np.uint8)

cv2.ellipse(img, (100, 100), (60, 40), 0, 0, 360, 255, -1)

cv2.circle(img, (30, 30), 3, 255, -1)
cv2.circle(img, (170, 30), 3, 255, -1)
cv2.circle(img, (30, 170), 3, 255, -1)
cv2.circle(img, (170, 170), 3, 255, -1)

cv2.circle(img, (100, 100), 5, 0, -1)
cv2.circle(img, (80, 80), 3, 0, -1)
cv2.circle(img, (120, 120), 4, 0, -1)

cv2.line(img, (140, 60), (180, 40), 255, 2)
cv2.line(img, (60, 140), (20, 160), 255, 2)

cv2.circle(img, (140, 100), 8, 255, -1)
```

### Explanation

This generates a synthetic binary image containing:

* a main object
* isolated white noise
* black holes
* thin protrusions
* attached noise

### Why this is useful

It helps test how morphology behaves under different noise conditions.

---

# Final exam summary for Lab01 → Lab05

These labs mainly teach you these practical ideas:

### Lab01

* image loading
* BGR vs RGB
* RGB histogram plotting
* channel splitting
* channel statistics

### Lab02

* advanced histogram interpretation
* grayscale histogram
* CDF
* HSV histogram
* YUV brightness analysis
* exposure and brightness comparison

### Lab03

* histogram equalization
* grayscale equalization
* color equalization using YUV
* before/after histogram comparison

### Lab04

* smoothing with averaging filters
* manual convolution
* comparing `filter2D` and `blur`
* effect of noise reduction
* padding methods in convolution

### Lab05

* mean filter
* Gaussian filter
* Sobel filter
* median filter
* basic morphology with erosion and dilation

---

