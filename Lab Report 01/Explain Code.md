
---

# Complete Full Explanation of the Code

---

## 1. Install required libraries

```python
!pip install mediapipe opencv-python --quiet
```

### Explanation

This line installs external Python packages.

### What each part means

* `!`
  Used in Jupyter/Colab to run a **shell command**.
* `pip install`
  Installs Python packages.
* `mediapipe`
  A library used for computer vision tasks like face, hand, and pose detection.
* `opencv-python`
  The OpenCV package used for image processing.
* `--quiet`
  Reduces installation output and makes the notebook cleaner.

### Why this is used

Before using OpenCV or MediaPipe, the packages must be installed in the notebook environment.

### Important note

In this notebook, **MediaPipe is installed but not actually used later**. Only OpenCV, NumPy, and Matplotlib are used in the main image-processing steps.

---

## 2. Import libraries

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

This block imports the required libraries and configures plotting.

### Line-by-line explanation

#### `from io import IncrementalNewlineDecoder`

Imports a class from Python’s built-in `io` module.

* In this notebook, it is **not used later**
* It can be removed safely

#### `import cv2`

Imports OpenCV with the alias `cv2`.

Used for:

* reading images
* converting color spaces
* splitting channels
* calculating histograms

#### `import numpy as np`

Imports NumPy as `np`.

Used for:

* numerical operations
* mean calculation
* standard deviation
* weighted averages

#### `import matplotlib.pyplot as plt`

Imports Matplotlib plotting module as `plt`.

Used for:

* displaying images
* drawing histograms
* adding titles, labels, legends

#### `%matplotlib inline`

This is a Jupyter magic command.

It tells the notebook to display plots **inside the notebook output**.

#### `plt.rcParams['figure.figsize'] = (10,6)`

Sets the default figure size for plots to width 10 and height 6.

#### `plt.rcParams['image.cmap'] = 'gray'`

Sets the default colormap for grayscale image display to gray.

### Why this block is important

This prepares the environment so image processing and plotting can be done smoothly.

---

## 3. Read image and convert directly to RGB

```python
img_bgr = cv2.imread('Girl.jpg')
img_bgr = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)

plt.figure(figsize=(6,6))
plt.imshow(img_bgr)
plt.title('RGB Image(original)')
```

### Explanation

This block loads the image and converts it from OpenCV’s default color format to RGB.

### Line-by-line explanation

#### `img_bgr = cv2.imread('Girl.jpg')`

Reads the image file named `Girl.jpg`.

* `cv2.imread()` loads the image as a NumPy array
* OpenCV reads color images in **BGR format**, not RGB

#### `img_bgr = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)`

Converts the image from **BGR** to **RGB**.

### Why is this needed?

Because:

* OpenCV uses **BGR**
* Matplotlib expects **RGB**

If you display an OpenCV image directly with Matplotlib without conversion, the colors will appear wrong.

#### `plt.figure(figsize=(6,6))`

Creates a new plotting window of size 6 × 6.

#### `plt.imshow(img_bgr)`

Displays the image.

At this point, the variable name is still `img_bgr`, but the image data has already been converted to RGB.

#### `plt.title('RGB Image(original)')`

Adds a title to the displayed image.

### Better variable naming

A clearer version would be:

```python
img_bgr = cv2.imread('Girl.jpg')
img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
```

Because after conversion, the image is no longer BGR.

---

## 4. Display original image without conversion

```python
img = cv2.imread('Girl.jpg') #created a variable and imported the image

plt.figure(figsize=(12,4)) #defined the figure size of the image
plt.imshow(img) #to show the image
plt.title('GBR Image')
```

### Explanation

This block reads and displays the image **without converting BGR to RGB**.

### What happens here

* `cv2.imread('Girl.jpg')` loads the image in BGR format
* `plt.imshow(img)` displays it as if it were RGB
* therefore, the image colors appear incorrect

### About the title

```python
plt.title('GBR Image')
```

This seems to be a typo.

It should likely be:

* `BGR Image`

not `GBR Image`

### Why this block matters

It helps demonstrate an important concept:

> OpenCV loads images as **BGR**, but Matplotlib displays images correctly only when they are in **RGB**.

