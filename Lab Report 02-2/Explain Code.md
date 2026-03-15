
---

# Complete Full Explanation of the Code

---

## 1. Install required libraries

```python
!pip install mediapipe opencv-python --quiet
```

### Explanation

This line installs external Python packages in the notebook environment.

### What each part means

* `!`
  Used in Jupyter Notebook or Google Colab to run a **terminal/shell command**.

* `pip install`
  Installs Python packages.

* `mediapipe`
  A computer vision library developed by Google.
  It is commonly used for tasks such as:

  * face detection
  * hand tracking
  * pose estimation

* `opencv-python`
  Installs OpenCV, which is the main library used in this notebook for image processing.

* `--quiet`
  Reduces the amount of installation output shown on the screen.

### Why this is used

Before using OpenCV functions like image loading, color conversion, histogram calculation, and channel splitting, the package must be installed.

### Important note

In this notebook, **MediaPipe is installed, but it is not actually used later in the code**.
The practical mainly uses:

* OpenCV
* NumPy
* Matplotlib

---

## 2. Import libraries and configure plotting

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

This block imports the required libraries and sets display options for plots and images.

### Line-by-line explanation

#### `from io import IncrementalNewlineDecoder`

Imports `IncrementalNewlineDecoder` from Python’s built-in `io` module.

### Important note

This import is **not used later in the notebook**.
So it can be removed without changing the output.

---

#### `import cv2`

Imports OpenCV using the alias `cv2`.

It is used for:

* reading images
* color conversion
* histogram calculation
* channel splitting

---

#### `import numpy as np`

Imports NumPy as `np`.

It is used for:

* array operations
* mean calculations
* standard deviation calculations
* weighted averages

---

#### `import matplotlib.pyplot as plt`

Imports the plotting part of Matplotlib as `plt`.

It is used for:

* displaying images
* plotting histograms
* adding labels, titles, and grids

---

#### `%matplotlib inline`

This is a **Jupyter magic command**.

It tells the notebook to display plots directly inside the notebook output.

---

#### `plt.rcParams['figure.figsize'] = (10,6)`

Sets the default figure size for plots.

That means graphs will normally appear with width `10` and height `6`.

---

#### `plt.rcParams['image.cmap'] = 'gray'`

Sets the default colormap for image display to grayscale.

This is useful when displaying single-channel images such as grayscale, red-channel, green-channel, or blue-channel matrices.

### Why this block is important

This block prepares the notebook so that image processing and plotting work correctly.

---

## 3. Read image and convert directly to RGB

```python
img_bgr = cv2.imread('Girl.jpg')

if img_bgr is None:
    raise FileNotFoundError("❌ Could not find 'Girl.jpg'. Please upload it to the Colab working directory.")

img_bgr = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)

plt.figure(figsize=(6,6))
plt.imshow(img_bgr)
plt.title('RGB Image(original)')
```

### Explanation

This block reads the image from disk, checks whether it was loaded successfully, converts it to RGB, and displays it.

### Line-by-line explanation

#### `img_bgr = cv2.imread('Girl.jpg')`

Reads the image file named `Girl.jpg`.

### Important concept

OpenCV reads color images in **BGR format**, not RGB.

That means the channel order is:

* Blue
* Green
* Red

---

#### `if img_bgr is None:`

Checks whether the image was loaded correctly.

If the file does not exist or cannot be read, OpenCV returns `None`.

---

#### `raise FileNotFoundError(...)`

Stops execution and shows a clear error message.

### Why this is useful

It prevents later code from failing with confusing errors.

---

#### `img_bgr = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)`

Converts the image from **BGR format to RGB format**.

### Why this is needed

Because:

* OpenCV loads color images as **BGR**
* Matplotlib expects images in **RGB**

If you skip this conversion, the image colors will look wrong.

---

#### `plt.figure(figsize=(6,6))`

Creates a figure of width `6` and height `6`.

---

#### `plt.imshow(img_bgr)`

Displays the image.

### Important note

Although the variable name is still `img_bgr`, the image data is now actually in **RGB format**.

A better variable name would be `img_rgb`.

---

#### `plt.title('RGB Image(original)')`

Adds a title above the displayed image.

---

## 4. Read image and correctly convert to RGB

