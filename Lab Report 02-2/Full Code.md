```python
# ============================================================
# DIGITAL IMAGE PROCESSING AND COMPUTER VISION PRACTICAL
# Complete full code with explain comments
# Based on the lab flow in your notebook
# ============================================================


# ============================================================
# 1. INSTALL REQUIRED LIBRARIES
# ============================================================
# Run this in Google Colab or Jupyter if OpenCV / MediaPipe
# are not already installed in the environment.
# MediaPipe is installed in the notebook, although it is not
# actually used in the later image-processing steps.

!pip install mediapipe opencv-python --quiet


# ============================================================
# 2. IMPORT REQUIRED LIBRARIES
# ============================================================
# cv2        -> OpenCV library for image processing
# numpy      -> numerical operations on image arrays
# matplotlib -> image display and plotting graphs

from io import IncrementalNewlineDecoder   # imported in notebook, but not used later
import cv2
import numpy as np
import matplotlib.pyplot as plt

# This magic command is used in Jupyter / Colab so plots appear
# directly inside the notebook output.
%matplotlib inline

# Set default figure size for plots
plt.rcParams['figure.figsize'] = (10, 6)

# Set default image color map to grayscale for single-channel displays
plt.rcParams['image.cmap'] = 'gray'


# ============================================================
# 3. READ IMAGE SAFELY
# ============================================================
# OpenCV reads color images in BGR format by default.
# So the image loaded here is NOT RGB yet.

img_bgr = cv2.imread('Girl.jpg')

# Always check whether the image was loaded successfully.
# If the file is missing, OpenCV returns None.
if img_bgr is None:
    raise FileNotFoundError("❌ Could not find 'Girl.jpg'. Please upload it to the Colab / notebook working directory.")


# ============================================================
# 4. CONVERT BGR TO RGB
# ============================================================
# Matplotlib expects RGB images.
# Therefore, before displaying the image using plt.imshow(),
# we convert it from BGR -> RGB.

img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)


# ============================================================
# 5. DISPLAY THE CORRECT RGB IMAGE
# ============================================================
plt.figure(figsize=(6, 6))
plt.imshow(img_rgb)
plt.title("RGB Image (Original)")
plt.axis('off')   # hides axis lines and tick marks
plt.show()


# ============================================================
# 6. DISPLAY THE IMAGE WITHOUT CONVERSION (FOR DEMONSTRATION)
# ============================================================
# This is only to show what happens if you display a BGR image
# directly using Matplotlib.
# The colors will look wrong because red and blue are swapped.

plt.figure(figsize=(6, 6))
plt.imshow(img_bgr)
plt.title("BGR Image Shown Directly (Wrong Colors)")
plt.axis('off')
plt.show()


# ============================================================
# 7. PLOT RGB CHANNEL HISTOGRAMS
# ============================================================
# A histogram shows the distribution of pixel intensities.
# For a color image, we can calculate one histogram for each
# channel: Red, Green, and Blue.

colors = ('r', 'g', 'b')
channel_names = ['Red', 'Green', 'Blue']

plt.figure(figsize=(10, 6))

# enumerate(colors) gives:
# i = 0, col = 'r'
# i = 1, col = 'g'
# i = 2, col = 'b'
for i, col in enumerate(colors):

    # cv2.calcHist parameters:
    # [img_rgb]  -> input image as a list
    # [i]        -> channel index (0=R, 1=G, 2=B for RGB image)
    # None       -> no mask, use whole image
    # [256]      -> number of histogram bins
    # [0,256]    -> intensity range from 0 to 255
    hist = cv2.calcHist([img_rgb], [i], None, [256], [0, 256])

    # Plot the histogram line for this channel
    plt.plot(hist, color=col, label=f'{channel_names[i]} Channel')

    # Set x-axis range
    plt.xlim([0, 256])

plt.title('RGB Channel Histograms')
plt.xlabel('Pixel Intensity (0–255)')
plt.ylabel('Frequency')
plt.legend()
plt.grid(alpha=0.3)
plt.show()


# ============================================================
# 8. COMPUTE MEAN AND STANDARD DEVIATION FROM HISTOGRAMS
# ============================================================
# This section calculates statistical information for each
# RGB channel using histogram weights.
#
# Mean intensity:
#     average brightness of that channel
#
# Standard deviation:
#     how spread out the intensity values are
#     (gives an idea about variation / contrast)

for i, name in enumerate(channel_names):

    # Calculate histogram for current channel
    hist = cv2.calcHist([img_rgb], [i], None, [256], [0, 256])

    # Flatten histogram from shape (256,1) to (256,)
    hist = hist.flatten()

    # Create intensity values: 0, 1, 2, ..., 255
    intensity_values = np.arange(256)

    # Weighted mean intensity using histogram frequencies
    mean_intensity = np.average(intensity_values, weights=hist)

    # Weighted standard deviation
    std_intensity = np.sqrt(
        np.average((intensity_values - mean_intensity) ** 2, weights=hist)
    )

    print(f"{name} Channel Analysis:")
    print(f"  Mean Intensity     : {mean_intensity:.2f}")
    print(f"  Standard Deviation : {std_intensity:.2f}")
    print()


# ============================================================
# 9. SPLIT RGB IMAGE INTO SEPARATE CHANNELS
# ============================================================
# cv2.split() separates the 3-channel image into three single-
# channel matrices.
#
# r_channel -> contains only red intensities
# g_channel -> contains only green intensities
# b_channel -> contains only blue intensities

r_channel, g_channel, b_channel = cv2.split(img_rgb)


# ============================================================
# 10. PRINT IMAGE AND CHANNEL SHAPES
# ============================================================
# Original RGB image usually has shape:
# (height, width, 3)
#
# After splitting, each channel becomes:
# (height, width)

print(f"Original RGB Image Shape : {img_rgb.shape}")
print(f"Red Channel Shape        : {r_channel.shape}")
print(f"Green Channel Shape      : {g_channel.shape}")
print(f"Blue Channel Shape       : {b_channel.shape}")


# ============================================================
# 11. CALCULATE HISTOGRAMS FOR SEPARATE CHANNELS
# ============================================================
# Because each channel image is already single-channel,
# the channel index is [0] for each one.

hist_r = cv2.calcHist([r_channel], [0], None, [256], [0, 256])
hist_g = cv2.calcHist([g_channel], [0], None, [256], [0, 256])
hist_b = cv2.calcHist([b_channel], [0], None, [256], [0, 256])


# ============================================================
# 12. PLOT HISTOGRAMS OF THE SEPARATED CHANNELS
# ============================================================
plt.figure(figsize=(10, 6))
plt.plot(hist_r, color='r', label='Red Channel')
plt.plot(hist_g, color='g', label='Green Channel')
plt.plot(hist_b, color='b', label='Blue Channel')
plt.title('RGB Channel Histograms using cv2.calcHist()')
plt.xlabel('Pixel Intensity (0–255)')
plt.ylabel('Frequency')
plt.legend()
plt.grid(alpha=0.3)
plt.xlim([0, 256])
plt.show()


# ============================================================
# 13. DISPLAY THE THREE CHANNELS AS IMAGES
# ============================================================
# Each separated channel is a single-channel intensity image.
# Using colormaps such as 'Reds', 'Greens', and 'Blues' helps
# visualize each channel clearly.

plt.figure(figsize=(15, 5))

plt.subplot(1, 3, 1)
plt.imshow(r_channel, cmap='Reds')
plt.title('Red Channel')
plt.axis('off')

plt.subplot(1, 3, 2)
plt.imshow(g_channel, cmap='Greens')
plt.title('Green Channel')
plt.axis('off')

plt.subplot(1, 3, 3)
plt.imshow(b_channel, cmap='Blues')
plt.title('Blue Channel')
plt.axis('off')

plt.show()


# ============================================================
# 14. DEFINE A FUNCTION TO COMPUTE BASIC CHANNEL STATISTICS
# ============================================================
# This function computes:
# - mean intensity
# - standard deviation
# - peak intensity (most frequent pixel value)

def channel_stats(channel, name):
    """
    Compute and print statistics for one color channel.

    Parameters:
        channel : single-channel image array
        name    : channel name as string

    Returns:
        mean, std, peak
    """

    # Direct average intensity of all pixels in the channel
    mean = np.mean(channel)

    # Standard deviation of the pixel values
    std = np.std(channel)

    # Histogram of this single-channel image
    hist = cv2.calcHist([channel], [0], None, [256], [0, 256])

    # Peak intensity = index of the histogram bin with the highest count
    peak = np.argmax(hist)

    print(f"{name:5} Channel | Mean: {mean:6.2f} | Std: {std:6.2f} | Peak Intensity: {peak:3}")

    return mean, std, peak


# ============================================================
# 15. CALL THE CHANNEL STATISTICS FUNCTION
# ============================================================
print("\n" + "=" * 70)
print("RGB CHANNEL STATISTICS")
print("-" * 70)

r_stats = channel_stats(r_channel, "Red")
g_stats = channel_stats(g_channel, "Green")
b_stats = channel_stats(b_channel, "Blue")

print("=" * 70)


# ============================================================
# 16. ADVANCED VISUAL ANALYSIS:
#     CHANNEL-ONLY IMAGES + HISTOGRAMS + MEAN LINES
# ============================================================
# This section creates a 2x3 visualization:
# Top row    -> channel-only images
# Bottom row -> histograms of each channel
#
# It also marks:
# - low intensity zone
# - mid intensity zone
# - high intensity zone
# - mean intensity line

mean_r = r_stats[0]
mean_g = g_stats[0]
mean_b = b_stats[0]

plt.figure(figsize=(16, 10))

channels = [
    (r_channel, hist_r, 'Red', 'red', mean_r),
    (g_channel, hist_g, 'Green', 'green', mean_g),
    (b_channel, hist_b, 'Blue', 'blue', mean_b)
]

for i, (ch, hist, name, color, mean_val) in enumerate(channels):

    # -------------------------------
    # Top row: channel-only color image
    # -------------------------------
    plt.subplot(2, 3, i + 1)

    # Create a blank RGB image with all zeros
    masked = np.zeros_like(img_rgb)

    # Put the selected channel into its correct position:
    # index 0 = Red, 1 = Green, 2 = Blue
    masked[:, :, i] = ch

    plt.imshow(masked)
    plt.title(f'{name} Channel Only', fontsize=13, fontweight='bold', color=color)
    plt.axis('off')

    # -------------------------------
    # Bottom row: histogram
    # -------------------------------
    plt.subplot(2, 3, i + 4)

    # Shade low / mid / high intensity zones
    plt.axvspan(0, 85, color='darkred', alpha=0.15, label='Low')
    plt.axvspan(86, 170, color='grey', alpha=0.15, label='Mid')
    plt.axvspan(171, 255, color='yellow', alpha=0.15, label='High')

    # Plot histogram line
    plt.plot(hist, color=color, linewidth=2, label=f'{name} Histogram')

    # Fill area under histogram
    plt.fill_between(range(256), hist.flatten(), color=color, alpha=0.3)

    # Draw a vertical dashed line at the mean intensity
    plt.axvline(mean_val, color='black', linestyle='--', linewidth=2,
                label=f'Mean = {mean_val:.1f}')

    plt.xlabel('Pixel Intensity (0–255)')
    plt.ylabel('Pixel Count')
    plt.xlim([0, 255])
    plt.grid(True, alpha=0.3)
    plt.legend()

plt.tight_layout()
plt.show()


# ============================================================
# 17. OVERLAY ALL THREE HISTOGRAMS IN ONE PLOT
# ============================================================
# This gives a combined comparison of the RGB distributions.

plt.figure(figsize=(14, 7))

x = range(256)

# Filled areas under each histogram
plt.fill_between(x, hist_r.flatten(), alpha=0.4, color='red', label='Red Channel')
plt.fill_between(x, hist_g.flatten(), alpha=0.4, color='green', label='Green Channel')
plt.fill_between(x, hist_b.flatten(), alpha=0.4, color='blue', label='Blue Channel')

# Histogram lines
plt.plot(hist_r, color='red', alpha=0.9, linewidth=2.5)
plt.plot(hist_g, color='green', alpha=0.9, linewidth=2.5)
plt.plot(hist_b, color='blue', alpha=0.9, linewidth=2.5)

# Mean intensity lines
plt.axvline(mean_r, color='red', linestyle='--', alpha=0.7, linewidth=1.5)
plt.axvline(mean_g, color='green', linestyle='--', alpha=0.7, linewidth=1.5)
plt.axvline(mean_b, color='blue', linestyle='--', alpha=0.7, linewidth=1.5)

plt.title('RGB Channels Overlay Histogram')
plt.xlabel('Pixel Intensity (0–255)')
plt.ylabel('Pixel Frequency')
plt.grid(True, alpha=0.2)
plt.xlim(0, 255)
plt.legend()
plt.tight_layout()
plt.show()


# ============================================================
# 18. CONVERT RGB IMAGE TO GRAYSCALE
# ============================================================
# A grayscale image has only one channel containing brightness
# information.

gray = cv2.cvtColor(img_rgb, cv2.COLOR_RGB2GRAY)

# Calculate histogram of grayscale image
hist_gray = cv2.calcHist([gray], [0], None, [256], [0, 256])

plt.figure(figsize=(10, 5))
plt.plot(hist_gray, color='black')
plt.title('Grayscale Histogram')
plt.xlabel('Intensity (0–255)')
plt.ylabel('Number of Pixels')
plt.xlim([0, 256])
plt.grid(True, alpha=0.3)
plt.show()


# ============================================================
# 19. PLOT HISTOGRAM + CUMULATIVE DISTRIBUTION FUNCTION (CDF)
# ============================================================
# CDF shows the cumulative number of pixels up to each intensity.

cdf = hist_gray.cumsum()

# Normalize CDF so it can be displayed on the same plot scale
cdf_normalized = cdf * hist_gray.max() / cdf.max()

plt.figure(figsize=(10, 5))
plt.plot(hist_gray, color='black', alpha=0.7, label='Histogram')
plt.plot(cdf_normalized, color='red', label='CDF (normalized)')
plt.title('Histogram and Cumulative Distribution Function')
plt.xlabel('Intensity')
plt.ylabel('Count')
plt.legend()
plt.grid(True, alpha=0.3)
plt.show()


# ============================================================
# 20. CONVERT IMAGE TO HSV AND PLOT H, S, V HISTOGRAMS
# ============================================================
# HSV stands for:
# H -> Hue
# S -> Saturation
# V -> Value
#
# IMPORTANT:
# Use the original BGR image here, because OpenCV expects BGR
# input for COLOR_BGR2HSV conversion.

hsv = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2HSV)

# Split into channels
h, s, v = cv2.split(hsv)

fig, ax = plt.subplots(1, 3, figsize=(15, 4))

hsv_channels = [h, s, v]
hsv_names = ['Hue', 'Saturation', 'Value']
hsv_colors = ['red', 'green', 'blue']

for i in range(3):
    hist_ch = cv2.calcHist([hsv_channels[i]], [0], None, [256], [0, 256])
    ax[i].plot(hist_ch, color=hsv_colors[i])
    ax[i].set_title(f'{hsv_names[i]} Histogram')
    ax[i].set_xlim([0, 256])
    ax[i].grid(True, alpha=0.3)

plt.tight_layout()
plt.show()


# ============================================================
# 21. CONVERT IMAGE TO YUV AND COMPARE Y CHANNEL WITH GRAYSCALE
# ============================================================
# YUV separates:
# Y -> luminance (brightness)
# U -> chrominance
# V -> chrominance

yuv = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2YUV)
y, u, v = cv2.split(yuv)

# Histogram of Y channel
hist_y = cv2.calcHist([y], [0], None, [256], [0, 256])

plt.figure(figsize=(10, 5))
plt.plot(hist_gray, color='black', label='Grayscale (RGB)')
plt.plot(hist_y, color='orange', label='Y-Channel (YUV)')
plt.title('Grayscale vs Y-Channel Histogram')
plt.xlabel('Intensity')
plt.ylabel('Count')
plt.legend()
plt.grid(True, alpha=0.3)
plt.show()


# ============================================================
# 22. CREATE HIGH / GOOD / LOW EXPOSURE VARIANTS
# ============================================================
# Exposure-like adjustment is simulated by MULTIPLYING pixel
# values by a factor.
#
# factor > 1  -> brighter / higher exposure
# factor < 1  -> darker / lower exposure

def adjust_exposure(img, factor):
    """
    Multiply pixel values to simulate exposure change.
    np.clip keeps values within valid range 0-255.
    """
    return np.clip(img * factor, 0, 255).astype('uint8')


# Original grayscale image
good_exposure = gray.copy()

# Higher exposure -> brighter image
high_exposure = adjust_exposure(gray, 2.0)

# Lower exposure -> darker image
low_exposure = adjust_exposure(gray, 0.4)


# ============================================================
# 23. CALCULATE HISTOGRAMS FOR EXPOSURE VARIANTS
# ============================================================
def compute_hist(img):
    """Compute histogram for a single-channel image."""
    return cv2.calcHist([img], [0], None, [256], [0, 256])

hist_good_exp = compute_hist(good_exposure)
hist_high_exp = compute_hist(high_exposure)
hist_low_exp = compute_hist(low_exposure)


# ============================================================
# 24. DISPLAY EXPOSURE VARIANTS
# ============================================================
plt.figure(figsize=(15, 5))

plt.subplot(1, 3, 1)
plt.imshow(low_exposure, cmap='gray')
plt.title('Low Exposure')
plt.axis('off')

plt.subplot(1, 3, 2)
plt.imshow(good_exposure, cmap='gray')
plt.title('Good Exposure')
plt.axis('off')

plt.subplot(1, 3, 3)
plt.imshow(high_exposure, cmap='gray')
plt.title('High Exposure')
plt.axis('off')

plt.show()


# ============================================================
# 25. PLOT HISTOGRAMS OF EXPOSURE VARIANTS
# ============================================================
plt.figure(figsize=(10, 6))
plt.plot(hist_low_exp, color='blue', label='Low Exposure')
plt.plot(hist_good_exp, color='black', label='Good Exposure')
plt.plot(hist_high_exp, color='red', label='High Exposure')
plt.title('Histogram Comparison of Exposure Variants')
plt.xlabel('Pixel Intensity (0–255)')
plt.ylabel('Frequency')
plt.legend()
plt.grid(alpha=0.3)
plt.xlim([0, 256])
plt.show()


# ============================================================
# 26. CREATE HIGH / GOOD / LOW BRIGHTNESS VARIANTS
# ============================================================
# Brightness adjustment is simulated by ADDING or subtracting
# a constant value.
#
# delta > 0  -> brighter
# delta < 0  -> darker

def adjust_brightness(img, delta):
    """
    Add constant value to pixel intensities.
    Convert to int16 first to avoid overflow during addition,
    then clip back into the valid 0-255 range.
    """
    return np.clip(img.astype(np.int16) + delta, 0, 255).astype('uint8')


good_brightness = gray.copy()
high_brightness = adjust_brightness(gray, +100)
low_brightness = adjust_brightness(gray, -100)


# ============================================================
# 27. CALCULATE HISTOGRAMS FOR BRIGHTNESS VARIANTS
# ============================================================
hist_good_bri = compute_hist(good_brightness)
hist_high_bri = compute_hist(high_brightness)
hist_low_bri = compute_hist(low_brightness)


# ============================================================
# 28. DISPLAY BRIGHTNESS VARIANTS
# ============================================================
plt.figure(figsize=(15, 5))

plt.subplot(1, 3, 1)
plt.imshow(low_brightness, cmap='gray')
plt.title('Low Brightness')
plt.axis('off')

plt.subplot(1, 3, 2)
plt.imshow(good_brightness, cmap='gray')
plt.title('Good Brightness')
plt.axis('off')

plt.subplot(1, 3, 3)
plt.imshow(high_brightness, cmap='gray')
plt.title('High Brightness')
plt.axis('off')

plt.show()


# ============================================================
# 29. PLOT HISTOGRAMS OF BRIGHTNESS VARIANTS
# ============================================================
plt.figure(figsize=(10, 6))
plt.plot(hist_low_bri, color='blue', label='Low Brightness')
plt.plot(hist_good_bri, color='black', label='Good Brightness')
plt.plot(hist_high_bri, color='red', label='High Brightness')
plt.title('Histogram Comparison of Brightness Variants')
plt.xlabel('Pixel Intensity (0–255)')
plt.ylabel('Frequency')
plt.legend()
plt.grid(alpha=0.3)
plt.xlim([0, 256])
plt.show()


# ============================================================
# 30. FINAL SUMMARY PRINTS
# ============================================================
print("\nPRACTICAL COMPLETED SUCCESSFULLY")
print("--------------------------------")
print("1. Image loaded safely")
print("2. BGR converted to RGB")
print("3. RGB image displayed")
print("4. RGB histograms plotted")
print("5. Channel statistics calculated")
print("6. RGB channels split and displayed")
print("7. Grayscale histogram plotted")
print("8. CDF plotted")
print("9. HSV histograms plotted")
print("10. Y-channel compared with grayscale")
print("11. Exposure variants created and analyzed")
print("12. Brightness variants created and analyzed")
```

