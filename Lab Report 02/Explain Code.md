
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

Before using OpenCV functions like image loading, color conversion, and histogram calculation, the package must be installed.

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

This block imports the required libraries and sets some display options for plots and images.

### Line-by-line explanation

#### `from io import IncrementalNewlineDecoder`

Imports `IncrementalNewlineDecoder` from Python’s built-in `io` module.

### Important note

This import is **not used anywhere later in the notebook**.
So it can be removed without affecting the program.

---

#### `import cv2`

Imports OpenCV using the alias `cv2`.

It is used for:

* reading images
* converting BGR to RGB
* splitting channels
* calculating histograms

---

#### `import numpy as np`

Imports NumPy as `np`.

It is used for:

* mathematical operations
* calculating mean
* calculating standard deviation
* weighted average calculations

---

#### `import matplotlib.pyplot as plt`

Imports the plotting module of Matplotlib as `plt`.

It is used for:

* displaying images
* drawing histograms
* adding titles and labels

---

#### `%matplotlib inline`

This is a **Jupyter magic command**.

It tells the notebook to display plots directly inside the notebook output.

---

#### `plt.rcParams['figure.figsize'] = (10,6)`

Sets the default figure size for plots.

That means all plots will use width `10` and height `6` unless changed manually.

---

#### `plt.rcParams['image.cmap'] = 'gray'`

Sets the default colormap for image display to grayscale.

This is useful when showing single-channel images.

### Why this block is important

This block prepares the notebook so that image processing and plotting work properly.

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

That means the channels are stored in this order:

* Blue
* Green
* Red

---

#### `if img_bgr is None:`

Checks whether the image was loaded correctly.

If the image file does not exist, OpenCV returns `None`.

---

#### `raise FileNotFoundError(...)`

Stops execution and shows a clear error message.

### Why this is useful

It prevents later errors.
For example, if the image is missing and you try to convert it using `cv2.cvtColor()`, the code will crash.

---

#### `img_bgr = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)`

Converts the image from **BGR format to RGB format**.

### Why this is needed

Because:

* OpenCV loads the image as **BGR**
* Matplotlib displays images correctly only when they are in **RGB**

If you skip this step, the displayed colors will be wrong.

---

#### `plt.figure(figsize=(6,6))`

Creates a new plotting figure with width `6` and height `6`.

---

#### `plt.imshow(img_bgr)`

Displays the image.

At this point, the variable name is still `img_bgr`, but the actual data has already been converted into RGB.

---

#### `plt.title('RGB Image(original)')`

Adds a title above the image.

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

This block loads and displays the image **without converting from BGR to RGB**.

### What happens here

* `cv2.imread('Girl.jpg')` loads the image in BGR format
* `plt.imshow(img)` displays it as if it were RGB
* therefore, the image colors appear incorrect

### Why the colors look wrong

Because Matplotlib expects the channel order to be:

* Red
* Green
* Blue

But OpenCV provides:

* Blue
* Green
* Red

So red and blue get swapped.

### About the title

```python
plt.title('GBR Image')
```

This appears to be a typo.

It should likely be:

```python
plt.title('BGR Image')
```

not `GBR Image`.

### Why this block is useful

This block demonstrates an important practical point:

> If an OpenCV image is shown directly with Matplotlib without conversion, the colors will not display correctly.

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

---

#### `img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)`

Converts the image from BGR to RGB.

---

#### `plt.figure(figsize=(12,4))`

Creates a new figure with width `12` and height `4`.

---

#### `plt.imshow(img_rgb)`

Displays the correctly converted RGB image.

---

#### `plt.title('RGB Image(original)')`

Adds a title to the image.

### Why this step is important

This is the correct standard workflow when using:

* OpenCV for reading images
* Matplotlib for displaying images

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

These are used when drawing the histogram lines.

---

## 6.2 `channel_names = ['Red','Green','Blue']`

Stores the names of the three channels.

These names are used in the legend.

---

## 6.3 `plt.figure(figsize=(10,6))`

Creates a figure for plotting the histograms.

---

