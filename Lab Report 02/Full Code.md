```python
# ============================================================
# DIGITAL IMAGE PROCESSING AND COMPUTER VISION
# FULL COMPLETE CODE WITH EXPLAIN COMMENTS
# ============================================================
#
# This practical demonstrates:
# 1. Installing and importing required libraries
# 2. Reading an image using OpenCV
# 3. Understanding BGR vs RGB color format
# 4. Displaying images using Matplotlib
# 5. Calculating RGB histograms
# 6. Computing statistical analysis for each color channel
# 7. Splitting the image into Red, Green, and Blue channels
# 8. Displaying individual color channels
#
# ============================================================


# ============================================================
# 1. INSTALL REQUIRED LIBRARIES
# ============================================================
# This line is mainly for Google Colab / Jupyter Notebook.
# It installs OpenCV and MediaPipe if they are not available.
# MediaPipe is not used later in this practical, but it appeared
# in your lab notebook, so it is included here for completeness.

# !pip install mediapipe opencv-python --quiet


# ============================================================
# 2. IMPORT REQUIRED LIBRARIES
# ============================================================

# This import appeared in your notebook, but it is not actually used.
# It can be removed safely.
from io import IncrementalNewlineDecoder

# OpenCV library for image reading, color conversion, and histogram calculation
import cv2

# NumPy library for mathematical and numerical operations
import numpy as np

# Matplotlib library for plotting and displaying images
import matplotlib.pyplot as plt

# This magic command is used in Jupyter/Colab to display plots inline.
# You can keep it only if you run the code in a notebook.
# %matplotlib inline


# ============================================================
# 3. CONFIGURE PLOT SETTINGS
# ============================================================
# Set a default figure size for plots
plt.rcParams['figure.figsize'] = (10, 6)

# Set default image colormap to grayscale
# This mainly affects single-channel image displays
plt.rcParams['image.cmap'] = 'gray'


# ============================================================
# 4. READ THE IMAGE
# ============================================================
# OpenCV reads the image from the file path.
# IMPORTANT:
# OpenCV loads color images in BGR format, not RGB.
image = cv2.imread('Girl.jpg')

# Check whether the image was loaded correctly.
# If the file does not exist, cv2.imread() returns None.
if image is None:
    raise FileNotFoundError("❌ Could not find 'Girl.jpg'. Please place the image in the working directory.")


# ============================================================
# 5. DISPLAY IMAGE DIRECTLY WITHOUT CONVERSION (WRONG COLORS)
# ============================================================
# This step shows what happens if we display an OpenCV image
# directly with Matplotlib without converting BGR to RGB.
# The colors will look incorrect because Matplotlib expects RGB.

plt.figure(figsize=(12, 4))
plt.imshow(image)                 # This will display incorrect colors
plt.title('BGR Image (Displayed Without Conversion)')
plt.axis('off')
plt.show()


# ============================================================
# 6. CONVERT BGR TO RGB
# ============================================================
# Since OpenCV reads the image in BGR format and Matplotlib expects RGB,
# we must convert the image before displaying it correctly.
image_rgb = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)


# ============================================================
# 7. DISPLAY THE CORRECT RGB IMAGE
# ============================================================
# Now the image is converted into RGB format, so Matplotlib
# can display it with correct colors.

plt.figure(figsize=(6, 6))
plt.imshow(image_rgb)
plt.title('RGB Image (Original)')
plt.axis('off')
plt.show()


# ============================================================
# 8. DEFINE HISTOGRAM COLORS AND CHANNEL NAMES
# ============================================================
# These will be used to plot histograms for the three channels.
colors = ('r', 'g', 'b')
channel_names = ['Red', 'Green', 'Blue']


# ============================================================
# 9. CALCULATE AND PLOT RGB HISTOGRAMS
# ============================================================
# A histogram shows the distribution of pixel intensity values.
# For RGB images, each channel has its own histogram.
#
# Intensity range:
# 0   = darkest
# 255 = brightest

plt.figure(figsize=(10, 6))

# Loop through the 3 channels:
# i = 0 -> Red
# i = 1 -> Green
# i = 2 -> Blue
for i, col in enumerate(colors):
    # Calculate histogram for one channel
    # Parameters:
    # [image_rgb] -> input image list
    # [i]         -> channel index
    # None        -> no mask, use whole image
    # [256]       -> 256 bins for intensities 0-255
    # [0,256]     -> intensity range
    hist = cv2.calcHist([image_rgb], [i], None, [256], [0, 256])

    # Plot histogram line
    plt.plot(hist, color=col, label=f'{channel_names[i]} Channel')

    # Set x-axis range to 0-256
    plt.xlim([0, 256])

# Add labels, title, legend, and grid
plt.title('RGB Channel Histograms - Girl.jpg')
plt.xlabel('Pixel Intensity (0-255)')
plt.ylabel('Frequency')
plt.legend()
plt.grid(alpha=0.3)
plt.show()


# ============================================================
# 10. CALCULATE MEAN AND STANDARD DEVIATION USING HISTOGRAM WEIGHTS
# ============================================================
# This section computes statistics from the histogram itself.
#
# Mean intensity:
# Average brightness of the channel
#
# Standard deviation:
# Measures how spread out the intensity values are

print("=" * 60)
print("CHANNEL ANALYSIS USING HISTOGRAM WEIGHTS")
print("=" * 60)

for i, name in enumerate(channel_names):
    # Compute histogram for the current channel
    hist = cv2.calcHist([image_rgb], [i], None, [256], [0, 256])

    # Convert histogram from shape (256,1) to shape (256,)
    hist = hist.flatten()

    # Create array of intensity values 0 to 255
    intensity_values = np.arange(256)

    # Weighted mean intensity
    mean_intensity = np.average(intensity_values, weights=hist)

    # Weighted standard deviation
    std_intensity = np.sqrt(
        np.average((intensity_values - mean_intensity) ** 2, weights=hist)
    )

    # Print results
    print(f"{name} Channel Analysis:")
    print(f"   Mean Intensity     : {mean_intensity:.2f}")
    print(f"   Standard Deviation : {std_intensity:.2f}")
    print()


# ============================================================
# 11. SPLIT RGB IMAGE INTO RED, GREEN, AND BLUE CHANNELS
# ============================================================
# cv2.split() separates a 3-channel image into three 2D arrays.
# Each channel will contain only one color intensity matrix.
r_channel, g_channel, b_channel = cv2.split(image_rgb)


# ============================================================
# 12. PRINT SHAPES OF ORIGINAL IMAGE AND CHANNELS
# ============================================================
# The original RGB image has shape: (height, width, 3)
# Each separated channel has shape: (height, width)

print("=" * 60)
print("IMAGE AND CHANNEL SHAPES")
print("=" * 60)
print(f"Original RGB Image Shape : {image_rgb.shape}")
print(f"Red Channel Shape        : {r_channel.shape}")
print(f"Green Channel Shape      : {g_channel.shape}")
print(f"Blue Channel Shape       : {b_channel.shape}")
print()


# ============================================================
# 13. CALCULATE HISTOGRAMS FOR THE SPLIT CHANNELS
# ============================================================
# Since each channel is already a single-channel image,
# the channel index is [0].

hist_r = cv2.calcHist([r_channel], [0], None, [256], [0, 256])
hist_g = cv2.calcHist([g_channel], [0], None, [256], [0, 256])
hist_b = cv2.calcHist([b_channel], [0], None, [256], [0, 256])


# ============================================================
# 14. PLOT HISTOGRAMS OF SPLIT CHANNELS
# ============================================================
# This is another way to show the same RGB histogram information,
# but now using the separated channels directly.

plt.figure(figsize=(10, 6))
plt.plot(hist_r, color='r', label='Red Channel')
plt.plot(hist_g, color='g', label='Green Channel')
plt.plot(hist_b, color='b', label='Blue Channel')
plt.title('RGB Channel Histograms using cv2.calcHist()')
plt.xlabel('Pixel Intensity (0-255)')
plt.ylabel('Frequency')
plt.xlim([0, 256])
plt.legend()
plt.grid(alpha=0.3)
plt.show()


# ============================================================
# 15. DISPLAY THE THREE CHANNELS SEPARATELY
# ============================================================
# Each channel is displayed as a grayscale-like image with a matching
# color map to make it visually intuitive.
#
# Bright areas = high intensity for that channel
# Dark areas   = low intensity for that channel

plt.figure(figsize=(15, 5))

# Red channel display
plt.subplot(1, 3, 1)
plt.imshow(r_channel, cmap='Reds')
plt.title('Red Channel')
plt.axis('off')

# Green channel display
plt.subplot(1, 3, 2)
plt.imshow(g_channel, cmap='Greens')
plt.title('Green Channel')
plt.axis('off')

# Blue channel display
plt.subplot(1, 3, 3)
plt.imshow(b_channel, cmap='Blues')
plt.title('Blue Channel')
plt.axis('off')

plt.show()


# ============================================================
# 16. DEFINE A FUNCTION TO CALCULATE CHANNEL STATISTICS
# ============================================================
# This function computes:
# - Mean intensity
# - Standard deviation
# - Peak intensity
#
# Peak intensity means the most frequent pixel value in the channel.

def channel_stats(channel, name):
    """
    Calculate and print basic statistics for one color channel.

    Parameters:
        channel : numpy array
            Single-channel image data
        name : str
            Name of the channel (Red / Green / Blue)

    Returns:
        mean : float
            Average intensity of the channel
        std : float
            Standard deviation of intensities
        peak : int
            Most frequent intensity value
    """

    # Mean intensity of pixel values
    mean = np.mean(channel)

    # Standard deviation of pixel values
    std = np.std(channel)

    # Histogram used to determine peak intensity
    hist = cv2.calcHist([channel], [0], None, [256], [0, 256])

    # Most frequent pixel intensity
    peak = np.argmax(hist)

    # Print results in a neat format
    print(f"{name:5} Channel | Mean: {mean:6.2f} | Std: {std:6.2f} | Peak Intensity: {peak:3}")

    return mean, std, peak


# ============================================================
# 17. CALL THE STATISTICS FUNCTION FOR EACH CHANNEL
# ============================================================
print("=" * 70)
print("RGB CHANNEL STATISTICS")
print("=" * 70)

r_stats = channel_stats(r_channel, "Red")
g_stats = channel_stats(g_channel, "Green")
b_stats = channel_stats(b_channel, "Blue")

print("=" * 70)


# ============================================================
# 18. OPTIONAL: CREATE COLOR-ONLY VISUALIZATIONS
# ============================================================
# This section creates three images:
# - one showing only red information
# - one showing only green information
# - one showing only blue information
#
# This helps visualize how each channel contributes to the final image.

# Create empty images with the same size as the original RGB image
red_only = np.zeros_like(image_rgb)
green_only = np.zeros_like(image_rgb)
blue_only = np.zeros_like(image_rgb)

# Fill only the required channel
# In RGB format:
# index 0 = Red
# index 1 = Green
# index 2 = Blue
red_only[:, :, 0] = r_channel
green_only[:, :, 1] = g_channel
blue_only[:, :, 2] = b_channel

plt.figure(figsize=(15, 5))

plt.subplot(1, 3, 1)
plt.imshow(red_only)
plt.title("Red Channel Only Image")
plt.axis('off')

plt.subplot(1, 3, 2)
plt.imshow(green_only)
plt.title("Green Channel Only Image")
plt.axis('off')

plt.subplot(1, 3, 3)
plt.imshow(blue_only)
plt.title("Blue Channel Only Image")
plt.axis('off')

plt.show()


# ============================================================
# 19. FINAL SUMMARY OUTPUT
# ============================================================
print("\n" + "=" * 70)
print("FINAL SUMMARY")
print("=" * 70)
print("1. The image was loaded using OpenCV.")
print("2. OpenCV reads color images in BGR format.")
print("3. The image was converted to RGB for correct display in Matplotlib.")
print("4. RGB histograms were calculated to analyze intensity distribution.")
print("5. The image was split into Red, Green, and Blue channels.")
print("6. Each channel was displayed separately.")
print("7. Mean, standard deviation, and peak intensity were calculated.")
print("=" * 70)
```

Here is a very short viva-ready memory note for this code:

```python
# PRACTICAL FLOW
# Read image -> Check existence -> Convert BGR to RGB ->
# Display image -> Plot RGB histogram -> Split channels ->
# Show channels -> Calculate statistics
```
