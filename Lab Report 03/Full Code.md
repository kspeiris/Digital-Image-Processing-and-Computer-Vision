
---

# Full Complete Code with Explain Comments

```python
# Import OpenCV library for image processing tasks
import cv2

# Import NumPy for numerical operations
# (In this code it is imported, but not used much directly)
import numpy as np

# Import Matplotlib for displaying images and histograms
import matplotlib.pyplot as plt

# Import os to check whether the image file exists
import os


# =========================================================
# STEP 1: GIVE THE IMAGE PATH
# =========================================================

# Path of the input image
# This should point to the image you want to process
image_path = "/content/sample_data/Girl.jpg"


# =========================================================
# STEP 2: CHECK IF THE FILE EXISTS
# =========================================================

# os.path.exists() returns True if the file is found
# If the file is missing, this block will print an error message
if not os.path.exists(image_path):
    print(f"❌ Error: Image file '{image_path}' not found!")

    # Helpful troubleshooting messages
    print("\nTroubleshooting:")
    print("1. Make sure 'Girl.png' is in the same directory as your notebook")
    print("2. Or provide the full path, e.g., '/content/Girl.png' or 'path/to/Girl.png'")

    # Show all image files in the current directory
    print("\nFiles in current directory:")
    print([f for f in os.listdir('.') if f.endswith(('.png', '.jpg', '.jpeg'))])

else:
    # =========================================================
    # STEP 3: READ THE IMAGE
    # =========================================================

    # cv2.imread() reads the image from the given path
    # OpenCV reads color images in BGR format by default
    img = cv2.imread(image_path)

    # Check whether OpenCV successfully loaded the image
    if img is None:
        print(f"❌ Error: Could not read '{image_path}'")
        print("Make sure it's a valid image file (PNG, JPG, etc.)")

    else:
        # =========================================================
        # STEP 4: CONVERT BGR IMAGE TO RGB
        # =========================================================

        # OpenCV uses BGR format, but Matplotlib displays images correctly in RGB
        # So we convert BGR → RGB before displaying
        img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

        # =========================================================
        # STEP 5: GRAYSCALE HISTOGRAM EQUALIZATION
        # =========================================================

        # Convert the original BGR image into grayscale
        # Grayscale image has only one intensity channel
        img_gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

        # Apply histogram equalization on the grayscale image
        # This improves contrast by spreading intensity values
        img_gray_eq = cv2.equalizeHist(img_gray)

        # =========================================================
        # STEP 6: COLOR HISTOGRAM EQUALIZATION USING YUV
        # =========================================================

        # Convert the original BGR image into YUV color space
        # Y = luminance (brightness)
        # U and V = color components
        img_yuv = cv2.cvtColor(img, cv2.COLOR_BGR2YUV)

        # Store a copy of the original YUV image
        # This is useful because later we want to compare the original Y channel histogram
        img_yuv_original = img_yuv.copy()

        # Equalize only the Y channel (brightness channel)
        # img_yuv[:, :, 0] means:
        # all rows, all columns, channel 0 (Y channel)
        img_yuv[:, :, 0] = cv2.equalizeHist(img_yuv[:, :, 0])

        # Convert the updated YUV image back to RGB for display
        img_color_eq = cv2.cvtColor(img_yuv, cv2.COLOR_YUV2RGB)

        # =========================================================
        # STEP 7: CREATE A FIGURE TO DISPLAY ALL RESULTS
        # =========================================================

        # Create a figure with width 15 and height 10
        plt.figure(figsize=(15, 10))

        # =========================================================
        # STEP 8: SHOW ORIGINAL RGB IMAGE
        # =========================================================

        # Create subplot at position 1 in a 2x4 grid
        plt.subplot(2, 4, 1)

        # Display the RGB image
        plt.imshow(img_rgb)

        # Add title
        plt.title('Original RGB Image')

        # Hide axis for cleaner output
        plt.axis('off')

        # =========================================================
        # STEP 9: SHOW GRAYSCALE IMAGE
        # =========================================================

        plt.subplot(2, 4, 2)
        plt.imshow(img_gray, cmap='gray')   # cmap='gray' displays image in grayscale
        plt.title('Grayscale')
        plt.axis('off')

        # =========================================================
        # STEP 10: SHOW EQUALIZED GRAYSCALE IMAGE
        # =========================================================

        plt.subplot(2, 4, 3)
        plt.imshow(img_gray_eq, cmap='gray')
        plt.title('Grayscale + Histogram Equalization')
        plt.axis('off')

        # =========================================================
        # STEP 11: SHOW COLOR EQUALIZED IMAGE
        # =========================================================

        plt.subplot(2, 4, 4)
        plt.imshow(img_color_eq)
        plt.title('Color Equalizer (YUV)')
        plt.axis('off')

        # =========================================================
        # STEP 12: PLOT HISTOGRAM OF ORIGINAL GRAYSCALE IMAGE
        # =========================================================

        plt.subplot(2, 4, 5)

        # img_gray.ravel() converts the 2D grayscale image into a 1D array
        # This makes it easy to compute the histogram of all pixel values
        plt.hist(
            img_gray.ravel(),
            bins=256,              # 256 intensity bins (0 to 255)
            range=(0, 256),        # intensity range
            color='gray',
            alpha=0.7,             # transparency
            label='Original'
        )

        plt.title('Histogram (Grayscale Original)')
        plt.xlabel('Pixel Intensity')
        plt.ylabel('Frequency')
        plt.legend()

        # =========================================================
        # STEP 13: PLOT HISTOGRAM OF EQUALIZED GRAYSCALE IMAGE
        # =========================================================

        plt.subplot(2, 4, 6)
        plt.hist(
            img_gray_eq.ravel(),
            bins=256,
            range=(0, 256),
            color='black',
            alpha=0.7,
            label='Equalized'
        )

        plt.title('Histogram (Grayscale Equalized)')
        plt.xlabel('Pixel Intensity')
        plt.ylabel('Frequency')
        plt.legend()

        # =========================================================
        # STEP 14: PLOT ORIGINAL Y CHANNEL HISTOGRAM
        # =========================================================

        plt.subplot(2, 4, 7)

        # img_yuv_original[:, :, 0] extracts the original Y channel
        # ravel() converts it into 1D for histogram plotting
        plt.hist(
            img_yuv_original[:, :, 0].ravel(),
            bins=256,
            range=(0, 256),
            color='orange',
            alpha=0.7,
            label='Original Y'
        )

        plt.title('Histogram of Y channel (Before)')
        plt.xlabel('Pixel Intensity')
        plt.ylabel('Frequency')
        plt.legend()

        # =========================================================
        # STEP 15: PLOT EQUALIZED Y CHANNEL HISTOGRAM
        # =========================================================

        plt.subplot(2, 4, 8)

        # img_yuv[:, :, 0] now contains the equalized Y channel
        plt.hist(
            img_yuv[:, :, 0].ravel(),
            bins=256,
            range=(0, 256),
            color='red',
            alpha=0.7,
            label='Equalized Y'
        )

        plt.title('Histogram of Y channel (After)')
        plt.xlabel('Pixel Intensity')
        plt.ylabel('Frequency')
        plt.legend()

        # =========================================================
        # STEP 16: ADJUST LAYOUT AND SHOW ALL PLOTS
        # =========================================================

        # Adjust spacing between subplots automatically
        plt.tight_layout()

        # Display the full figure
        plt.show()

        # =========================================================
        # STEP 17: PRINT FINAL RESULT DETAILS
        # =========================================================

        print("✓ Histogram equalization completed successfully!")

        # Print the shape of the original image
        # Example: (height, width, 3)
        print(f"  Image shape: {img.shape}")

        # Print grayscale intensity range before equalization
        print(f"  Original image intensity range: {img_gray.min()} - {img_gray.max()}")

        # Print grayscale intensity range after equalization
        print(f"  Equalized image intensity range: {img_gray_eq.min()} - {img_gray_eq.max()}")
```

