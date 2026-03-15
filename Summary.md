
---

# LAB 01 — RGB Image Loading, Channel Splitting, and Histogram Analysis

## Final Corrected Full Code

```python
# LAB 01
# RGB Image Loading, Channel Splitting, and Histogram Analysis

import cv2
import numpy as np
import matplotlib.pyplot as plt
import os

# -----------------------------
# 1. Load image safely
# -----------------------------
IMAGE_PATH = "Girl.jpg"

if not os.path.exists(IMAGE_PATH):
    raise FileNotFoundError(f"Image not found: {IMAGE_PATH}")

image_bgr = cv2.imread(IMAGE_PATH)

if image_bgr is None:
    raise ValueError(f"Could not read image: {IMAGE_PATH}")

# Convert BGR to RGB for correct display in Matplotlib
image_rgb = cv2.cvtColor(image_bgr, cv2.COLOR_BGR2RGB)

# -----------------------------
# 2. Display original RGB image
# -----------------------------
plt.figure(figsize=(6, 6))
plt.imshow(image_rgb)
plt.title("Original Image (RGB)")
plt.axis("off")
plt.show()

# -----------------------------
# 3. Plot RGB channel histograms
# -----------------------------
colors = ("r", "g", "b")
channel_names = ["Red", "Green", "Blue"]

plt.figure(figsize=(10, 6))

for i, col in enumerate(colors):
    hist = cv2.calcHist([image_rgb], [i], None, [256], [0, 256])
    plt.plot(hist, color=col, label=f"{channel_names[i]} Channel")
    plt.xlim([0, 256])

plt.title("RGB Channel Histograms")
plt.xlabel("Pixel Intensity (0–255)")
plt.ylabel("Frequency")
plt.legend()
plt.grid(alpha=0.3)
plt.show()

# -----------------------------
# 4. Compute weighted statistics
# -----------------------------
print("=" * 60)
print("RGB CHANNEL STATISTICS (from histogram)")
print("=" * 60)

for i, name in enumerate(channel_names):
    hist = cv2.calcHist([image_rgb], [i], None, [256], [0, 256]).flatten()
    mean_intensity = np.average(np.arange(256), weights=hist)
    std_intensity = np.sqrt(
        np.average((np.arange(256) - mean_intensity) ** 2, weights=hist)
    )

    print(f"{name} Channel")
    print(f"  Mean Intensity     : {mean_intensity:.2f}")
    print(f"  Standard Deviation : {std_intensity:.2f}")
    print()

# -----------------------------
# 5. Split image into R, G, B channels
# -----------------------------
r_channel, g_channel, b_channel = cv2.split(image_rgb)

print(f"Original RGB Image Shape : {image_rgb.shape}")
print(f"Red Channel Shape        : {r_channel.shape}")
print(f"Green Channel Shape      : {g_channel.shape}")
print(f"Blue Channel Shape       : {b_channel.shape}")

# -----------------------------
# 6. Compute histograms for split channels
# -----------------------------
hist_r = cv2.calcHist([r_channel], [0], None, [256], [0, 256])
hist_g = cv2.calcHist([g_channel], [0], None, [256], [0, 256])
hist_b = cv2.calcHist([b_channel], [0], None, [256], [0, 256])

plt.figure(figsize=(10, 6))
plt.plot(hist_r, color="r", label="Red Channel")
plt.plot(hist_g, color="g", label="Green Channel")
plt.plot(hist_b, color="b", label="Blue Channel")
plt.title("RGB Channel Histograms using cv2.calcHist()")
plt.xlabel("Pixel Intensity (0–255)")
plt.ylabel("Frequency")
plt.legend()
plt.grid(alpha=0.3)
plt.show()

# -----------------------------
# 7. Display each channel as image
# -----------------------------
plt.figure(figsize=(15, 5))

plt.subplot(1, 3, 1)
plt.imshow(r_channel, cmap="Reds")
plt.title("Red Channel")
plt.axis("off")

plt.subplot(1, 3, 2)
plt.imshow(g_channel, cmap="Greens")
plt.title("Green Channel")
plt.axis("off")

plt.subplot(1, 3, 3)
plt.imshow(b_channel, cmap="Blues")
plt.title("Blue Channel")
plt.axis("off")

plt.show()

# -----------------------------
# 8. Channel statistics function
# -----------------------------
def channel_stats(channel, name):
    mean = np.mean(channel)
    std = np.std(channel)
    hist = cv2.calcHist([channel], [0], None, [256], [0, 256])
    peak = np.argmax(hist)

    print(f"{name:5} channel | Mean: {mean:6.1f} | Std: {std:5.1f} | Peak Intensity: {peak:3}")
    return mean, std, peak

print("\n" + "=" * 70)
print("RGB CHANNEL STATISTICS (direct pixel analysis)")
print("-" * 70)

r_stats = channel_stats(r_channel, "Red")
g_stats = channel_stats(g_channel, "Green")
b_stats = channel_stats(b_channel, "Blue")

print("=" * 70)
```