So this block is useful to show the “wrong color display” case.

---

## 5. Read image and correctly convert to RGB

```python
img = cv2.imread('Girl.jpg')
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB) #converted the image to RGB

plt.figure(figsize=(12,4))
plt.imshow(img_rgb)
plt.title('RGB Image(original)')
```

### Explanation

This is the corrected version of the previous step.

### Line-by-line explanation

#### `img = cv2.imread('Girl.jpg')`

Reads the original image in BGR format.

#### `img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)`

Converts the image into RGB format.

#### `plt.figure(figsize=(12,4))`

Creates a wide figure.

#### `plt.imshow(img_rgb)`

Displays the correctly converted RGB image.

#### `plt.title('RGB Image(original)')`

Adds a title.

### Why this step is important

This is the standard correct workflow for showing OpenCV images with Matplotlib.

---

# RGB HISTOGRAM ANALYSIS SECTION

---

## 6. Define colors and channel names, then plot histograms

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

This code calculates and displays histograms for the Red, Green, and Blue channels.

---

## 6.1 `colors = ('r','g','b')`

Creates a tuple containing plotting colors.

* `'r'` = red
* `'g'` = green
* `'b'` = blue

These colors are used when drawing histogram lines.

---

## 6.2 `channel_names = ['Red','Green','Blue']`

Stores the names of the channels.

Used for labels in the legend.

---

## 6.3 `plt.figure(figsize=(10,6))`

Creates a figure for histogram plotting.

---

## 6.4 `for i, col in enumerate(colors):`

Loops through the three channels.

`enumerate(colors)` produces:

* `i = 0`, `col = 'r'`
* `i = 1`, `col = 'g'`
* `i = 2`, `col = 'b'`

Here:

* `i` is the channel index
* `col` is the color used for plotting

---

## 6.5 `hist = cv2.calcHist([img_rgb], [i], None, [256], [0,256])`

This is the key histogram calculation line.

### Breakdown of parameters

#### `[img_rgb]`

Input image list

#### `[i]`

Channel index:

* `0` = Red
* `1` = Green
* `2` = Blue

Since the image is RGB, index order is RGB.

#### `None`

No mask is applied.
This means the histogram is calculated for the **entire image**.

#### `[256]`

Number of bins = 256

Because pixel intensity values range from 0 to 255.

#### `[0,256]`

Intensity range from 0 up to 256.

---

## 6.6 `plt.plot(hist, color=col, label=f'{channel_names[i]} Channel')`

Plots the histogram curve using the appropriate color and label.

Example:

* red line for red channel
* green line for green channel
* blue line for blue channel

---

## 6.7 `plt.xlim([0,256])`

Sets the x-axis range from 0 to 256.

This matches the pixel intensity range.

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

* title describes the graph
* x-axis = intensity value
* y-axis = number of pixels with that intensity
* legend shows which line belongs to which channel
* grid makes the plot easier to read
* show renders the final graph

---

## 6.9 What is a histogram?

A histogram is a graph showing how pixel values are distributed.

### X-axis

Pixel intensity values from 0 to 255

### Y-axis

How many pixels have that intensity value

### Usefulness

Histograms help analyze:

* brightness
* contrast
* color distribution
* image quality

---

# REPEATED SECOND SECTION OF THE NOTEBOOK

Your notebook repeats the same concept in a cleaner step-by-step style. I will explain that too.

---

## 7. Import libraries again

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt
```

### Explanation

This repeats the imports for the second section.

It is not wrong, but it is redundant if already imported earlier.

---

## 8. Load image

```python
image = cv2.imread('Girl.jpg')

if image is None:
    raise FileNotFoundError("❌ Could not find 'Girl.jpg'. Please upload it to the Colab working directory.")
