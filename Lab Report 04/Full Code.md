
```python
# ============================================================
# LAB 04 - Digital Image Processing and Computer Vision
# Topic:
#   1. Image smoothing using a 3x3 averaging filter
#   2. Manual filtering
#   3. OpenCV filtering using cv2.filter2D() and cv2.blur()
#   4. Adding noise and reducing noise
#   5. Comparing different padding methods in convolution
# ============================================================


# ============================================================
# 1. IMPORT REQUIRED LIBRARIES
# ============================================================

import cv2                      # OpenCV library for image processing
import numpy as np              # NumPy for matrix/array operations
import matplotlib.pyplot as plt # Matplotlib for displaying images
import os                       # os module for checking file existence

# This line is useful in Jupyter Notebook / Google Colab
# It shows plots directly below the code cell
%matplotlib inline

# Set default figure size for plots
plt.rcParams['figure.figsize'] = [10, 8]


# ============================================================
# 2. LOAD IMAGE WITH ERROR HANDLING
# ============================================================

# Path of the image file
image_path = 'Girl.jpg'

# Check whether the file exists in the current folder
if not os.path.exists(image_path):
    print(f"Error: {image_path} not found!")
    print("Please place 'Girl.jpg' in the current working directory before running this section.")
else:
    # Read the image using OpenCV
    # OpenCV loads color images in BGR format
    img = cv2.imread(image_path)

    # Check whether the image was loaded successfully
    if img is None:
        print(f"Error: Could not load image from {image_path}")
    else:
        # Convert the image from BGR to RGB for correct display in matplotlib
        img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

        # Convert the image to grayscale
        gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

        # Print basic information
        print("Image loaded successfully!")
        print(f"Original size: {img.shape[1]} x {img.shape[0]}")
        print(f"Grayscale image shape: {gray.shape}")


# ============================================================
# 3. MANUAL 3x3 AVERAGING FILTER
# ============================================================

def average_filter_manual(image):
    """
    Apply a manual 3x3 averaging filter to a grayscale image.

    Parameters:
        image : input grayscale image

    Returns:
        output : smoothed grayscale image
    """

    # Get height and width of the image
    h, w = image.shape

    # Create an empty output image with the same shape as input
    output = np.zeros_like(image)

    # Pad the image by 1 pixel on all sides
    # mode='edge' means edge pixels are repeated outward
    padded = np.pad(image, pad_width=1, mode='edge')

    # Loop through each pixel of the original image
    for y in range(h):
        for x in range(w):
            # Extract the 3x3 neighborhood around the current pixel
            neighborhood = padded[y:y+3, x:x+3]

            # Compute the average value of the 3x3 area
            output[y, x] = np.mean(neighborhood)

    # Convert output to unsigned 8-bit integer format
    return output.astype(np.uint8)


# Apply the manual average filter only if image is loaded
if 'gray' in locals():
    smoothed_manual = average_filter_manual(gray)
    print("Manual 3x3 averaging filter applied successfully!")


# ============================================================
# 4. APPLY AVERAGING FILTER USING cv2.filter2D()
# ============================================================

if 'gray' in locals():
    # Create a 3x3 averaging kernel
    # All values are 1/9 so the sum of the kernel becomes 1
    kernel_3x3 = np.ones((3, 3), np.float32) / 9.0

    # Apply convolution using cv2.filter2D()
    # -1 means output image depth is same as input image
    smoothed_filter2d = cv2.filter2D(gray, -1, kernel_3x3)

    print("cv2.filter2D() with 3x3 averaging kernel applied!")
    print("Kernel used:")
    print(kernel_3x3)


# ============================================================
# 5. APPLY FAST SMOOTHING USING cv2.blur()
# ============================================================

if 'gray' in locals():
    # cv2.blur() is a fast built-in averaging filter
    smoothed_fast = cv2.blur(gray, (3, 3))

    print("Fast smoothing using cv2.blur() applied successfully!")


# ============================================================
# 6. ADD NOISE TO IMAGE AND SMOOTH IT
# ============================================================

if 'gray' in locals():
    # Create random noise in the range [-30, 30]
    # int16 is used to avoid overflow/underflow during addition
    noise = np.random.randint(-30, 31, gray.shape, dtype=np.int16)

    # Add noise to the grayscale image
    # Convert grayscale image to int16 first for safe addition
    # Clip values to the valid range [0, 255]
    noisy = np.clip(gray.astype(np.int16) + noise, 0, 255).astype(np.uint8)

    # Smooth the noisy image using averaging filter
    noisy_smoothed = cv2.blur(noisy, (3, 3))

    print("Noise added and noisy image smoothed successfully!")


# ============================================================
# 7. DISPLAY ALL MAIN RESULTS
# ============================================================

if 'gray' in locals():
    # Create a figure with 2 rows and 4 columns
    fig, axes = plt.subplots(2, 4, figsize=(20, 10))

    # --------------------------------------------------------
    # Row 1
    # --------------------------------------------------------

    # Original grayscale image
    axes[0, 0].imshow(gray, cmap='gray')
    axes[0, 0].set_title('Original Grayscale')
    axes[0, 0].axis('off')

    # Smoothed grayscale image using average filter
    axes[0, 1].imshow(smoothed_fast, cmap='gray')
    axes[0, 1].set_title('Smoothed (3x3 Averaging)')
    axes[0, 1].axis('off')

    # Noisy image
    axes[0, 2].imshow(noisy, cmap='gray')
    axes[0, 2].set_title('Original + Noise')
    axes[0, 2].axis('off')

    # Noisy image after smoothing
    axes[0, 3].imshow(noisy_smoothed, cmap='gray')
    axes[0, 3].set_title('Noisy -> Smoothed')
    axes[0, 3].axis('off')

    # --------------------------------------------------------
    # Row 2
    # --------------------------------------------------------

    # Show the 3x3 kernel visually
    kernel_display = axes[1, 0].imshow(kernel_3x3, cmap='jet', interpolation='nearest')
    axes[1, 0].set_title('3x3 Averaging Kernel')
    plt.colorbar(kernel_display, ax=axes[1, 0], fraction=0.046, pad=0.04)

    # Difference between original and smoothed image
    # absdiff gives absolute pixel-wise difference
    diff = cv2.absdiff(gray, smoothed_fast)
    diff_display = axes[1, 1].imshow(diff, cmap='hot', vmin=0, vmax=50)
    axes[1, 1].set_title('Difference (Original - Smoothed)')
    axes[1, 1].axis('off')
    plt.colorbar(diff_display, ax=axes[1, 1], fraction=0.046, pad=0.04)

    # Original color image
    axes[1, 2].imshow(img_rgb)
    axes[1, 2].set_title('Original Color Image')
    axes[1, 2].axis('off')

    # Smoothed color image
    smoothed_color = cv2.blur(img_rgb, (3, 3))
    axes[1, 3].imshow(smoothed_color)
    axes[1, 3].set_title('Color Image Smoothed')
    axes[1, 3].axis('off')

    # Main title
    plt.suptitle('Image Smoothing using 3x3 Averaging (Box) Filter', fontsize=20, y=1.02)

    # Adjust spacing and display
    plt.tight_layout()
    plt.show()

    print("3x3 averaging filter applied successfully!")
    print("Output shapes:")
    print(f"Manual method: {smoothed_manual.shape}")
    print(f"cv2.filter2D(): {smoothed_filter2d.shape}")
    print(f"cv2.blur(): {smoothed_fast.shape}")


# ============================================================
# 8. COMPARE THE FILTERING METHODS
# ============================================================

if 'gray' in locals():
    # Compare manual filter vs cv2.filter2D()
    diff_methods = cv2.absdiff(smoothed_manual, smoothed_filter2d)
    print("Difference between manual method and cv2.filter2D():")
    print(f"Max difference: {diff_methods.max()}")
    print(f"Mean difference: {diff_methods.mean():.6f}")

    print()

    # Compare cv2.filter2D() vs cv2.blur()
    diff_methods2 = cv2.absdiff(smoothed_filter2d, smoothed_fast)
    print("Difference between cv2.filter2D() and cv2.blur():")
    print(f"Max difference: {diff_methods2.max()}")
    print(f"Mean difference: {diff_methods2.mean():.6f}")

    # --------------------------------------------------------
    # Show a small patch from the image for detailed comparison
    # --------------------------------------------------------

    fig, axes = plt.subplots(1, 4, figsize=(15, 4))

    # Top-left point of patch
    patch_y, patch_x = 100, 100

    # Patch size
    patch_size = 50

    # Extract 50x50 patches from all images
    patch_original  = gray[patch_y:patch_y+patch_size, patch_x:patch_x+patch_size]
    patch_manual    = smoothed_manual[patch_y:patch_y+patch_size, patch_x:patch_x+patch_size]
    patch_filter2d  = smoothed_filter2d[patch_y:patch_y+patch_size, patch_x:patch_x+patch_size]
    patch_blur      = smoothed_fast[patch_y:patch_y+patch_size, patch_x:patch_x+patch_size]

    # Display original patch
    axes[0].imshow(patch_original, cmap='gray')
    axes[0].set_title('Original Patch')
    axes[0].axis('off')

    # Display manual filter patch
    axes[1].imshow(patch_manual, cmap='gray')
    axes[1].set_title('Manual Filter')
    axes[1].axis('off')

    # Display cv2.filter2D patch
    axes[2].imshow(patch_filter2d, cmap='gray')
    axes[2].set_title('cv2.filter2D()')
    axes[2].axis('off')

    # Display cv2.blur patch
    axes[3].imshow(patch_blur, cmap='gray')
    axes[3].set_title('cv2.blur()')
    axes[3].axis('off')

    plt.suptitle('Comparison of 50x50 Image Patches', fontsize=14)
    plt.tight_layout()
    plt.show()


# ============================================================
# 9. CREATE TEST IMAGES FOR PADDING EXPERIMENT
# ============================================================

def create_checkerboard(size=8, num_tiles=8):
    """
    Create a checkerboard image for studying edge effects.

    size      : scaling factor for final image size
    num_tiles : number of black/white tile repetitions
    """

    # Create alternating rows
    re = np.r_[num_tiles * [0, 1]]   # row pattern: 0 1 0 1 ...
    ro = np.r_[num_tiles * [1, 0]]   # row pattern: 1 0 1 0 ...

    # Stack rows vertically
    board = np.vstack(num_tiles * (re, ro))

    # Convert 0/1 to 0/255 and resize without smoothing
    board = cv2.resize(
        board.astype("uint8") * 255,
        (size * 20, size * 20),
        interpolation=cv2.INTER_NEAREST
    )

    return board


# Create checkerboard image
checkerboard = create_checkerboard()
cv2.imwrite("checkerboard.png", checkerboard)

# Create simple rectangle image
rectangle = np.ones((300, 300), dtype=np.uint8) * 255   # white image
rectangle[50:250, 80:220] = 0                           # black rectangle
cv2.imwrite("rectangle.png", rectangle)

print("Test images created successfully!")
print(f"Checkerboard shape: {checkerboard.shape}")
print(f"Rectangle shape: {rectangle.shape}")


# ============================================================
# 10. LOAD TEST IMAGE FOR PADDING EXPERIMENT
# ============================================================

# Choose one image
# gray2 = cv2.imread('checkerboard.png', cv2.IMREAD_GRAYSCALE)
gray2 = cv2.imread('rectangle.png', cv2.IMREAD_GRAYSCALE)

# If file loading fails, use generated checkerboard
if gray2 is None:
    print("Error: Could not load image file. Using generated checkerboard instead.")
    gray2 = create_checkerboard()

# Define the same 3x3 averaging kernel
kernel = np.ones((3, 3), np.float32) / 9

# Padding size for 3x3 kernel = 1 pixel
pad = 1

print("Padding experiment image loaded successfully!")
print(f"Image shape: {gray2.shape}")
print("Kernel:")
print(kernel)


# ============================================================
# 11. DEFINE DIFFERENT PADDING MODES
# ============================================================

padding_modes = [
    ("BORDER_CONSTANT (Zero)",    cv2.BORDER_CONSTANT,    0),
    ("BORDER_REPLICATE (Edge)",   cv2.BORDER_REPLICATE,   None),
    ("BORDER_REFLECT",            cv2.BORDER_REFLECT,     None),
    ("BORDER_REFLECT_101",        cv2.BORDER_REFLECT_101, None),
    ("BORDER_WRAP",               cv2.BORDER_WRAP,        None),
    ("No Padding (Valid)",        None,                   None),
]

print("Padding modes to compare:")
for i, (name, border_type, value) in enumerate(padding_modes, 1):
    print(f"{i}. {name}")


# ============================================================
# 12. APPLY CONVOLUTION USING DIFFERENT PADDING METHODS
# ============================================================

results = []

for name, border_type, const_value in padding_modes:

    # --------------------------------------------------------
    # Case 1: No padding (Valid convolution)
    # --------------------------------------------------------
    if border_type is None:
        # Apply filtering with constant border first
        full = cv2.filter2D(gray2, -1, kernel, borderType=cv2.BORDER_CONSTANT)

        # Crop outer border to simulate valid convolution
        cropped = full[pad:-pad, pad:-pad]

        results.append((name, cropped))
        print(f"{name}: output shape = {cropped.shape}")

    # --------------------------------------------------------
    # Case 2: Zero / constant padding
    # --------------------------------------------------------
    elif border_type == cv2.BORDER_CONSTANT:
        # Manually add a 1-pixel constant border around the image
        padded = cv2.copyMakeBorder(
            gray2, pad, pad, pad, pad,
            borderType=cv2.BORDER_CONSTANT,
            value=const_value
        )

        # Apply filter to the padded image
        filtered_full = cv2.filter2D(
            padded, -1, kernel,
            borderType=cv2.BORDER_CONSTANT
        )

        # Remove the extra padding to restore original size
        filtered = filtered_full[pad:-pad, pad:-pad]

        results.append((name, filtered))
        print(f"{name}: output shape = {filtered.shape}")

    # --------------------------------------------------------
    # Case 3: Wrap padding
    # --------------------------------------------------------
    elif border_type == cv2.BORDER_WRAP:
        # Add wrap border manually
        padded = cv2.copyMakeBorder(
            gray2, pad, pad, pad, pad,
            borderType=cv2.BORDER_WRAP
        )

        # Apply filter
        filtered_full = cv2.filter2D(
            padded, -1, kernel,
            borderType=cv2.BORDER_REPLICATE
        )

        # Remove the extra padding
        filtered = filtered_full[pad:-pad, pad:-pad]

        results.append((name, filtered))
        print(f"{name}: output shape = {filtered.shape}")

    # --------------------------------------------------------
    # Case 4: Other OpenCV-supported border types
    # --------------------------------------------------------
    else:
        filtered = cv2.filter2D(gray2, -1, kernel, borderType=border_type)
        results.append((name, filtered))
        print(f"{name}: output shape = {filtered.shape}")

print(f"\nAll {len(results)} padding methods processed successfully!")


# ============================================================
# 13. VISUALIZE ALL PADDING RESULTS
# ============================================================

plt.figure(figsize=(15, 12))

# Display original image in the first position
plt.subplot(3, 3, 1)
plt.imshow(gray2, cmap='gray')
plt.title('Original Image', fontsize=14, fontweight='bold')
plt.xlabel(f'{gray2.shape[1]} x {gray2.shape[0]}')
plt.axis('off')

# Display all filtered images
for i, (title, img_out) in enumerate(results, start=2):

    # If image has 3 channels, convert to grayscale
    if len(img_out.shape) == 3:
        img_out = cv2.cvtColor(img_out, cv2.COLOR_BGR2GRAY)

    plt.subplot(3, 3, i)
    plt.imshow(img_out, cmap='gray')
    plt.title(title, fontsize=12)
    plt.xlabel(f'{img_out.shape[1]} x {img_out.shape[0]}')
    plt.axis('off')

plt.suptitle('Convolution Results with Different Padding Methods',
             fontsize=16, fontweight='bold', y=1.02)
plt.tight_layout()
plt.show()


# ============================================================
# 14. DETAILED CORNER COMPARISON
# ============================================================

# Select a zoom region
zoom_y, zoom_x, size = 20, 20, 40

# Create zero-padded version
zero_padded = cv2.copyMakeBorder(
    gray2, pad, pad, pad, pad,
    borderType=cv2.BORDER_CONSTANT, value=0
)

zero_full = cv2.filter2D(
    zero_padded, -1, kernel,
    borderType=cv2.BORDER_CONSTANT
)

zero_result = zero_full[pad:-pad, pad:-pad]

# Create reflect_101 result
reflect_result = cv2.filter2D(
    gray2, -1, kernel,
    borderType=cv2.BORDER_REFLECT_101
)

# Plot comparison
fig, axes = plt.subplots(1, 3, figsize=(12, 4))

# Original zoomed region
axes[0].imshow(gray2[zoom_y:zoom_y+size, zoom_x:zoom_x+size], cmap='gray')
axes[0].set_title('Original (Zoomed Corner)', fontsize=12)
axes[0].axis('off')

# Zero padding result in zoomed region
axes[1].imshow(
    zero_result[zoom_y:zoom_y+size, zoom_x:zoom_x+size],
    cmap='gray', vmin=0, vmax=255
)
axes[1].set_title('Zero Padding', fontsize=12)
axes[1].axis('off')

# Reflect_101 result in zoomed region
axes[2].imshow(
    reflect_result[zoom_y:zoom_y+size, zoom_x:zoom_x+size],
    cmap='gray', vmin=0, vmax=255
)
axes[2].set_title('Reflect 101 Padding', fontsize=12)
axes[2].axis('off')

plt.suptitle('Detailed Corner Comparison: Border Effects',
             fontsize=14, fontweight='bold', y=1.05)
plt.tight_layout()
plt.show()


# ============================================================
# 15. PRINT SUMMARY OF PADDING METHODS
# ============================================================

print("=" * 60)
print("PADDING METHODS SUMMARY")
print("=" * 60)

print("\n1. BORDER_CONSTANT (Zero Padding):")
print("   - Adds zeros around the image")
print("   - Can create dark borders in the filtered result")

print("\n2. BORDER_REPLICATE (Edge Padding):")
print("   - Repeats the border pixels")
print("   - Helps preserve edge intensity")

print("\n3. BORDER_REFLECT / BORDER_REFLECT_101:")
print("   - Reflects the image around its borders")
print("   - Reflect_101 excludes the edge pixel itself")

print("\n4. BORDER_WRAP:")
print("   - Wraps image values from opposite side")
print("   - Useful for periodic patterns")

print("\n5. No Padding (Valid Convolution):")
print("   - Output becomes smaller")
print("   - Border pixels are removed")

print("\n" + "=" * 60)
print("Key Insight:")
print("Padding mainly affects border pixels, while inner pixels")
print("usually remain very similar across different methods.")
print("=" * 60)


# ============================================================
# 16. OPTIONAL: CREATE A SIMPLE TEST IMAGE DIRECTLY
# ============================================================

def create_test_image(size=200):
    """
    Create a synthetic grayscale test image with:
    - horizontal gradient
    - white rectangle
    - black circle
    """

    # Start with a black image
    img = np.zeros((size, size), dtype=np.uint8)

    # Create horizontal gradient from black to white
    for i in range(size):
        img[:, i] = i * 255 // size

    # Add white rectangle
    img[50:150, 50:150] = 255

    # Add black circle
    cv2.circle(img, (150, 150), 30, 0, -1)

    return img


# Generate test image
test_img = create_test_image(200)

print("Synthetic test image created successfully!")
print(f"Shape: {test_img.shape}")
print(f"Data type: {test_img.dtype}")
print(f"Min pixel value: {test_img.min()}")
print(f"Max pixel value: {test_img.max()}")

# Display test image
plt.figure(figsize=(5, 5))
plt.imshow(test_img, cmap='gray')
plt.title('Synthetic Test Image')
plt.axis('off')
plt.show()
```