## Explanation

This lab teaches the basics of working with a color image.

`cv2.imread()` reads the image in **BGR** order, but Matplotlib expects **RGB**, so `cv2.cvtColor(..., cv2.COLOR_BGR2RGB)` is necessary for correct display.

Then the code calculates RGB histograms. A histogram shows how many pixels fall into each intensity value from `0` to `255`.

Next, the image is split into separate red, green, and blue channels using `cv2.split()`. Each channel is then analyzed visually and numerically.

The statistics part computes:

* **mean** = average brightness of the channel
* **standard deviation** = spread of intensity values
* **peak intensity** = most frequent pixel value

This lab is mainly about understanding image representation in RGB form.

---

# LAB 02 — Advanced Histogram Analysis, Grayscale, CDF, HSV, YUV, Exposure, and Brightness

## Final Corrected Full Code

```python
# LAB 02
# Advanced Histogram Analysis, Grayscale, CDF, HSV, YUV, Exposure, and Brightness

import cv2
import numpy as np
import matplotlib.pyplot as plt
import os

# -----------------------------
# 1. Load image safely
# -----------------------------
IMAGE_PATH = "Girl.jpg"

if not os.path.exists(IMAGE_PATH):
    raise FileNotFoundError(f"Image not found: {IMAGE_PATH}")

image_bgr = cv2.imread(IMAGE_PATH)
if image_bgr is None:
    raise ValueError(f"Could not read image: {IMAGE_PATH}")

image_rgb = cv2.cvtColor(image_bgr, cv2.COLOR_BGR2RGB)

# -----------------------------
# 2. Split RGB channels
# -----------------------------
r_channel, g_channel, b_channel = cv2.split(image_rgb)

hist_r = cv2.calcHist([r_channel], [0], None, [256], [0, 256])
hist_g = cv2.calcHist([g_channel], [0], None, [256], [0, 256])
hist_b = cv2.calcHist([b_channel], [0], None, [256], [0, 256])

mean_r = np.mean(r_channel)
mean_g = np.mean(g_channel)
mean_b = np.mean(b_channel)

# -----------------------------
# 3. Show channel-only images + histograms
# -----------------------------
plt.figure(figsize=(16, 10))

channels = [
    (r_channel, hist_r, "Red", "red", mean_r, 0),
    (g_channel, hist_g, "Green", "green", mean_g, 1),
    (b_channel, hist_b, "Blue", "blue", mean_b, 2),
]

for i, (ch, hist, name, color, mean_val, channel_index) in enumerate(channels):
    plt.subplot(2, 3, i + 1)
    masked = np.zeros_like(image_rgb)
    masked[:, :, channel_index] = ch
    plt.imshow(masked)
    plt.title(f"{name} Channel Only", fontsize=14, fontweight="bold", color=color)
    plt.axis("off")

    plt.subplot(2, 3, i + 4)
    plt.axvspan(0, 85, color="darkred", alpha=0.15, label="Low")
    plt.axvspan(86, 170, color="grey", alpha=0.15, label="Mid")
    plt.axvspan(171, 255, color="yellow", alpha=0.15, label="High")

    plt.plot(hist, color=color, linewidth=2, label=f"{name} Histogram")
    plt.fill_between(range(256), hist.flatten(), color=color, alpha=0.3)
    plt.axvline(mean_val, color="black", linestyle="--", linewidth=2,
                label=f"Mean = {mean_val:.1f}")

    plt.xlabel("Pixel Intensity (0–255)")
    plt.ylabel("Pixel Count")
    plt.grid(True, alpha=0.3)
    plt.xlim([0, 255])
    plt.legend()

plt.suptitle("RGB Channel Analysis", fontsize=18, fontweight="bold", y=0.98)
plt.tight_layout()
plt.show()

# -----------------------------
# 4. Overlay RGB histograms
# -----------------------------
plt.figure(figsize=(14, 7))
x = range(256)

plt.fill_between(x, hist_r.flatten(), alpha=0.4, color="red", label="Red Channel")
plt.fill_between(x, hist_g.flatten(), alpha=0.4, color="green", label="Green Channel")
plt.fill_between(x, hist_b.flatten(), alpha=0.4, color="blue", label="Blue Channel")

plt.plot(hist_r, color="red", alpha=0.9, linewidth=2.5)
plt.plot(hist_g, color="green", alpha=0.9, linewidth=2.5)
plt.plot(hist_b, color="blue", alpha=0.9, linewidth=2.5)

plt.axvline(mean_r, color="red", linestyle="--", alpha=0.7, linewidth=1.5)
plt.axvline(mean_g, color="green", linestyle="--", alpha=0.7, linewidth=1.5)
plt.axvline(mean_b, color="blue", linestyle="--", alpha=0.7, linewidth=1.5)

plt.title("RGB Overlay Histogram", fontsize=18, fontweight="bold")
plt.xlabel("Pixel Intensity (0-255)")
plt.ylabel("Pixel Frequency")
plt.grid(True, alpha=0.2)
plt.xlim(0, 255)
plt.legend()
plt.tight_layout()
plt.show()

# -----------------------------
# 5. Grayscale histogram
# -----------------------------
gray = cv2.cvtColor(image_bgr, cv2.COLOR_BGR2GRAY)
hist_gray = cv2.calcHist([gray], [0], None, [256], [0, 256])

plt.figure(figsize=(10, 5))
plt.plot(hist_gray, color="black")
plt.title("Grayscale Histogram")
plt.xlabel("Intensity (0–255)")
plt.ylabel("Number of Pixels")
plt.xlim([0, 256])
plt.grid(True, alpha=0.3)
plt.show()

# -----------------------------
# 6. Cumulative Distribution Function (CDF)
# -----------------------------
cdf = hist_gray.cumsum()
cdf_normalized = cdf * hist_gray.max() / cdf.max()

plt.figure(figsize=(10, 5))
plt.plot(hist_gray, color="black", alpha=0.7, label="Histogram")
plt.plot(cdf_normalized, color="red", label="CDF (normalized)")
plt.title("Histogram and CDF")
plt.xlabel("Intensity")
plt.ylabel("Count")
plt.legend()
plt.grid(True, alpha=0.3)
plt.show()

# -----------------------------
# 7. HSV histograms
# -----------------------------
hsv = cv2.cvtColor(image_bgr, cv2.COLOR_BGR2HSV)
h, s, v = cv2.split(hsv)

fig, ax = plt.subplots(1, 3, figsize=(15, 4))
channels_hsv = [h, s, v]
names_hsv = ["Hue", "Saturation", "Value"]
colors_hsv = ["red", "green", "blue"]

for i in range(3):
    hist_ch = cv2.calcHist([channels_hsv[i]], [0], None, [256], [0, 256])
    ax[i].plot(hist_ch, color=colors_hsv[i])
    ax[i].set_title(f"{names_hsv[i]} Histogram")
    ax[i].set_xlim([0, 256])
    ax[i].grid(True, alpha=0.3)

plt.tight_layout()
plt.show()

# -----------------------------
# 8. YUV Y-channel comparison
# -----------------------------
yuv = cv2.cvtColor(image_bgr, cv2.COLOR_BGR2YUV)
y, u, v_yuv = cv2.split(yuv)
hist_y = cv2.calcHist([y], [0], None, [256], [0, 256])

plt.figure(figsize=(10, 5))
plt.plot(hist_gray, color="black", label="Grayscale")
plt.plot(hist_y, color="orange", label="Y-Channel (YUV)")
plt.title("Grayscale vs Y-Channel Histogram")
plt.xlabel("Intensity")
plt.ylabel("Count")
plt.legend()
plt.grid(True, alpha=0.3)
plt.show()

# -----------------------------
# 9. Exposure analysis
# -----------------------------
def adjust_exposure(img, factor):
    return np.clip(img.astype(np.float32) * factor, 0, 255).astype(np.uint8)

good_exp = gray.copy()
high_exp = adjust_exposure(gray, 2.0)
low_exp = adjust_exposure(gray, 0.4)

def compute_hist(img):
    return cv2.calcHist([img], [0], None, [256], [0, 256])

hist_good_exp = compute_hist(good_exp)
hist_high_exp = compute_hist(high_exp)
hist_low_exp = compute_hist(low_exp)

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

print("\nEXPOSURE STATISTICS")
print("-" * 80)
exposure_stats(low_exp,  "Low Exp.")
exposure_stats(good_exp, "Good Exp.")
exposure_stats(high_exp, "High Exp.")
print("-" * 80)

# -----------------------------
# 10. Brightness analysis
# -----------------------------
def adjust_brightness(img, delta):
    return np.clip(img.astype(np.int16) + delta, 0, 255).astype(np.uint8)

good_bri = gray.copy()
high_bri = adjust_brightness(gray, +100)
low_bri = adjust_brightness(gray, -100)

hist_good_bri = compute_hist(good_bri)
hist_high_bri = compute_hist(high_bri)
hist_low_bri = compute_hist(low_bri)

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

print("\nBRIGHTNESS STATISTICS")
print("-" * 100)
brightness_stats(low_bri,  "Low Bright.")
brightness_stats(good_bri, "Good Bright.")
brightness_stats(high_bri, "High Bright.")
print("-" * 100)
```

