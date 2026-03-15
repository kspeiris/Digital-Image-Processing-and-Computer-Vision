```python
# ============================================================
# DIGITAL IMAGE PROCESSING & COMPUTER VISION LAB
# Practical: Image Loading, RGB Conversion, Histogram Analysis,
#            Channel Separation, and Statistical Analysis
# ============================================================

# -----------------------------
# 1. Import required libraries
# -----------------------------

import cv2                      # OpenCV library for image processing
import numpy as np              # NumPy for numerical operations
import matplotlib.pyplot as plt # Matplotlib for image display and plotting

# Optional: set default figure size for plots
plt.rcParams['figure.figsize'] = (10, 6)


# -----------------------------------
# 2. Load the image from local folder
# -----------------------------------

# cv2.imread() reads the image from the given file path.
# OpenCV loads color images in BGR format by default.
image_bgr = cv2.imread('Girl.jpg')

# Check whether the image was loaded successfully.
# If the image path is wrong or the file is missing, image_bgr will be None.
if image_bgr is None:
    raise FileNotFoundError("Could not find 'Girl.jpg'. Make sure the file is in the working directory.")


# --------------------------------------------------
# 3. Convert the image from BGR format to RGB format
# --------------------------------------------------

# Matplotlib expects images in RGB format.
# So we convert the OpenCV image from BGR to RGB.
image_rgb = cv2.cvtColor(image_bgr, cv2.COLOR_BGR2RGB)


# -------------------------
# 4. Display the RGB image
# -------------------------

plt.figure(figsize=(6, 6))              # Create a new figure window
plt.imshow(image_rgb)                   # Display the RGB image
plt.title("Original Image (RGB)")       # Add title
plt.axis('off')                         # Hide axis for cleaner display
plt.show()                              # Render the image


# ---------------------------------------------------------
# 5. Display the original BGR image without RGB conversion
# ---------------------------------------------------------

# This is only for demonstration.
# If we display the OpenCV BGR image directly with matplotlib,
# the colors will appear incorrect.
plt.figure(figsize=(6, 6))
plt.imshow(image_bgr)                   # Wrong display because image is still in BGR
plt.title("Displayed Without Conversion (BGR shown as RGB)")
plt.axis('off')
plt.show()


# ----------------------------------------------
# 6. Print the shape of the original RGB image
# ----------------------------------------------

# shape returns: (height, width, channels)
print("Original RGB Image Shape:", image_rgb.shape)

# Example output might be:
# (512, 512, 3)
# That means:
# 512 -> height
# 512 -> width
# 3   -> three color channels (R, G, B)


# -----------------------------------------
# 7. Split the image into R, G, B channels
# -----------------------------------------

# cv2.split() separates the 3-channel image into three 2D arrays
r_channel, g_channel, b_channel = cv2.split(image_rgb)

# Print the shape of each channel
print("Red Channel Shape  :", r_channel.shape)
print("Green Channel Shape:", g_channel.shape)
print("Blue Channel Shape :", b_channel.shape)


# ----------------------------------------
# 8. Display the individual RGB channels
# ----------------------------------------

plt.figure(figsize=(15, 5))

# Display Red channel
plt.subplot(1, 3, 1)                    # 1 row, 3 columns, position 1
plt.imshow(r_channel, cmap='Reds')      # Show red channel with red colormap
plt.title("Red Channel")
plt.axis('off')

# Display Green channel
plt.subplot(1, 3, 2)                    # position 2
plt.imshow(g_channel, cmap='Greens')    # Show green channel with green colormap
plt.title("Green Channel")
plt.axis('off')

# Display Blue channel
plt.subplot(1, 3, 3)                    # position 3
plt.imshow(b_channel, cmap='Blues')     # Show blue channel with blue colormap
plt.title("Blue Channel")
plt.axis('off')

plt.show()


# ------------------------------------------------------
# 9. Calculate histogram for each RGB channel directly
# ------------------------------------------------------

# cv2.calcHist() is used to calculate the histogram.
# Parameters:
# [image_rgb] -> list containing the image
# [i]         -> channel index (0=Red, 1=Green, 2=Blue) because image is already RGB
# None        -> no mask, so histogram is calculated for the whole image
# [256]       -> number of bins (0 to 255)
# [0,256]     -> intensity range

colors = ('r', 'g', 'b')                        # Colors for plotting
channel_names = ['Red', 'Green', 'Blue']        # Channel labels

plt.figure(figsize=(10, 6))

for i, col in enumerate(colors):
    hist = cv2.calcHist([image_rgb], [i], None, [256], [0, 256])
    plt.plot(hist, color=col, label=f'{channel_names[i]} Channel')
    plt.xlim([0, 256])                          # Set x-axis limit from 0 to 256

plt.title("RGB Channel Histograms")
plt.xlabel("Pixel Intensity (0-255)")
plt.ylabel("Frequency")
plt.legend()
plt.grid(alpha=0.3)
plt.show()


# ----------------------------------------------------------------
# 10. Calculate histogram for each separated channel individually
# ----------------------------------------------------------------

# Now each channel is a single grayscale-like array.
# Therefore the channel index is [0] for each one.

hist_r = cv2.calcHist([r_channel], [0], None, [256], [0, 256])
hist_g = cv2.calcHist([g_channel], [0], None, [256], [0, 256])
hist_b = cv2.calcHist([b_channel], [0], None, [256], [0, 256])

# Plot the histograms again
plt.figure(figsize=(10, 6))
plt.plot(hist_r, color='r', label='Red Channel')
plt.plot(hist_g, color='g', label='Green Channel')
plt.plot(hist_b, color='b', label='Blue Channel')
plt.title("RGB Histograms Using Split Channels")
plt.xlabel("Pixel Intensity (0-255)")
plt.ylabel("Frequency")
plt.legend()
plt.grid(alpha=0.3)
plt.xlim([0, 256])
plt.show()


# -----------------------------------------------------------
# 11. Calculate mean and standard deviation from each channel
# -----------------------------------------------------------

# Mean intensity = average brightness of the channel
# Standard deviation = spread of pixel values / contrast amount

red_mean = np.mean(r_channel)
green_mean = np.mean(g_channel)
blue_mean = np.mean(b_channel)

red_std = np.std(r_channel)
green_std = np.std(g_channel)
blue_std = np.std(b_channel)

print("\nChannel Statistics Using Direct Pixel Values")
print("------------------------------------------------")
print(f"Red Channel   -> Mean: {red_mean:.2f}, Standard Deviation: {red_std:.2f}")
print(f"Green Channel -> Mean: {green_mean:.2f}, Standard Deviation: {green_std:.2f}")
print(f"Blue Channel  -> Mean: {blue_mean:.2f}, Standard Deviation: {blue_std:.2f}")


# ----------------------------------------------------------
# 12. Find peak intensity (most frequent value) using hist
# ----------------------------------------------------------

# np.argmax(histogram) returns the index of the maximum value.
# That index represents the most frequent intensity value.

red_peak = np.argmax(hist_r)
green_peak = np.argmax(hist_g)
blue_peak = np.argmax(hist_b)

print("\nPeak Intensity Values")
print("----------------------")
print(f"Red Channel Peak Intensity   : {red_peak}")
print(f"Green Channel Peak Intensity : {green_peak}")
print(f"Blue Channel Peak Intensity  : {blue_peak}")


# -------------------------------------------------------------------
# 13. Channel analysis using a reusable function for cleaner outputs
# -------------------------------------------------------------------

def channel_stats(channel, name):
    """
    This function calculates and prints:
    - Mean intensity
    - Standard deviation
    - Peak intensity
    for a given image channel.
    """

    # Calculate mean intensity from channel pixel values
    mean_value = np.mean(channel)

    # Calculate standard deviation from channel pixel values
    std_value = np.std(channel)

    # Calculate histogram to identify the most common intensity value
    hist = cv2.calcHist([channel], [0], None, [256], [0, 256])

    # Get the intensity with maximum frequency
    peak_value = np.argmax(hist)

    # Print the results
    print(f"{name} Channel")
    print(f"Mean Intensity     : {mean_value:.2f}")
    print(f"Standard Deviation : {std_value:.2f}")
    print(f"Peak Intensity     : {peak_value}")
    print()

    # Return values in case we need them later
    return mean_value, std_value, peak_value


# ------------------------------------------------
# 14. Call the function for each RGB channel
# ------------------------------------------------

print("\nDetailed Channel Analysis")
print("==========================")

red_stats = channel_stats(r_channel, "Red")
green_stats = channel_stats(g_channel, "Green")
blue_stats = channel_stats(b_channel, "Blue")


# ------------------------------------------------------------------
# 15. Histogram-based mean and standard deviation (advanced method)
# ------------------------------------------------------------------

# This section calculates statistics from histogram weights.
# It is mathematically correct and useful for understanding
# histogram-based analysis.

print("Histogram-Based Statistical Analysis")
print("====================================")

for i, name in enumerate(channel_names):

    # Calculate histogram of one channel from the RGB image
    hist = cv2.calcHist([image_rgb], [i], None, [256], [0, 256])

    # Convert histogram from shape (256,1) to shape (256,)
    hist = hist.flatten()

    # Create array of intensity values [0, 1, 2, ..., 255]
    intensity_values = np.arange(256)

    # Weighted mean:
    # sum(intensity * frequency) / total frequency
    mean_intensity = np.average(intensity_values, weights=hist)

    # Weighted standard deviation:
    # sqrt( average((x - mean)^2, weights=hist) )
    std_intensity = np.sqrt(
        np.average((intensity_values - mean_intensity) ** 2, weights=hist)
    )

    # Most frequent intensity value
    peak_intensity = np.argmax(hist)

    print(f"{name} Channel")
    print(f"Mean Intensity     : {mean_intensity:.2f}")
    print(f"Standard Deviation : {std_intensity:.2f}")
    print(f"Peak Intensity     : {peak_intensity}")
    print()


# --------------------------------------
# 16. Final result / conclusion message
# --------------------------------------

print("Lab Completed Successfully.")
print("The image was loaded, converted from BGR to RGB,")
print("displayed correctly, split into RGB channels,")
print("analyzed using histograms, and evaluated using")
print("mean, standard deviation, and peak intensity.")
```

## What this code covers

This full code includes:

* importing required libraries
* reading the image
* checking file existence
* converting **BGR to RGB**
* displaying image correctly
* showing wrong BGR display for comparison
* checking image shape
* splitting **R, G, B** channels
* displaying each channel
* plotting RGB histograms
* calculating:

  * mean intensity
  * standard deviation
  * peak intensity
* histogram-based statistical analysis
* reusable function for channel analysis

## Important viva points from this code

`cv2.imread()` reads the image in **BGR** format.

`cv2.cvtColor(..., cv2.COLOR_BGR2RGB)` converts the image to **RGB** for correct display in Matplotlib.

`cv2.split()` separates the image into three color channels.

`cv2.calcHist()` calculates the histogram of an image or channel.

`np.mean()` gives average intensity.

`np.std()` gives the spread of pixel values.

`np.argmax(hist)` gives the most frequent intensity value, called the **peak intensity**.