```python
img = cv2.imread('Girl.jpg')
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB) #converted the image to RGB

plt.figure(figsize=(12,4))
plt.imshow(img_rgb)
plt.title('RGB Image(original)')
```

### Explanation

This is a cleaner version of the previous section.

### Line-by-line explanation

#### `img = cv2.imread('Girl.jpg')`

Loads the image in BGR format.

---

#### `img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)`

Converts the image from BGR to RGB.

---

#### `plt.figure(figsize=(12,4))`

Creates a figure with width `12` and height `4`.

---

#### `plt.imshow(img_rgb)`

Displays the corrected RGB image.

---

#### `plt.title('RGB Image(original)')`

Adds a title to the image.

### Why this block is important

This is the correct workflow when:

* OpenCV is used for reading
* Matplotlib is used for displaying

---

## 5. Display original image without conversion

```python
img = cv2.imread('Girl.jpg') #created a variable and imported the image

plt.figure(figsize=(12,4)) #defined the figure size of the image
plt.imshow(img) #to show the image
plt.title('GBR Image')
```

### Explanation

This block loads and displays the image **without converting from BGR to RGB**.

### What happens here

* `cv2.imread('Girl.jpg')` reads the image in BGR format
* `plt.imshow(img)` displays it as though it were RGB
* the colors therefore appear incorrect

### Why the colors look wrong

Because Matplotlib expects the channel order:

* Red
* Green
* Blue

But OpenCV provides:

* Blue
* Green
* Red

So the red and blue channels are swapped.

### About the title

```python
plt.title('GBR Image')
```

This appears to be a typo.

It should probably be:

```python
plt.title('BGR Image')
```

not `GBR Image`.

### Why this block is useful

This block demonstrates an important practical point:

> If an OpenCV image is shown directly with Matplotlib without BGR-to-RGB conversion, the colors will display incorrectly.

---

# RGB HISTOGRAM ANALYSIS SECTION

---

## 6. Define colors and channel names, then plot RGB histograms

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

This block calculates and displays histograms for the Red, Green, and Blue channels of the image.

---

## 6.1 `colors = ('r','g','b')`

Creates a tuple of plotting colors.

* `'r'` = red
* `'g'` = green
* `'b'` = blue

These are used to draw the histogram lines.

---

## 6.2 `channel_names = ['Red','Green','Blue']`

Stores the names of the three channels.

These names appear in the plot legend.

---

## 6.3 `plt.figure(figsize=(10,6))`

Creates a new figure for plotting the histograms.

---

## 6.4 `for i, col in enumerate(colors):`

Loops through the three color channels.

### What `enumerate(colors)` gives

* `i = 0`, `col = 'r'`
* `i = 1`, `col = 'g'`
* `i = 2`, `col = 'b'`

Here:

* `i` is the channel index
* `col` is the plotting color

---

## 6.5 `hist = cv2.calcHist([img_rgb], [i], None, [256], [0,256])`

Calculates the histogram for one color channel.

### Breakdown of the parameters

#### `[img_rgb]`

The input image list.

---

#### `[i]`

The channel index.

Since the image is RGB:

* `0` = Red
* `1` = Green
* `2` = Blue

---

#### `None`

No mask is used.

That means the histogram is calculated over the **entire image**.

---

#### `[256]`

Number of bins.

This is `256` because image intensity values range from `0` to `255`.

---

#### `[0,256]`

Intensity range.

This tells OpenCV to calculate the histogram over the full intensity range.

---

## 6.6 `plt.plot(hist, color=col, label=f'{channel_names[i]} Channel')`

Plots the histogram as a line graph.

Each channel uses its matching display color.

---

## 6.7 `plt.xlim([0,256])`

Sets the x-axis range from `0` to `256`.

This matches the full pixel intensity range.

---

## 6.8 Plot formatting

```python
plt.title('RGB Channel Histograms - Girl.png')
plt.xlabel('Pixel Intensity (0–255)')
plt.ylabel('Frequency')
plt.legend()
plt.grid(alpha=0.3)
plt.show()
```

### Meaning

* `title` gives the graph a heading
* `xlabel` labels the x-axis
* `ylabel` labels the y-axis
* `legend` identifies each line
* `grid` improves readability
* `show()` displays the final plot

---