## 6.4 `for i, col in enumerate(colors):`

Loops through the three color channels.

### What `enumerate(colors)` gives

* `i = 0`, `col = 'r'`
* `i = 1`, `col = 'g'`
* `i = 2`, `col = 'b'`

Here:

* `i` is the channel index
* `col` is the color used for plotting

---

## 6.5 `hist = cv2.calcHist([img_rgb], [i], None, [256], [0,256])`

This line calculates the histogram for one color channel.

### Breakdown of the parameters

#### `[img_rgb]`

Input image list.

---

#### `[i]`

Channel index.

Since the image is in RGB format:

* `0` = Red
* `1` = Green
* `2` = Blue

---

#### `None`

No mask is applied.

That means the histogram is calculated for the **entire image**.

---

#### `[256]`

Number of histogram bins.

This is `256` because pixel intensity values go from `0` to `255`.

---

#### `[0,256]`

Intensity range.

It means the histogram covers all possible intensity values from `0` up to `255`.

---

## 6.6 `plt.plot(hist, color=col, label=f'{channel_names[i]} Channel')`

Plots the histogram as a line graph.

Each channel is shown in its matching color:

* red line for red channel
* green line for green channel
* blue line for blue channel

---

## 6.7 `plt.xlim([0,256])`

Sets the x-axis range from `0` to `256`.

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

* `title` gives the graph a heading
* `xlabel` labels the x-axis
* `ylabel` labels the y-axis
* `legend` shows which line belongs to which channel
* `grid` makes the graph easier to read
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
* color balance
* intensity distribution

---

# REPEATED SECOND SECTION OF THE NOTEBOOK

The notebook then repeats the practical in a cleaner step-by-step style.

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

This is not wrong, but it is **redundant** if the imports were already done earlier.

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

Checks whether the image was successfully loaded.

If the file is missing, OpenCV returns `None`.

---

#### `raise FileNotFoundError(...)`

Stops the program and shows a useful error message.

### Why this is good practice

It prevents invalid image data from being passed into later functions.

---

## 9. Convert BGR to RGB

```python
image_rgb = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
```

### Explanation

Converts the image from BGR to RGB.

### Why this is needed

Because:

* OpenCV reads images as BGR
* Matplotlib displays images correctly as RGB

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

Adds a title above the image.

---

#### `plt.axis('off')`

Hides axis lines and tick marks.

This makes the image display cleaner.

---

#### `plt.show()`

Renders the image.

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

This is the same histogram process again, but now using the variable `image_rgb`.

It plots the histogram of:

* red channel
* green channel
* blue channel

This section is clearer and better organized.

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

This loop calculates statistical measurements for each RGB channel.

---

## 12.1 `for i, name in enumerate(channel_names):`

Loops through the three channels:

* Red
* Green
* Blue

`i` is the channel index, and `name` is the channel name.

---

## 12.2 `hist = cv2.calcHist([image_rgb], [i], None, [256], [0,256])`

Calculates the histogram for one channel.

---

## 12.3 `hist = hist.flatten()`

Converts the histogram from a 2D array into a 1D array.

### Why this is needed

OpenCV returns the histogram in shape like:

```python
(256, 1)
```

But for mathematical calculations, it is easier to use a flat 1D array:

```python
(256,)
```

---

## 12.4 `mean_intensity = np.average(np.arange(256), weights=hist)`

Calculates the **weighted mean intensity**.

### What `np.arange(256)` creates

It creates intensity values:

```python
0, 1, 2, ..., 255
```

### What `weights=hist` means

Each intensity is weighted by how many pixels have that value.

### Meaning

This gives the average brightness of the channel.

### Interpretation

* higher mean = brighter channel
* lower mean = darker channel

---

## 12.5 Standard deviation calculation

```python
std_intensity = np.sqrt(np.average((np.arange(256) - mean_intensity) ** 2, weights=hist))
```

### Explanation

This calculates the weighted standard deviation of the intensity values.

### Meaning

It measures how spread out the pixel intensities are.

### Interpretation