## Explanation

This lab extends histogram analysis.

First, the RGB channels are visualized separately, and their histograms are shown with low, mid, and high intensity regions. Then the three histograms are overlaid in one graph for comparison.

The grayscale histogram is created by converting the image to a single channel. After that, the **CDF** is computed using cumulative summation. The CDF shows how intensity values accumulate from dark to bright.

The code then converts the image into **HSV** and **YUV** color spaces:

* **HSV** separates hue, saturation, and brightness
* **YUV** separates brightness from color information

Finally, the code studies **exposure** and **brightness**:

* exposure is simulated by multiplying pixel values
* brightness is simulated by adding or subtracting a constant

This is useful in viva questions because students are often asked the difference between exposure and brightness.

---

# LAB 03 — Histogram Equalization

## Final Corrected Full Code

```python
# LAB 03
# Histogram Equalization

import cv2
import numpy as np
import matplotlib.pyplot as plt
import os

# -----------------------------
# 1. Load image safely
# -----------------------------
IMAGE_PATH = "Girl.jpg"

if not os.path.exists(IMAGE_PATH):
    raise FileNotFoundError(f"Image not found: {IMAGE_PATH}")

img = cv2.imread(IMAGE_PATH)

if img is None:
    raise ValueError(f"Could not read image: {IMAGE_PATH}")

# Convert BGR to RGB for display
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

# -----------------------------
# 2. Grayscale histogram equalization
# -----------------------------
img_gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
img_gray_eq = cv2.equalizeHist(img_gray)

# -----------------------------
# 3. Color histogram equalization using YUV
# -----------------------------
img_yuv = cv2.cvtColor(img, cv2.COLOR_BGR2YUV)
img_yuv_original = img_yuv.copy()

# Equalize only Y channel
img_yuv[:, :, 0] = cv2.equalizeHist(img_yuv[:, :, 0])

# Convert back to RGB
img_color_eq = cv2.cvtColor(img_yuv, cv2.COLOR_YUV2RGB)

# -----------------------------
# 4. Plot results
# -----------------------------
plt.figure(figsize=(15, 10))

plt.subplot(2, 4, 1)
plt.imshow(img_rgb)
plt.title("Original RGB Image")
plt.axis("off")

plt.subplot(2, 4, 2)
plt.imshow(img_gray, cmap="gray")
plt.title("Grayscale")
plt.axis("off")

plt.subplot(2, 4, 3)
plt.imshow(img_gray_eq, cmap="gray")
plt.title("Grayscale + Histogram Equalization")
plt.axis("off")

plt.subplot(2, 4, 4)
plt.imshow(img_color_eq)
plt.title("Color Equalization (YUV)")
plt.axis("off")

plt.subplot(2, 4, 5)
plt.hist(img_gray.ravel(), bins=256, range=(0, 256), color="gray", alpha=0.7, label="Original")
plt.title("Histogram (Grayscale Original)")
plt.xlabel("Pixel Intensity")
plt.ylabel("Frequency")
plt.legend()

plt.subplot(2, 4, 6)
plt.hist(img_gray_eq.ravel(), bins=256, range=(0, 256), color="black", alpha=0.7, label="Equalized")
plt.title("Histogram (Grayscale Equalized)")
plt.xlabel("Pixel Intensity")
plt.ylabel("Frequency")
plt.legend()

plt.subplot(2, 4, 7)
plt.hist(img_yuv_original[:, :, 0].ravel(), bins=256, range=(0, 256), color="orange", alpha=0.7, label="Original Y")
plt.title("Histogram of Y channel (Before)")
plt.xlabel("Pixel Intensity")
plt.ylabel("Frequency")
plt.legend()

plt.subplot(2, 4, 8)
plt.hist(img_yuv[:, :, 0].ravel(), bins=256, range=(0, 256), color="red", alpha=0.7, label="Equalized Y")
plt.title("Histogram of Y channel (After)")
plt.xlabel("Pixel Intensity")
plt.ylabel("Frequency")
plt.legend()

plt.tight_layout()
plt.show()

# -----------------------------
# 5. Print summary
# -----------------------------
print("Histogram equalization completed successfully!")
print(f"Image shape: {img.shape}")
print(f"Original grayscale intensity range: {img_gray.min()} - {img_gray.max()}")
print(f"Equalized grayscale intensity range: {img_gray_eq.min()} - {img_gray_eq.max()}")
```