## 6.9 What is a histogram?

A histogram is a graph that shows how pixel intensities are distributed.

### X-axis

Pixel intensity values from `0` to `255`

### Y-axis

The number of pixels having each intensity value

### Why histograms are useful

They help analyze:

* brightness
* contrast
* channel dominance
* color balance

---

# REPEATED SECOND SECTION OF THE NOTEBOOK

The notebook repeats the practical in a cleaner step-by-step form.

---

## 7. Import libraries again

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt
```

### Explanation

This block imports the same libraries again.

### Important note

This is not wrong, but it is **redundant** because the libraries were already imported earlier.

---

## 8. Load image safely

```python
image = cv2.imread('Girl.jpg')

if image is None:
    raise FileNotFoundError("❌ Could not find 'Girl.jpg'. Please upload it to the Colab working directory.")
```

### Explanation

This block loads the image and checks whether it exists.

### Line-by-line explanation

#### `image = cv2.imread('Girl.jpg')`

Reads the image file.

---

#### `if image is None:`

Checks whether the image was loaded successfully.

---

#### `raise FileNotFoundError(...)`

Stops the program with a clear error if the file is missing.

### Why this is good practice

It prevents invalid image data from being used in later steps.

---

## 9. Convert BGR to RGB

```python
image_rgb = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
```

### Explanation

Converts the image from BGR to RGB.

### Why this is needed

Because:

* OpenCV loads images as BGR
* Matplotlib displays correctly in RGB

---

## 10. Display the RGB image

```python
plt.figure(figsize=(6,6))
plt.imshow(image_rgb)
plt.title("Original Image - Girl.png (RGB)")
plt.axis('off')
plt.show()
```

### Explanation

This block displays the correctly converted RGB image.

### Line-by-line explanation

#### `plt.figure(figsize=(6,6))`

Creates a square figure.

---

#### `plt.imshow(image_rgb)`

Displays the RGB image.

---

#### `plt.title("Original Image - Girl.png (RGB)")`

Adds a title.

---

#### `plt.axis('off')`

Removes axis lines and tick marks.

---

#### `plt.show()`

Displays the final output.

---

## 11. Plot RGB histograms again

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

This is the same histogram process again, but now using `image_rgb`.

It shows the histogram of:

* red channel
* green channel
* blue channel

---

# STATISTICAL ANALYSIS SECTION

---

## 12. Compute mean and standard deviation using histogram weights

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

This block calculates statistical measures for each RGB channel.

---

## 12.1 `hist = hist.flatten()`

Converts the histogram from shape `(256,1)` into a 1D array.

### Why this is needed

Mathematical operations are easier with a flat 1D array.

---

## 12.2 `mean_intensity = np.average(np.arange(256), weights=hist)`

Calculates the **weighted mean intensity**.

### Meaning

This gives the average brightness of the channel.

### Interpretation

* higher mean = brighter channel
* lower mean = darker channel

---

## 12.3 Standard deviation calculation

```python
std_intensity = np.sqrt(np.average((np.arange(256) - mean_intensity) ** 2, weights=hist))
```

### Explanation

Calculates the weighted standard deviation of the intensity distribution.

### Meaning

It measures how spread out the intensity values are.

### Interpretation

* low standard deviation = pixel values are similar
* high standard deviation = pixel values vary more

This gives an idea about contrast variation in that channel.

---

## 12.4 Printing results

```python
print(f" {name} Channel Analysis:")
print(f"   Mean Intensity     : {mean_intensity:.2f}")
print(f"   Standard Deviation : {std_intensity:.2f}")
```

### Explanation

Prints the channel name and the calculated statistical values.

### What `:.2f` means

Formats the values to 2 decimal places.

---

# SPLIT INTO CHANNELS SECTION

---

## 13. Split RGB image into three channels

```python
r_channel, g_channel, b_channel = cv2.split(image_rgb)
```

### Explanation

This separates the RGB image into three single-channel images.

### Output

* `r_channel` contains red intensity values
* `g_channel` contains green intensity values
* `b_channel` contains blue intensity values

### Why this is useful

It allows:

* separate analysis of each channel
* separate histogram calculation
* independent display of each color component

---

## 14. Print shapes of image and channels

```python
print(f"Original RGB Image Shape : {image_rgb.shape}")
print(f"Red Channel Shape        : {r_channel.shape}")
print(f"Green Channel Shape      : {g_channel.shape}")
print(f"Blue Channel Shape       : {b_channel.shape}")
```

### Explanation

This block prints the dimensions of the original RGB image and each separated channel.

### Example

If the RGB image shape is:

```python
(512, 512, 3)
```

then each channel shape becomes:

```python
(512, 512)
```

### Why this matters

It helps you understand how a color image is stored internally.

---

# HISTOGRAMS OF SEPARATE CHANNELS

---

## 15. Calculate histograms for each split channel

```python
hist_r = cv2.calcHist([r_channel], [0], None, [256], [0,256])
hist_g = cv2.calcHist([g_channel], [0], None, [256], [0,256])
hist_b = cv2.calcHist([b_channel], [0], None, [256], [0,256])
```

### Explanation

This calculates a histogram for each separated channel.

### Why the channel index is `[0]` here

Because each of `r_channel`, `g_channel`, and `b_channel` is already a **single-channel image**.

So the only available channel inside each one is channel `0`.

---

## 16. Plot separate channel histograms

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

This block plots the three already-computed channel histograms together.

### Why this is useful

It helps compare how much each color contributes to the image.

---

# DISPLAY THE THREE CHANNELS VISUALLY

---

## 17. Show red, green, and blue channels

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

This block displays the three separated channels as images.

### What these images represent

Bright regions mean stronger intensity for that channel at that pixel position.

### Why `cmap='Reds'`, `cmap='Greens'`, `cmap='Blues'` is used

Because each channel is a single-channel image.
Using a matching colormap makes the visualization easier to understand.

---

# CHANNEL STATISTICS FUNCTION

---

## 18. Define a reusable function for channel statistics

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
```