* low standard deviation = intensities are close to each other
* high standard deviation = intensities vary more widely

So it gives an idea of contrast variation in the channel.

---

## 12.6 Printing results

```python
print(f" {name} Channel Analysis:")
print(f"   Mean Intensity     : {mean_intensity:.2f}")
print(f"   Standard Deviation : {std_intensity:.2f}")
```

### Explanation

Prints the result for each channel in readable form.

### What `:.2f` means

It formats the number to 2 decimal places.

---

## 12.7 Why this method is useful

This method calculates statistics **using the histogram itself**, not directly from the image array.

That is mathematically valid and shows good understanding of histogram-based image analysis.

---

# SPLIT INTO CHANNELS SECTION

---

## 13. Split RGB image into three channels

```python
r_channel, g_channel, b_channel = cv2.split(image_rgb)
```

### Explanation

This separates the RGB image into three individual channel images.

### Output

* `r_channel` contains only red intensities
* `g_channel` contains only green intensities
* `b_channel` contains only blue intensities

### Why this is useful

It allows:

* separate analysis of each channel
* independent histogram calculation
* channel-based image display

### Important concept

A color image usually has shape:

```python
(height, width, 3)
```

After splitting, each channel has shape:

```python
(height, width)
```

because each channel becomes a single 2D matrix.

---

## 14. Print shapes of image and channels

```python
print(f"Original RGB Image Shape : {image_rgb.shape}")
print(f"Red Channel Shape        : {r_channel.shape}")
print(f"Green Channel Shape      : {g_channel.shape}")
print(f"Blue Channel Shape       : {b_channel.shape}")
```

### Explanation

This block prints the dimensions of the original image and the separated channels.

### Example meaning

If the original image shape is:

```python
(512, 512, 3)
```

then each channel shape becomes:

```python
(512, 512)
```

### Why this matters

It helps you understand how a 3-channel image is stored internally.

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

### Meaning

* `hist_r` = intensity distribution of the red channel
* `hist_g` = intensity distribution of the green channel
* `hist_b` = intensity distribution of the blue channel

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

This block plots the three already-calculated histograms together.

### Why this is useful

It helps visualize color distribution in the image.

For example:

* if the red histogram is dominant, the image has stronger red tones
* if blue intensities are mostly low, blue contribution is weak

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

This block displays the three individual channels as separate images.

---

## 17.1 `plt.figure(figsize=(15,5))`

Creates a wide figure to place three images side by side.

---

## 17.2 `plt.subplot(1,3,1)`

Creates a subplot layout of:

* 1 row
* 3 columns
* current position = 1

Similarly:

* `(1,3,2)` places the second image
* `(1,3,3)` places the third image

---

## 17.3 `plt.imshow(r_channel, cmap='Reds')`

Displays the red channel using a red-colored colormap.

### Why use `cmap='Reds'`?

Because a single-channel image would otherwise appear in grayscale.
Using a matching colormap makes the result easier to interpret visually.

The same idea is used for:

* `'Greens'`
* `'Blues'`

---

## 17.4 `plt.title(...)`

Adds the channel name above each subplot.

---

## 17.5 `plt.axis('off')`

Removes axes and tick marks for a cleaner image display.

---

## 17.6 `plt.show()`

Displays the final subplot figure.

### What these channel images represent

They show how strong each channel is at each pixel position.

Bright areas mean stronger intensity in that channel.

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

This function calculates basic statistics for one color channel.

---

## 18.1 Function parameters

### `channel`

The image array for one channel.

### `name`

The name of the channel, such as `"Red"`.

### `color`

A color value is passed into the function, but it is **not actually used inside the function**.

So in the current version, this parameter is unnecessary.

---

## 18.2 `mean = np.mean(channel)`

Calculates the mean intensity directly from all pixel values.

### Meaning

This gives the average brightness of the channel.

---

## 18.3 `std = np.std(channel)`

Calculates the standard deviation of the channel.

### Meaning

This shows how much the intensities vary.

---

## 18.4 Histogram for peak intensity