## Explanation

Histogram equalization improves contrast.

The grayscale part uses `cv2.equalizeHist()` directly on the grayscale image. This redistributes intensities so that they spread more across the full `0–255` range.

For the color image, the code first converts the image to **YUV**. Only the **Y channel** is equalized, because Y represents luminance. The color channels are left unchanged as much as possible. Then the image is converted back to RGB.

This lab is important because many exam questions ask why we do not equalize all RGB channels independently. The answer is that equalizing only the luminance channel usually preserves natural colors better.

---

# LAB 04 — Image Smoothing with Averaging Filter and Padding Methods

## Final Corrected Full Code

```python
# LAB 04
# Image Smoothing with 3x3 Averaging Filter and Padding Methods

import cv2
import numpy as np
import matplotlib.pyplot as plt
import os

# -----------------------------
# 1. Load image safely
# -----------------------------
IMAGE_PATH = "Girl.jpg"

if not os.path.exists(IMAGE_PATH):
    raise FileNotFoundError(f"Image not found: {IMAGE_PATH}")

img = cv2.imread(IMAGE_PATH)
if img is None:
    raise ValueError(f"Could not read image: {IMAGE_PATH}")

img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

print(f"Image loaded successfully!")
print(f"Original size: {img.shape[1]}x{img.shape[0]}")
print(f"Grayscale shape: {gray.shape}")

# -----------------------------
# 2. Manual 3x3 averaging filter
# -----------------------------
def average_filter_manual(image):
    h, w = image.shape
    output = np.zeros_like(image)

    padded = np.pad(image, pad_width=1, mode='edge')

    for y in range(h):
        for x in range(w):
            neighborhood = padded[y:y+3, x:x+3]
            output[y, x] = np.mean(neighborhood)

    return output.astype(np.uint8)

smoothed_manual = average_filter_manual(gray)

# -----------------------------
# 3. filter2D smoothing
# -----------------------------
kernel_3x3 = np.ones((3, 3), np.float32) / 9.0
smoothed_filter2d = cv2.filter2D(gray, -1, kernel_3x3)

# -----------------------------
# 4. Fast blur smoothing
# -----------------------------
smoothed_fast = cv2.blur(gray, (3, 3))

# -----------------------------
# 5. Add noise and smooth
# -----------------------------
noise = np.random.randint(-30, 31, gray.shape, dtype=np.int16)
noisy = np.clip(gray.astype(np.int16) + noise, 0, 255).astype(np.uint8)
noisy_smoothed = cv2.blur(noisy, (3, 3))

# -----------------------------
# 6. Display smoothing results
# -----------------------------
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
axes[0, 3].set_title('Noisy → Smoothed')
axes[0, 3].axis('off')

kernel_display = axes[1, 0].imshow(kernel_3x3, cmap='jet', interpolation='nearest')
axes[1, 0].set_title('3×3 Averaging Kernel')
plt.colorbar(kernel_display, ax=axes[1, 0], fraction=0.046, pad=0.04)

diff = cv2.absdiff(gray, smoothed_fast)
diff_display = axes[1, 1].imshow(diff, cmap='hot', vmin=0, vmax=50)
axes[1, 1].set_title('Difference (Original - Smoothed)')
axes[1, 1].axis('off')
plt.colorbar(diff_display, ax=axes[1, 1], fraction=0.046, pad=0.04)

axes[1, 2].imshow(img_rgb)
axes[1, 2].set_title('Original Color Image')
axes[1, 2].axis('off')

smoothed_color = cv2.blur(img_rgb, (3, 3))
axes[1, 3].imshow(smoothed_color)
axes[1, 3].set_title('Color Image Smoothed')
axes[1, 3].axis('off')

plt.suptitle('Image Smoothing using 3×3 Averaging Filter', fontsize=18, y=1.02)
plt.tight_layout()
plt.show()

# -----------------------------
# 7. Compare methods numerically
# -----------------------------
print("Difference between manual and cv2.filter2D():")
diff_methods = cv2.absdiff(smoothed_manual, smoothed_filter2d)
print(f"Max difference: {diff_methods.max()}")
print(f"Mean difference: {diff_methods.mean():.6f}")

print("\nDifference between cv2.filter2D() and cv2.blur():")
diff_methods2 = cv2.absdiff(smoothed_filter2d, smoothed_fast)
print(f"Max difference: {diff_methods2.max()}")
print(f"Mean difference: {diff_methods2.mean():.6f}")

# -----------------------------
# 8. Create synthetic test image for padding study
# -----------------------------
def create_test_image(size=200):
    img = np.zeros((size, size), dtype=np.uint8)

    for i in range(size):
        img[:, i] = i * 255 // size

    img[50:150, 50:150] = 255
    cv2.circle(img, (150, 150), 30, 0, -1)

    return img

gray_test = create_test_image(200)

plt.figure(figsize=(5, 5))
plt.imshow(gray_test, cmap='gray')
plt.title('Original Test Image')
plt.axis('off')
plt.show()

# -----------------------------
# 9. Padding modes
# -----------------------------
kernel = np.ones((3, 3), np.float32) / 9
pad = 1

padding_modes = [
    ("Zero Padding (BORDER_CONSTANT)", cv2.BORDER_CONSTANT, 0),
    ("Edge Padding (BORDER_REPLICATE)", cv2.BORDER_REPLICATE, None),
    ("Mirror Padding (BORDER_REFLECT)", cv2.BORDER_REFLECT, None),
    ("Mirror 101 (BORDER_REFLECT_101)", cv2.BORDER_REFLECT_101, None),
    ("Wrap Padding (BORDER_WRAP)", cv2.BORDER_WRAP, None),
    ("No Padding (Valid Convolution)", None, None),
]

results = []
descriptions = []

for name, border_type, const_value in padding_modes:
    if border_type is None:
        filtered = cv2.filter2D(gray_test, -1, kernel, borderType=cv2.BORDER_CONSTANT)
        cropped = filtered[pad:-pad, pad:-pad]
        results.append(cropped)
        descriptions.append(f"{name}\n{cropped.shape[1]}×{cropped.shape[0]}")

    elif border_type == cv2.BORDER_CONSTANT:
        padded = cv2.copyMakeBorder(gray_test, pad, pad, pad, pad,
                                    borderType=border_type, value=const_value)
        filtered_full = cv2.filter2D(padded, -1, kernel, borderType=border_type)
        filtered = filtered_full[pad:-pad, pad:-pad]
        results.append(filtered)
        descriptions.append(f"{name}\n{filtered.shape[1]}×{filtered.shape[0]}")

    elif border_type == cv2.BORDER_WRAP:
        padded = cv2.copyMakeBorder(gray_test, pad, pad, pad, pad, borderType=border_type)
        filtered_full = cv2.filter2D(padded, -1, kernel, borderType=cv2.BORDER_REPLICATE)
        filtered = filtered_full[pad:-pad, pad:-pad]
        results.append(filtered)
        descriptions.append(f"{name}\n{filtered.shape[1]}×{filtered.shape[0]}")

    else:
        filtered = cv2.filter2D(gray_test, -1, kernel, borderType=border_type)
        results.append(filtered)
        descriptions.append(f"{name}\n{filtered.shape[1]}×{filtered.shape[0]}")

# -----------------------------
# 10. Display padding results
# -----------------------------
fig, axes = plt.subplots(3, 3, figsize=(15, 12))
axes_flat = axes.flatten()

axes_flat[0].imshow(gray_test, cmap='gray')
axes_flat[0].set_title(f'Original Image\n{gray_test.shape[1]}×{gray_test.shape[0]}', fontsize=12, fontweight='bold')
axes_flat[0].axis('off')

for i, (img_out, title) in enumerate(zip(results, descriptions), 1):
    axes_flat[i].imshow(img_out, cmap='gray')
    axes_flat[i].set_title(title, fontsize=11)
    axes_flat[i].axis('off')

for i in range(len(results) + 1, len(axes_flat)):
    axes_flat[i].axis('off')

plt.suptitle('Effects of Different Padding Methods in Convolution',
             fontsize=16, fontweight='bold', y=1.02)
plt.tight_layout()
plt.show()
```