### Explanation

This function calculates basic statistics for one color channel.

---

## 18.1 Function parameters

### `channel`

The input image data for one channel.

### `name`

The channel name such as `"Red"` or `"Green"`.

### `color`

This parameter is passed, but it is **not actually used inside the function**.

So this parameter is unnecessary in the current version.

---

## 18.2 `mean = np.mean(channel)`

Calculates the average intensity directly from the channel values.

### Meaning

This is the average brightness of the channel.

---

## 18.3 `std = np.std(channel)`

Calculates the standard deviation of the pixel values.

### Meaning

This shows how much the intensity values vary.

---

## 18.4 Find the peak intensity

```python
hist = cv2.calcHist([channel], [0], None, [256], [0, 256])
peak = np.argmax(hist)
```

### Explanation

* `hist` counts how many times each intensity appears
* `np.argmax(hist)` finds the intensity with the highest count

### Meaning of peak intensity

Peak intensity is the pixel value that appears most frequently.

---

## 18.5 Formatted output

```python
print(f"{name:5} channel | Mean: {mean:6.1f} | Std: {std:5.1f} | Peak Intensity: {peak:3}")
```

### Explanation

Prints the result in a neatly aligned format.

---

## 18.6 Return values

```python
return mean, std, peak
```

Returns the calculated statistics so they can be stored and reused later.

---

## 19. Call the function for each channel

```python
print("\n" + "="*70)
print("RGB CHANNEL STATISTICS")
print("-"*70)

r_stats = channel_stats(r_channel, "Red", "r")
g_stats = channel_stats(g_channel, "Green", "g")
b_stats = channel_stats(b_channel, "Blue", "b")

print("="*70)
```

### Explanation

This block prints a heading and then runs the function for:

* red channel
* green channel
* blue channel

Each result contains:

* mean
* standard deviation
* peak intensity

---

# ADVANCED VISUAL ANALYSIS SECTION

---

## 20. Plot channel-only images with histograms and mean lines

```python
mean_r = r_stats[0]
mean_g = g_stats[0]
mean_b = b_stats[0]

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

plt.tight_layout()
plt.show()
```

### Explanation

This block creates a more advanced visualization.

### Top row

Shows the **channel-only images**.

### Bottom row

Shows the histogram for each channel.

### Extra features added

* shaded intensity zones:

  * low intensity
  * mid intensity
  * high intensity
* histogram area filling
* vertical mean line

### Why this is useful

This makes the analysis easier and more professional for presentation.