---

# What this code does

This uploaded lab code performs **two types of histogram equalization**:

## 1. Grayscale Histogram Equalization

* converts the image to grayscale
* enhances contrast using `cv2.equalizeHist()`
* compares original and equalized histograms

## 2. Color Histogram Equalization using YUV

* converts the image to YUV color space
* equalizes only the **Y channel** (brightness)
* converts the image back to RGB
* preserves color better than equalizing RGB channels separately

---

# Important note from your uploaded file

Your uploaded notebook really contains this line in the troubleshooting text:

```python
print("1. Make sure 'Girl.png' is in the same directory as your notebook")
```

But the actual image path used is:

```python
"/content/sample_data/Girl.jpg"
```

So that message is slightly inconsistent in the original notebook. The code still follows your uploaded file structure.

---

# Easy viva points to remember

**Why convert BGR to RGB?**
Because OpenCV reads images in BGR format, but Matplotlib displays correctly in RGB.

**Why convert to grayscale?**
Because histogram equalization is commonly applied to a single intensity channel.

**Why use YUV?**
Because YUV separates brightness from color, so equalizing only brightness improves contrast while preserving colors.

**What does `ravel()` do?**
It converts the image array into a 1D array for histogram plotting.

**What is histogram equalization?**
It is a method to improve contrast by redistributing pixel intensity values.