```python
hist = cv2.calcHist([channel], [0], None, [256], [0, 256])
peak = np.argmax(hist)
```

### Explanation

* `hist` counts how many times each intensity occurs
* `np.argmax(hist)` finds the index of the highest frequency
* that index is the **peak intensity**

### Meaning of peak intensity

Peak intensity is the intensity value that appears most frequently in the channel.

---

## 18.5 Formatted print statement

```python
print(f"{name:5} channel | Mean: {mean:6.1f} | Std: {std:5.1f} | Peak Intensity: {peak:3}")
```

### Explanation

This prints the output in a neatly aligned way.

---

## 18.6 `return mean, std, peak`

Returns the calculated values so they can be stored in variables.

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

This block prints a heading and then runs the `channel_stats()` function for all three channels.

### Output meaning

* `r_stats` stores the result for red
* `g_stats` stores the result for green
* `b_stats` stores the result for blue

Each result contains:

* mean
* standard deviation
* peak intensity

---

# IMPORTANT CONCEPTS BEHIND THE CODE

---

## A. What is `cv2.imread()`?

Used to read an image from a file.

```python
img = cv2.imread('Girl.jpg')
```

### Returns

* image as a NumPy array if successful
* `None` if unsuccessful

---

## B. What is `cv2.cvtColor()`?

Used to convert an image from one color space to another.

Example:

```python
cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
```

### Common conversions

* BGR → RGB
* BGR → Grayscale
* BGR → HSV

---

## C. What is `cv2.calcHist()`?

Used to calculate the histogram of an image or channel.

General form:

```python
cv2.calcHist(images, channels, mask, histSize, ranges)
```

### Parameters

* `images` = input image list
* `channels` = channel index
* `mask` = selected region or `None`
* `histSize` = number of bins
* `ranges` = value range

---

## D. What is a color channel?

A color image has three channels:

* Red
* Green
* Blue

Each channel stores intensity values from `0` to `255`.

---

## E. What is mean intensity?

The average brightness of a channel.

Formula:

[
\text{Mean} = \frac{\sum \text{pixel values}}{N}
]

---

## F. What is standard deviation?

It measures how much the pixel intensities vary around the mean.

* low standard deviation = intensities are similar
* high standard deviation = intensities vary more

---

## G. What is peak intensity?

The intensity value that appears most frequently in the image or channel.

---

# WHAT THIS NOTEBOOK IS TRYING TO TEACH

This practical mainly teaches:

1. how OpenCV loads images
2. why BGR and RGB are different
3. how to display images correctly
4. how to calculate histograms
5. how to analyze RGB channels statistically
6. how to split an image into separate channels

---

# SMALL ISSUES IN THE NOTEBOOK

These are small mistakes or unnecessary parts that are good to know for the exam.

---

## 1. `IncrementalNewlineDecoder` is unused

```python
from io import IncrementalNewlineDecoder
```

This import is present, but not used later.

---

## 2. `mediapipe` is installed but unused

The package is installed, but no MediaPipe code is used in the notebook.

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

This works:

```python
img_bgr = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
```

but after conversion, the name `img_bgr` becomes misleading.

Better version:

```python
img_bgr = cv2.imread('Girl.jpg')
img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
```

---

## 5. Unused parameter in function

```python
def channel_stats(channel, name, color):
```

The `color` parameter is passed, but not used inside the function.

---

# CLEAN FINAL VERSION OF THE CODE

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

# PRACTICAL EXAM VIVA ANSWERS YOU SHOULD REMEMBER

## Why do we convert BGR to RGB?

Because OpenCV reads images in BGR format, but Matplotlib displays images correctly only when they are in RGB format.

---

## What does a histogram show?

It shows the distribution of pixel intensity values in an image or in a single channel.

---

## Why do we split channels?

To analyze each color component separately.

---

## What is mean intensity?

The average brightness of a channel.

---

## What is standard deviation?

A measure of how much the intensity values vary.

---

## What is peak intensity?

The most frequent intensity value in that channel.

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
→ Split image into channels
→ Display channels
→ Compute statistics for each channel
```

---