```

### Explanation

This block loads the image safely.

### Line-by-line explanation

#### `image = cv2.imread('Girl.jpg')`

Reads the image.

#### `if image is None:`

Checks whether the file was successfully loaded.

OpenCV returns `None` if:

* the image file is missing
* the path is wrong
* the file cannot be read

#### `raise FileNotFoundError(...)`

Stops execution and shows a clear error message.

### Why this is good practice

It prevents later errors such as passing `None` into `cv2.cvtColor()`.

---

## 9. Convert BGR to RGB

```python
image_rgb = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
```

### Explanation

This converts the loaded image into RGB format for correct display and analysis.

### Why needed

* OpenCV reads BGR
* Matplotlib and most human interpretation expect RGB

---

## 10. Display the RGB image

```python
plt.figure(figsize=(6,6))
plt.imshow(image_rgb)
plt.title("Original Image - Girl.png (RGB)")
plt.axis('off')
plt.show()
```

### Line-by-line explanation

#### `plt.figure(figsize=(6,6))`

Creates a square plotting window.

#### `plt.imshow(image_rgb)`

Displays the RGB image.

#### `plt.title("Original Image - Girl.png (RGB)")`

Adds a descriptive title.

#### `plt.axis('off')`

Removes axis lines and tick labels.

This makes the image display cleaner.

#### `plt.show()`

Renders the figure.

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

This is the same idea as before, but now based on the variable `image_rgb`.

It plots the histogram of:

* red channel
* green channel
* blue channel

This section is cleaner and is likely the main part intended for the lab.

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

This loop calculates statistical properties of each RGB channel.

---

## 12.1 `for i, name in enumerate(channel_names):`

Loops over:

* channel index `i`
* channel name `name`

So it processes:

* Red
* Green
* Blue

---

## 12.2 `hist = cv2.calcHist([image_rgb], [i], None, [256], [0,256])`

Calculates the histogram for one channel.

---

## 12.3 `hist = hist.flatten()`

Converts the histogram from a 2D array shape like `(256,1)` into a 1D array shape `(256,)`.

### Why needed

It makes the histogram easier to use in numerical calculations.

---

## 12.4 `mean_intensity = np.average(np.arange(256), weights=hist)`

### What this does

Calculates the weighted mean intensity.

#### `np.arange(256)`

Creates intensity values:
`0, 1, 2, ..., 255`

#### `weights=hist`

Uses the histogram frequencies as weights.

### Meaning

This computes the average pixel intensity for the channel.

### Interpretation

* higher mean → brighter channel
* lower mean → darker channel

---

## 12.5 Standard deviation calculation

```python
std_intensity = np.sqrt(np.average((np.arange(256) - mean_intensity) ** 2, weights=hist))
```

### What this does

Calculates the weighted standard deviation of the channel intensities.

### Meaning

It measures how spread out the intensity values are.

### Interpretation

* low standard deviation → pixel values are clustered near the mean
* high standard deviation → pixel values vary more, indicating stronger contrast or wider intensity spread

---

## 12.6 Printing results

```python
print(f" {name} Channel Analysis:")
print(f"   Mean Intensity     : {mean_intensity:.2f}")
print(f"   Standard Deviation : {std_intensity:.2f}")
```

### Explanation

Prints each channel’s analysis in a readable format.

The `:.2f` means values are shown with 2 decimal places.

---

## 12.7 Why this method is interesting

This computes statistics **from the histogram itself**, not directly from the image array.

That is mathematically valid and shows a good understanding of histogram-based image analysis.

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

* `r_channel` contains only red intensities
* `g_channel` contains only green intensities
* `b_channel` contains only blue intensities

### Why useful

It allows:

* separate analysis of each channel
* independent histogram computation
* visualization of the individual channel contributions

### Important concept

A color image has shape:

```python
(height, width, 3)
```

After splitting, each channel becomes:

```python
(height, width)
```

Because each channel is now a 2D array.

---

## 14. Print shapes of image and channels

```python
print(f"Original RGB Image Shape : {image_rgb.shape}")
print(f"Red Channel Shape        : {r_channel.shape}")
print(f"Green Channel Shape      : {g_channel.shape}")
print(f"Blue Channel Shape       : {b_channel.shape}")
```

### Explanation

This prints the dimensions of the image and the separated channels.

### Example

If original image shape is:

```python
(512, 512, 3)
```

Then each channel shape is:

```python
(512, 512)
```

### Meaning

* original image has 3 channels
* each separated channel has only intensity values for one color

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

This computes the histogram for each channel separately.

### Why channel index is `[0]` now

Because each variable (`r_channel`, `g_channel`, `b_channel`) is already a **single-channel image**.

So the only channel inside each one is index 0.

### Meaning

* `hist_r` = red intensity distribution
* `hist_g` = green intensity distribution
* `hist_b` = blue intensity distribution

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

This plots the histograms calculated in the previous step.

### Why this is useful

It visualizes the color balance of the image.

For example:

* if red histogram is dominant, the image contains more reddish tones
* if blue intensities are concentrated at low values, blue contribution is small

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

This block displays the three channels separately as images.

---

## 17.1 `plt.figure(figsize=(15,5))`

Creates a wide figure to place three images side by side.

---

## 17.2 `plt.subplot(1,3,1)`

Creates a subplot grid of:

* 1 row
* 3 columns
* current plot position = 1

Similarly:

* `(1,3,2)` for second image
* `(1,3,3)` for third image

---

## 17.3 `plt.imshow(r_channel, cmap='Reds')`

Displays the red channel using a red colormap.

### Why use `cmap='Reds'`?

Without a colormap, a single-channel image may appear in default grayscale.

Using:

* `'Reds'`
* `'Greens'`
* `'Blues'`

makes each channel visually intuitive.

---

## 17.4 `plt.title(...)`

Adds the channel name as a title.

---

## 17.5 `plt.axis('off')`

Removes axes for cleaner display.

---

## 17.6 `plt.show()`

Displays the complete figure.

### What these images represent

They do **not** show the original image colors directly.

They show **how strong each channel is at each pixel location**.

Bright areas mean higher intensity in that channel.

---

# FINAL CHANNEL STATISTICS FUNCTION

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

This function computes basic statistics for a single color channel.

---

## 18.1 Function parameters

### `channel`

The channel image array

### `name`

Channel name, like `"Red"`

### `color`

A color parameter is passed, but it is **not used inside the function**

So this parameter is unnecessary in the current version.

---

## 18.2 `mean = np.mean(channel)`

Calculates the average intensity directly from all pixels in the channel.

This is simpler than the previous histogram-weighted approach.

---

## 18.3 `std = np.std(channel)`

Calculates the standard deviation directly from pixel values.

---

## 18.4 Histogram for peak intensity

```python
hist = cv2.calcHist([channel], [0], None, [256], [0, 256])
peak = np.argmax(hist)
```

### Explanation

* `hist` gives the frequency of each intensity value
* `np.argmax(hist)` returns the index of the maximum value
* that index is the **most frequent intensity**, called the **peak intensity**

### Example

If intensity 120 occurs most often, peak intensity = 120

---

## 18.5 Print formatted output

```python
print(f"{name:5} channel | Mean: {mean:6.1f} | Std: {std:5.1f} | Peak Intensity: {peak:3}")
```

### Explanation

This uses formatted printing to align the output neatly.

---

## 18.6 `return mean, std, peak`

Returns the three computed values so they can be stored for later use.

---

# 19. Call the function for each channel

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

This prints a heading and calls the statistics function for all three channels.

### Output meaning

* `r_stats` stores `(mean, std, peak)` for red
* `g_stats` stores `(mean, std, peak)` for green
* `b_stats` stores `(mean, std, peak)` for blue

---

# Important Concepts Behind the Code

---

## A. What is `cv2.imread()`?

Used to read an image from disk.

```python
img = cv2.imread('Girl.jpg')
```

Returns:

* image as NumPy array if successful
* `None` if unsuccessful

---

## B. What is `cv2.cvtColor()`?

Used to convert images between color spaces.

```python
cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
```

Common conversions:

* BGR → RGB
* RGB → Grayscale
* BGR → HSV

---

## C. What is `cv2.calcHist()`?

Calculates histogram of an image or channel.

General form:

```python
cv2.calcHist(images, channels, mask, histSize, ranges)
```

### Parameters

* `images`: input image list
* `channels`: channel index
* `mask`: region mask or `None`
* `histSize`: number of bins
* `ranges`: intensity range

---

## D. What is a color channel?

A color image has three channels:

* Red
* Green
* Blue

Each channel stores intensity values from 0 to 255.

---

## E. What is mean intensity?

Average brightness of a channel.

Formula:
[
\text{Mean} = \frac{\sum \text{pixel values}}{N}
]

---

## F. What is standard deviation?

Measures variation of pixel intensities.

* low std = similar intensities
* high std = wide intensity spread

---

## G. What is peak intensity?

The intensity value that appears most frequently in the image/channel.

---

# What your notebook is trying to teach

This practical is mainly about understanding:

1. how images are loaded in OpenCV
2. why BGR and RGB are different
3. how to display images correctly
4. how to calculate histograms
5. how to analyze color channels statistically
6. how to split an image into separate color components

---

# Small issues in your notebook

These are not major, but good to know for the exam.

## 1. `IncrementalNewlineDecoder` is unused

```python
from io import IncrementalNewlineDecoder
```

Not used later.

## 2. `mediapipe` is installed but unused

It is installed, but no MediaPipe code appears in the notebook.

## 3. Typo in title

```python
plt.title('GBR Image')
```

Should likely be:

```python
plt.title('BGR Image')
```

## 4. Variable naming can be improved

This:

```python
img_bgr = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
```

works, but the name becomes misleading after conversion.

Better:

```python
img_bgr = cv2.imread('Girl.jpg')
img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
```

## 5. `color` parameter in function is unused

```python
def channel_stats(channel, name, color):
```

`color` is passed but not used.

---

# Clean final version of the code

Here is the same practical in a cleaner form:

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

# Load image
image = cv2.imread('Girl.jpg')

if image is None:
    raise FileNotFoundError("Could not find 'Girl.jpg'.")

# Convert BGR to RGB
image_rgb = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)

# Display image
plt.figure(figsize=(6,6))
plt.imshow(image_rgb)
plt.title("Original Image (RGB)")
plt.axis('off')
plt.show()

# Plot RGB histograms
colors = ('r', 'g', 'b')
channel_names = ['Red', 'Green', 'Blue']

plt.figure(figsize=(10,6))
for i, col in enumerate(colors):
    hist = cv2.calcHist([image_rgb], [i], None, [256], [0,256])
    plt.plot(hist, color=col, label=f'{channel_names[i]} Channel')

plt.title("RGB Channel Histograms")
plt.xlabel("Pixel Intensity (0-255)")
plt.ylabel("Frequency")
plt.legend()
plt.grid(alpha=0.3)
plt.xlim([0,256])
plt.show()

# Split channels
r_channel, g_channel, b_channel = cv2.split(image_rgb)

# Show channels
plt.figure(figsize=(15,5))

plt.subplot(1,3,1)
plt.imshow(r_channel, cmap='Reds')
plt.title("Red Channel")
plt.axis('off')

plt.subplot(1,3,2)
plt.imshow(g_channel, cmap='Greens')
plt.title("Green Channel")
plt.axis('off')

plt.subplot(1,3,3)
plt.imshow(b_channel, cmap='Blues')
plt.title("Blue Channel")
plt.axis('off')

plt.show()

# Channel statistics
def channel_stats(channel, name):
    mean = np.mean(channel)
    std = np.std(channel)
    hist = cv2.calcHist([channel], [0], None, [256], [0,256])
    peak = np.argmax(hist)

    print(f"{name} Channel")
    print(f"Mean Intensity     : {mean:.2f}")
    print(f"Standard Deviation : {std:.2f}")
    print(f"Peak Intensity     : {peak}")
    print()

channel_stats(r_channel, "Red")
channel_stats(g_channel, "Green")
channel_stats(b_channel, "Blue")
```

---

# Practical exam viva answers you should remember

## Why do we convert BGR to RGB?

Because OpenCV reads color images in BGR format, but Matplotlib displays them correctly only in RGB format.

## What does histogram show?

It shows the distribution of pixel intensity values in an image or channel.

## Why split channels?

To analyze each color component separately.

## What is mean intensity?

The average brightness of a channel.

## What is standard deviation?

A measure of how much the intensity values vary.

## What is peak intensity?

The most frequent intensity value in that channel.

---

# Easy flow of the whole lab

```python
Install packages
→ Import libraries
→ Read image
→ Check if image exists
→ Convert BGR to RGB
→ Display image
→ Calculate RGB histograms
→ Split image into channels
→ Display channels
→ Compute statistics for each channel
```