## Explanation

This lab has two main parts.

The first part demonstrates **image smoothing** using a 3×3 averaging filter. The same operation is shown in three ways:

* manual implementation
* `cv2.filter2D()`
* `cv2.blur()`

This is useful because it helps you understand both the theory and the built-in OpenCV shortcuts.

The second part studies **padding in convolution**. Padding is needed because pixels on the border do not have a complete neighborhood around them.

Different padding methods change border behavior:

* constant padding adds fixed values
* replicate repeats edge pixels
* reflect mirrors the border
* wrap copies values from the opposite side
* valid convolution removes the border area

This lab is important in practical exams because they often ask why output near the border changes depending on padding.

---

# LAB 05 — Mean Filter, Gaussian Filter, Sobel Filter, and Median Filter

## Final Corrected Full Code

```python
# LAB 05
# Mean Filter, Gaussian Filter, Sobel Filter, and Median Filter

import cv2
import numpy as np
import matplotlib.pyplot as plt
import os

# -----------------------------
# 1. Load image safely
# -----------------------------
IMAGE_PATH = "girl.jpg"

if not os.path.exists(IMAGE_PATH):
    raise FileNotFoundError(f"Image not found: {IMAGE_PATH}")

image = cv2.imread(IMAGE_PATH, cv2.IMREAD_GRAYSCALE)

if image is None:
    raise ValueError(f"Could not read image: {IMAGE_PATH}")

plt.figure(figsize=(5, 5))
plt.imshow(image, cmap="gray")
plt.title("Original Grayscale Image")
plt.axis("off")
plt.show()

# -----------------------------
# 2. Mean filter
# -----------------------------
def mean_filter(image, kernel_size):
    if kernel_size % 2 == 0:
        kernel_size += 1

    pad_size = kernel_size // 2

    padded_image = cv2.copyMakeBorder(
        image,
        pad_size, pad_size, pad_size, pad_size,
        cv2.BORDER_CONSTANT,
        value=0
    )

    filtered_image = np.zeros_like(image, dtype=np.float32)

    for y in range(image.shape[0]):
        for x in range(image.shape[1]):
            window = padded_image[y:y+kernel_size, x:x+kernel_size]
            filtered_image[y, x] = np.mean(window)

    return np.clip(filtered_image, 0, 255).astype(np.uint8)

mean_filtered = mean_filter(image, 5)

# -----------------------------
# 3. Gaussian filter
# -----------------------------
def gaussian_kernel(size, sigma=1.0):
    if size % 2 == 0:
        size += 1

    ax = np.arange(-size // 2 + 1., size // 2 + 1.)
    xx, yy = np.meshgrid(ax, ax)
    kernel = np.exp(-(xx**2 + yy**2) / (2. * sigma**2))
    return kernel / np.sum(kernel)

def convolve(image, kernel):
    pad = kernel.shape[0] // 2
    padded_image = cv2.copyMakeBorder(image, pad, pad, pad, pad, cv2.BORDER_CONSTANT)
    output = np.zeros_like(image, dtype=np.float32)

    for y in range(image.shape[0]):
        for x in range(image.shape[1]):
            window = padded_image[y:y+kernel.shape[0], x:x+kernel.shape[1]]
            output[y, x] = np.sum(window * kernel)

    return np.clip(output, 0, 255).astype(np.uint8)

gaussian_k = gaussian_kernel(5, sigma=1.0)
gaussian_filtered = convolve(image, gaussian_k)

# -----------------------------
# 4. Sobel filter
# -----------------------------
def sobel_filter(image):
    sobel_x_kernel = np.array([[-1, 0, 1],
                               [-2, 0, 2],
                               [-1, 0, 1]], dtype=np.float32)

    sobel_y_kernel = np.array([[-1, -2, -1],
                               [ 0,  0,  0],
                               [ 1,  2,  1]], dtype=np.float32)

    sobel_x = cv2.filter2D(image.astype(np.float32), cv2.CV_32F, sobel_x_kernel)
    sobel_y = cv2.filter2D(image.astype(np.float32), cv2.CV_32F, sobel_y_kernel)

    gradient_magnitude = np.sqrt(sobel_x**2 + sobel_y**2)

    normalized_gradient = cv2.normalize(
        gradient_magnitude, None, 0, 255, cv2.NORM_MINMAX
    )

    return normalized_gradient.astype(np.uint8)

sobel_edges = sobel_filter(image)

# -----------------------------
# 5. Median filter
# -----------------------------
def custom_median_filter(image, kernel_size):
    height, width = image.shape
    filtered_image = np.zeros_like(image)
    kernel_half = kernel_size // 2

    padded_image = cv2.copyMakeBorder(
        image,
        kernel_half, kernel_half,
        kernel_half, kernel_half,
        cv2.BORDER_CONSTANT
    )

    for i in range(height):
        for j in range(width):
            window = []
            for ki in range(-kernel_half, kernel_half + 1):
                for kj in range(-kernel_half, kernel_half + 1):
                    window.append(
                        padded_image[i + ki + kernel_half, j + kj + kernel_half]
                    )

            window.sort()
            median_value = window[len(window) // 2]
            filtered_image[i, j] = median_value

    return filtered_image

median_filtered = custom_median_filter(image, 5)

# -----------------------------
# 6. Display all results
# -----------------------------
plt.figure(figsize=(14, 10))

plt.subplot(2, 3, 1)
plt.imshow(image, cmap="gray")
plt.title("Original Image")
plt.axis("off")

plt.subplot(2, 3, 2)
plt.imshow(mean_filtered, cmap="gray")
plt.title("Mean Filter")
plt.axis("off")

plt.subplot(2, 3, 3)
plt.imshow(gaussian_filtered, cmap="gray")
plt.title("Gaussian Filter")
plt.axis("off")

plt.subplot(2, 3, 4)
plt.imshow(sobel_edges, cmap="gray")
plt.title("Sobel Edge Detection")
plt.axis("off")

plt.subplot(2, 3, 5)
plt.imshow(median_filtered, cmap="gray")
plt.title("Median Filter")
plt.axis("off")

plt.tight_layout()
plt.show()

# -----------------------------
# 7. Show Gaussian kernel
# -----------------------------
plt.figure(figsize=(4, 4))
plt.imshow(gaussian_k, cmap="viridis")
plt.title("Gaussian Kernel (5x5)")
plt.colorbar()
plt.show()
```

## Explanation

This lab focuses on four major filters.

The **mean filter** replaces each pixel with the average of its neighbors. It smooths the image but also blurs edges.

The **Gaussian filter** also smooths the image, but it uses weighted values. Pixels near the center have more importance than pixels farther away. This often gives better visual results than the mean filter.

The **Sobel filter** is different because it is not used to smooth. It detects edges by calculating horizontal and vertical intensity changes. After combining those changes, the result highlights boundaries in the image.

The **median filter** replaces a pixel with the median value in its neighborhood. This is especially useful for removing salt-and-pepper noise while preserving edges better than averaging filters.

---

# Final short exam summary

For your practical exam, remember these core ideas:

**Lab 01:** image loading, BGR to RGB conversion, RGB histograms, channel splitting
**Lab 02:** advanced histogram analysis, grayscale, CDF, HSV, YUV, exposure, brightness
**Lab 03:** histogram equalization in grayscale and color using YUV
**Lab 04:** averaging filter, smoothing, noise reduction, convolution padding methods
**Lab 05:** mean, Gaussian, Sobel, and median filters