### Important note

The notebook uses `img_rgb` here instead of `image_rgb`.
That works only because `img_rgb` was created earlier.

---

## 21. Overlay all three histograms in one plot

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

plt.title('■ RGB Channels Overlay Histogram - Lena Image')
plt.xlabel('Pixel Intensity (0-255)')
plt.ylabel('Pixel Frequency')
plt.grid(True, alpha=0.2)
plt.xlim(0, 255)
plt.legend()
plt.tight_layout()
plt.show()
```

### Explanation

This block overlays the histograms of all three channels in one graph.

### Why this is useful

It allows quick comparison of channel distributions in one place.

### Important note

The title says **Lena Image**, but the actual file used is `Girl.jpg`.

So the title is inconsistent with the image being processed.

---

# GRAYSCALE ANALYSIS SECTION

---

## 22. Convert RGB image to grayscale and plot histogram

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

This block converts the RGB image into grayscale and then plots the grayscale histogram.

### What is grayscale?

A grayscale image has only one channel.

Each pixel contains only brightness information.

### Why this is useful

Grayscale simplifies image analysis and is commonly used for:

* thresholding
* edge detection
* brightness analysis

---

## 23. Plot histogram and cumulative distribution function

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

This block calculates and displays the **CDF** together with the histogram.

### What is CDF?

CDF stands for **Cumulative Distribution Function**.

It shows the cumulative total of pixels up to each intensity level.

### Why this is useful

CDF is important in topics such as:

* histogram equalization
* intensity analysis
* contrast enhancement

---

# HSV COLOR SPACE SECTION

---

## 24. Convert image to HSV and plot H, S, V histograms

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

This block converts the image from BGR to HSV and plots the histogram for each HSV channel.

### What HSV means

* **H** = Hue
* **S** = Saturation
* **V** = Value

### Why HSV is useful

HSV separates:

* color information
* color purity
* brightness

This is useful in many computer vision tasks such as color segmentation.

### Important note

Here `img_bgr` was already converted to RGB earlier in the notebook, so the variable name is misleading.
A safer approach would be to keep a true BGR copy separate.

---

# YUV COLOR SPACE SECTION

---

## 25. Convert image to YUV and compare Y channel with grayscale

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

This block converts the image to YUV and compares the histogram of:

* grayscale image
* Y channel

### What YUV means

* **Y** = luminance or brightness
* **U** and **V** = chrominance information

### Why this comparison is useful

Both grayscale and Y channel represent brightness-related information, but they are not always identical.

---

# EXPOSURE AND BRIGHTNESS ANALYSIS SECTION

---

## 26. Create exposure variants

```python
def adjust_exposure(img, factor):
    """Multiply pixel values (simulate exposure change)."""
    return np.clip(img * factor, 0, 255).astype('uint8')

good = gray.copy()
high = adjust_exposure(gray, 2.0)
low  = adjust_exposure(gray, 0.4)
```

### Explanation

This block creates different exposure versions of a grayscale image.

### Meaning of each one

* `good` = original image
* `high` = overexposed/brighter image
* `low` = underexposed/darker image

### Why `np.clip()` is used

Pixel values must remain in the valid range:

* minimum = `0`
* maximum = `255`

---

## 27. Compute histograms for exposure images

```python
def compute_hist(img):
    return cv2.calcHist([img], [0], None, [256], [0, 256])

hist_good = compute_hist(good)
hist_high = compute_hist(high)
hist_low  = compute_hist(low)
```

### Explanation

This function calculates the histogram for a grayscale image.

It is then used for:

* normal exposure
* high exposure
* low exposure

---

## 28. Create brightness variants by adding a constant

```python
def adjust_brightness(img, delta):
    return np.clip(img.astype(np.int16) + delta, 0, 255).astype('uint8')

good = gray.copy()
high = adjust_brightness(gray, +100)
low = adjust_brightness(gray, -100)
```

### Explanation

This version changes brightness by **adding or subtracting a constant value**.

### Difference between exposure and brightness

* **Exposure adjustment** multiplies pixel values
* **Brightness adjustment** adds a constant value

Both affect image appearance, but in slightly different ways.

---

# IMPORTANT CONCEPTS BEHIND THE CODE

---

## A. What is `cv2.imread()`?

Used to read an image from a file.

```python
img = cv2.imread('Girl.jpg')
```

### Returns

* a NumPy array if successful
* `None` if unsuccessful

---

## B. What is `cv2.cvtColor()`?

Used to convert an image from one color space to another.

Examples:

```python
cv2.COLOR_BGR2RGB
cv2.COLOR_RGB2GRAY
cv2.COLOR_BGR2HSV
cv2.COLOR_BGR2YUV
```

---

## C. What is `cv2.calcHist()`?

Used to calculate the histogram of an image or channel.

General form:

```python
cv2.calcHist(images, channels, mask, histSize, ranges)
```

### Parameters

* `images` = input image list
* `channels` = selected channel index
* `mask` = region mask or `None`
* `histSize` = number of bins
* `ranges` = intensity/value range

---

## D. What is a color channel?

A color image usually has three channels:

* Red
* Green
* Blue

Each channel stores intensity values from `0` to `255`.

---

## E. What is mean intensity?

The average brightness of a channel or grayscale image.

---

## F. What is standard deviation?

It measures how much the intensity values spread around the mean.

* low standard deviation = low variation
* high standard deviation = higher variation

---

## G. What is peak intensity?

The intensity value that appears most frequently in the image or channel.

---

## H. What is grayscale?

A single-channel image containing only brightness information.

---

## I. What is HSV?

A color space based on:

* Hue
* Saturation
* Value

---

## J. What is YUV?

A color space that separates:

* brightness
* color information

---

# WHAT THIS NOTEBOOK IS TRYING TO TEACH

This practical mainly teaches:

1. how OpenCV loads images
2. why BGR and RGB are different
3. how to display images correctly
4. how to calculate histograms
5. how to compute channel statistics
6. how to split an image into separate channels
7. how to analyze grayscale intensity
8. how to use CDF
9. how to explore other color spaces like HSV and YUV
10. how brightness and exposure affect histograms

---

# SMALL ISSUES IN THE NOTEBOOK

---

## 1. `IncrementalNewlineDecoder` is unused

```python
from io import IncrementalNewlineDecoder
```

This import is present, but not used later.

---

## 2. `mediapipe` is installed but unused

The package is installed, but MediaPipe functions are not used in the notebook.

---

## 3. Typo in image title

```python
plt.title('GBR Image')
```

This should likely be:

```python
plt.title('BGR Image')
```

---

## 4. Variable naming can be improved

This is misleading:

```python
img_bgr = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
```

A clearer version is:

```python
img_bgr = cv2.imread('Girl.jpg')
img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
```

---

## 5. The function parameter `color` is unused

```python
def channel_stats(channel, name, color):
```

The `color` parameter is passed but not used inside the function.

---

## 6. Some titles say “Lena Image”

But the actual file used is `Girl.jpg`.

So some labels were copied from another notebook and not fully updated.

---

# PRACTICAL EXAM VIVA ANSWERS YOU SHOULD REMEMBER

## Why do we convert BGR to RGB?

Because OpenCV reads images in BGR format, but Matplotlib displays correctly only in RGB format.

---

## What does a histogram show?

It shows the distribution of pixel intensity values.

---

## Why do we split channels?

To analyze each color component separately.

---

## What is mean intensity?

The average brightness of a channel.

---

## What is standard deviation?

A measure of how much intensity values vary.

---

## What is peak intensity?

The most frequent intensity value in a channel.

---

## What is CDF?

The cumulative distribution of intensity values.

---

## Why use grayscale?

Because it simplifies processing by reducing the image to one brightness channel.

---

## Why use HSV?

Because it separates color and brightness information more clearly.

---

## Why use YUV?

Because it separates luminance from chrominance.

---

# EASY FLOW OF THE WHOLE LAB

```python
Install packages
→ Import libraries
→ Read image
→ Check if image exists
→ Convert BGR to RGB
→ Display image
→ Calculate RGB histograms
→ Compute channel statistics
→ Split image into channels
→ Display channels
→ Plot separate histograms
→ Convert to grayscale
→ Plot grayscale histogram
→ Compute CDF
→ Convert to HSV and YUV
→ Compare brightness information
→ Create high/low brightness or exposure versions
→ Analyze histogram changes
```

---

