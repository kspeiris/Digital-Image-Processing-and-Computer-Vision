

---

# Full Complete Code with Explain Comments

```python
# ============================================================
# DIGITAL IMAGE PROCESSING & COMPUTER VISION PRACTICAL
# Lab Topic: Filters and Morphological Operations
# ============================================================

# -----------------------------
# 1. Import required libraries
# -----------------------------

import cv2                      # OpenCV library for image processing
import numpy as np              # NumPy for matrix and array operations
import matplotlib.pyplot as plt # Matplotlib for plotting images
import time                     # Used to measure execution time
from scipy.ndimage import convolve  # Used for convolution in Gaussian filter

# These two imports are mainly for Google Colab
from google.colab.patches import cv2_imshow
from google.colab import files


# ============================================================
# 2. DEFINE CUSTOM MEAN FILTER
# ============================================================

def mean_filter(image, kernel_size):
    """
    Apply a custom mean (average) filter to a grayscale image.

    Parameters:
        image (numpy array): Input grayscale image
        kernel_size (int): Size of the square kernel (must be odd)

    Returns:
        filtered_image (numpy array): Smoothed image
    """

    # If kernel size is even, make it odd
    # Example: 4 -> 5
    if kernel_size % 2 == 0:
        kernel_size += 1

    # Padding size depends on kernel size
    # Example: kernel size 3 -> pad 1
    #          kernel size 5 -> pad 2
    pad_size = kernel_size // 2

    # Add zero padding around the image borders
    padded_image = cv2.copyMakeBorder(
        image,
        pad_size, pad_size, pad_size, pad_size,
        cv2.BORDER_CONSTANT,
        value=0
    )

    # Create an empty output image
    # float32 is used first for safer average calculations
    filtered_image = np.zeros_like(image, dtype=np.float32)

    # Traverse every pixel in the original image
    for y in range(image.shape[0]):
        for x in range(image.shape[1]):

            # Extract the local window around the current pixel
            window = padded_image[y:y+kernel_size, x:x+kernel_size]

            # Replace the pixel value with the average of the window
            filtered_image[y, x] = np.mean(window)

    # Clip values to valid image range [0,255] and convert back
    filtered_image = np.clip(filtered_image, 0, 255).astype(image.dtype)

    return filtered_image


# ============================================================
# 3. DEFINE GAUSSIAN KERNEL FUNCTION
# ============================================================

def gaussian_kernel(size, sigma=None):
    """
    Create a 2D Gaussian kernel.

    Parameters:
        size (int): Kernel size
        sigma (float): Standard deviation of Gaussian distribution

    Returns:
        kernel (numpy array): Normalized Gaussian kernel
    """

    # If sigma is not given, estimate it from the kernel size
    if sigma is None:
        sigma = size / 6

    # Make sure kernel size is odd
    if size % 2 == 0:
        size += 1

    # Create coordinate values centered around zero
    ax = np.arange(-size // 2 + 1., size // 2 + 1.)

    # Create meshgrid for x and y coordinates
    xx, yy = np.meshgrid(ax, ax)

    # Apply Gaussian formula
    kernel = np.exp(-(xx**2 + yy**2) / (2.0 * sigma**2))

    # Normalize kernel so sum becomes 1
    kernel = kernel / np.sum(kernel)

    return kernel


# ============================================================
# 4. DEFINE SOBEL FILTER
# ============================================================

def sobel_filter(image):
    """
    Apply Sobel edge detection to a grayscale image.

    Parameters:
        image (numpy array): Input grayscale image

    Returns:
        normalized_gradient (numpy array): Displayable edge image
        gradient_magnitude (numpy array): Raw gradient strength
        gradient_direction (numpy array): Edge direction in degrees
        sobel_x (numpy array): Horizontal gradient
        sobel_y (numpy array): Vertical gradient
    """

    # Sobel kernel for horizontal changes
    sobel_x_kernel = np.array([
        [-1, 0, 1],
        [-2, 0, 2],
        [-1, 0, 1]
    ], dtype=np.float32)

    # Sobel kernel for vertical changes
    sobel_y_kernel = np.array([
        [-1, -2, -1],
        [ 0,  0,  0],
        [ 1,  2,  1]
    ], dtype=np.float32)

    # Apply Sobel kernels using OpenCV filter2D
    sobel_x = cv2.filter2D(image.astype(np.float32), cv2.CV_32F, sobel_x_kernel)
    sobel_y = cv2.filter2D(image.astype(np.float32), cv2.CV_32F, sobel_y_kernel)

    # Calculate gradient magnitude
    gradient_magnitude = np.sqrt(sobel_x**2 + sobel_y**2)

    # Normalize magnitude to 0-255 for display
    normalized_gradient = cv2.normalize(
        gradient_magnitude, None, 0, 255, cv2.NORM_MINMAX, cv2.CV_8U
    )

    # Calculate edge direction in degrees
    gradient_direction = np.arctan2(sobel_y, sobel_x) * (180 / np.pi)

    return normalized_gradient, gradient_magnitude, gradient_direction, sobel_x, sobel_y


# ============================================================
# 5. DEFINE MANUAL CONVOLUTION
# ============================================================

def manual_convolution(image, kernel):
    """
    Apply convolution manually.

    Parameters:
        image (numpy array): Input grayscale image
        kernel (numpy array): Filter kernel

    Returns:
        output (numpy array): Filtered image
    """

    kernel_size = kernel.shape[0]
    pad_size = kernel_size // 2

    # Add zero padding to the image
    padded = np.pad(image, pad_size, mode='constant', constant_values=0)

    # Output image initialized to zeros
    output = np.zeros_like(image, dtype=np.float32)

    # Traverse every pixel
    for y in range(image.shape[0]):
        for x in range(image.shape[1]):

            # Extract local window
            window = padded[y:y+kernel_size, x:x+kernel_size]

            # Multiply window and kernel, then sum
            output[y, x] = np.sum(window * kernel)

    return output


# ============================================================
# 6. DEFINE MANUAL SOBEL FILTER
# ============================================================

def sobel_manual(image):
    """
    Manual Sobel edge detection using custom convolution.
    """

    sobel_x_kernel = np.array([
        [-1, 0, 1],
        [-2, 0, 2],
        [-1, 0, 1]
    ], dtype=np.float32)

    sobel_y_kernel = np.array([
        [-1, -2, -1],
        [ 0,  0,  0],
        [ 1,  2,  1]
    ], dtype=np.float32)

    # Apply manual convolution
    sobel_x = manual_convolution(image.astype(np.float32), sobel_x_kernel)
    sobel_y = manual_convolution(image.astype(np.float32), sobel_y_kernel)

    # Calculate edge strength
    gradient_magnitude = np.sqrt(sobel_x**2 + sobel_y**2)

    # Normalize for display
    grad_magnitude_normalized = cv2.normalize(
        gradient_magnitude, None, 0, 255, cv2.NORM_MINMAX, cv2.CV_8U
    )

    return grad_magnitude_normalized, sobel_x, sobel_y


# ============================================================
# 7. DEFINE CUSTOM MEDIAN FILTER
# ============================================================

def custom_median_filter(image, kernel_size):
    """
    Apply median filter manually.
    Useful for removing salt-and-pepper noise.
    """

    height, width = image.shape
    filtered_image = np.zeros_like(image)
    kernel_half = kernel_size // 2

    # Traverse each pixel
    for i in range(height):
        for j in range(width):

            # Store neighboring values in a list
            neighborhood = []

            # Traverse local neighborhood
            for ki in range(-kernel_half, kernel_half + 1):
                for kj in range(-kernel_half, kernel_half + 1):
                    ni = i + ki
                    nj = j + kj

                    # Check valid image boundaries
                    if 0 <= ni < height and 0 <= nj < width:
                        neighborhood.append(image[ni, nj])

            # Replace pixel with median value
            filtered_image[i, j] = np.median(neighborhood)

    return filtered_image


# ============================================================
# 8. DEFINE OPTIMIZED MEDIAN FILTER
# ============================================================

def optimized_median_filter(image, kernel_size):
    """
    Faster median filter using reflected padding.
    """

    height, width = image.shape
    filtered_image = np.zeros_like(image)
    kernel_half = kernel_size // 2

    # Reflect padding helps preserve edge continuity
    padded = np.pad(image, kernel_half, mode='reflect')

    for i in range(height):
        for j in range(width):
            # Extract local window directly from padded image
            window = padded[i:i+kernel_size, j:j+kernel_size]

            # Median of the window
            filtered_image[i, j] = np.median(window)

    return filtered_image


# ============================================================
# 9. ADD NOISE TO IMAGE
# ============================================================

def add_noise_to_image(image, noise_type='salt_and_pepper', noise_level=0.05):
    """
    Add artificial noise to an image.

    Parameters:
        image (numpy array): Input grayscale image
        noise_type (str): 'salt_and_pepper', 'gaussian', or 'speckle'
        noise_level (float): Amount of noise

    Returns:
        noisy_image (numpy array): Noisy image
    """

    noisy_image = image.copy().astype(np.float32)
    height, width = image.shape

    if noise_type == 'salt_and_pepper':
        # Salt: white pixels
        salt = np.random.rand(height, width) < noise_level / 2

        # Pepper: black pixels
        pepper = np.random.rand(height, width) < noise_level / 2

        noisy_image[salt] = 255
        noisy_image[pepper] = 0

    elif noise_type == 'gaussian':
        # Add Gaussian-distributed random noise
        noise = np.random.normal(0, noise_level * 255, (height, width))
        noisy_image = image + noise
        noisy_image = np.clip(noisy_image, 0, 255)

    elif noise_type == 'speckle':
        # Multiplicative noise
        noise = np.random.randn(height, width) * noise_level
        noisy_image = image * (1 + noise)
        noisy_image = np.clip(noisy_image, 0, 255)

    return noisy_image.astype(np.uint8)


# ============================================================
# 10. DEFINE PSNR FUNCTION
# ============================================================

def calculate_psnr(original, filtered):
    """
    Calculate Peak Signal-to-Noise Ratio (PSNR).
    Higher PSNR means better quality.
    """

    mse = np.mean((original.astype(np.float32) - filtered.astype(np.float32)) ** 2)

    if mse == 0:
        return float('inf')

    max_pixel = 255.0
    psnr = 20 * np.log10(max_pixel / np.sqrt(mse))
    return psnr


# ============================================================
# 11. COMPARE FILTERS
# ============================================================

def compare_filters(noisy_image, kernel_size):
    """
    Compare median, Gaussian, and mean filters with timing.
    """

    # Custom median filter timing
    start_time = time.time()
    custom_result = custom_median_filter(noisy_image, kernel_size)
    custom_time = time.time() - start_time

    # Optimized median filter timing
    start_time = time.time()
    optimized_result = optimized_median_filter(noisy_image, kernel_size)
    optimized_time = time.time() - start_time

    # OpenCV median filter timing
    start_time = time.time()
    opencv_result = cv2.medianBlur(noisy_image, kernel_size)
    opencv_time = time.time() - start_time

    # Gaussian blur timing
    start_time = time.time()
    gaussian_result = cv2.GaussianBlur(noisy_image, (kernel_size, kernel_size), 0)
    gaussian_time = time.time() - start_time

    # Mean blur timing
    start_time = time.time()
    mean_result = cv2.blur(noisy_image, (kernel_size, kernel_size))
    mean_time = time.time() - start_time

    return {
        'custom': (custom_result, custom_time),
        'optimized': (optimized_result, optimized_time),
        'opencv': (opencv_result, opencv_time),
        'gaussian': (gaussian_result, gaussian_time),
        'mean': (mean_result, mean_time)
    }


# ============================================================
# 12. UPLOAD IMAGE
# ============================================================

print("Upload an image file (for example: lena.jpg or Girl.jpg)")
uploaded = files.upload()

# Get uploaded filename automatically
filename = list(uploaded.keys())[0]

# Read image in grayscale
image = cv2.imread(filename, cv2.IMREAD_GRAYSCALE)

# If reading fails, try color and convert to grayscale
if image is None:
    image_color = cv2.imread(filename)
    if image_color is not None:
        image = cv2.cvtColor(image_color, cv2.COLOR_BGR2GRAY)
    else:
        print("Error: Could not load image.")
        raise ValueError("Image loading failed")

# Print image details
print("Image loaded successfully")
print("Image shape:", image.shape)
print("Image dtype:", image.dtype)
print("Pixel range:", image.min(), "to", image.max())

# Show original image
print("Original Image:")
cv2_imshow(image)


# ============================================================
# 13. APPLY MEAN FILTER
# ============================================================

kernel_sizes = [3, 5, 7, 9]

for kernel_size in kernel_sizes:
    print(f"\nApplying Mean Filter with kernel size {kernel_size}x{kernel_size}")

    filtered_image = mean_filter(image, kernel_size)

    print("Filtered Image:")
    cv2_imshow(filtered_image)

    # Difference image shows how much the original changed
    difference = cv2.absdiff(image.astype(np.float32), filtered_image.astype(np.float32))

    # Avoid division by zero if no difference exists
    if difference.max() != 0:
        difference_normalized = (difference / difference.max() * 255).astype(np.uint8)
    else:
        difference_normalized = difference.astype(np.uint8)

    print("Difference Map:")
    cv2_imshow(difference_normalized)

    # Print statistics
    print(f"Original Mean Value: {np.mean(image):.2f}")
    print(f"Filtered Mean Value: {np.mean(filtered_image):.2f}")
    print(f"Maximum Pixel Change: {difference.max():.2f}")
    print(f"Average Pixel Change: {np.mean(difference):.2f}")


# ============================================================
# 14. DISPLAY GAUSSIAN KERNELS
# ============================================================

gaussian_kernel_sizes = [3, 5, 7, 9, 11]

fig_kernels, axes_kernels = plt.subplots(1, len(gaussian_kernel_sizes), figsize=(18, 4))

for i, size in enumerate(gaussian_kernel_sizes):
    kernel = gaussian_kernel(size)

    # Show the kernel as a heat map
    axes_kernels[i].imshow(kernel, cmap='hot')
    axes_kernels[i].set_title(f'Size {size}x{size}\nSigma={size/6:.2f}')
    axes_kernels[i].axis('off')

plt.tight_layout()
plt.show()


# ============================================================
# 15. APPLY GAUSSIAN FILTER
# ============================================================

fig, axes = plt.subplots(2, 3, figsize=(15, 10))

# Display original image first
axes[0, 0].imshow(image, cmap='gray')
axes[0, 0].set_title("Original Image")
axes[0, 0].axis('off')

for i, kernel_size in enumerate(gaussian_kernel_sizes[:5]):
    kernel = gaussian_kernel(kernel_size)

    # Apply convolution using Gaussian kernel
    filtered_image = convolve(image, kernel)

    # Compute subplot location
    row = (i + 1) // 3
    col = (i + 1) % 3

    axes[row, col].imshow(filtered_image, cmap='gray')
    axes[row, col].set_title(f'Gaussian {kernel_size}x{kernel_size}')
    axes[row, col].axis('off')

    print(f"\nGaussian Filter Output with kernel size {kernel_size}x{kernel_size}")
    cv2_imshow(filtered_image.astype(np.uint8))

plt.tight_layout()
plt.show()


# ============================================================
# 16. COMPARE CUSTOM GAUSSIAN WITH OPENCV GAUSSIANBLUR
# ============================================================

test_kernel_size = 7
sigma = test_kernel_size / 6

your_kernel = gaussian_kernel(test_kernel_size, sigma)
your_filtered = convolve(image, your_kernel)

opencv_filtered = cv2.GaussianBlur(image, (test_kernel_size, test_kernel_size), sigma)

difference = np.abs(your_filtered.astype(np.float32) - opencv_filtered.astype(np.float32))

print("\nComparison: Custom Gaussian vs OpenCV GaussianBlur")
print(f"Maximum Difference: {difference.max():.6f}")
print(f"Mean Difference: {difference.mean():.6f}")


# ============================================================
# 17. APPLY SOBEL FILTER USING OPENCV
# ============================================================

sobel_display, sobel_mag, sobel_dir, sobel_x, sobel_y = sobel_filter(image)

print("\nSobel X (Horizontal Gradient):")
cv2_imshow(cv2.convertScaleAbs(sobel_x))

print("Sobel Y (Vertical Gradient):")
cv2_imshow(cv2.convertScaleAbs(sobel_y))

print("Sobel Gradient Magnitude:")
cv2_imshow(sobel_display)


# ============================================================
# 18. APPLY MANUAL SOBEL FILTER
# ============================================================

manual_sobel_display, manual_sobel_x, manual_sobel_y = sobel_manual(image)

print("\nManual Sobel X:")
cv2_imshow(cv2.convertScaleAbs(manual_sobel_x))

print("Manual Sobel Y:")
cv2_imshow(cv2.convertScaleAbs(manual_sobel_y))

print("Manual Sobel Gradient Magnitude:")
cv2_imshow(manual_sobel_display)


# ============================================================
# 19. ADD NOISE TO IMAGE
# ============================================================

noisy_image = add_noise_to_image(image, noise_type='salt_and_pepper', noise_level=0.08)

print("\nNoisy Image (Salt and Pepper Noise):")
cv2_imshow(noisy_image)


# ============================================================
# 20. APPLY MEDIAN FILTERS AND COMPARE
# ============================================================

kernel_size = 3
comparison_results = compare_filters(noisy_image, kernel_size)

print("\nCustom Median Filter Result:")
cv2_imshow(comparison_results['custom'][0])

print("Optimized Median Filter Result:")
cv2_imshow(comparison_results['optimized'][0])

print("OpenCV Median Blur Result:")
cv2_imshow(comparison_results['opencv'][0])

print("Gaussian Blur Result:")
cv2_imshow(comparison_results['gaussian'][0])

print("Mean Filter Result:")
cv2_imshow(comparison_results['mean'][0])


# ============================================================
# 21. PRINT PSNR VALUES
# ============================================================

print("\nPSNR Comparison with Original Image:")
print(f"Custom Median Filter PSNR:    {calculate_psnr(image, comparison_results['custom'][0]):.2f} dB")
print(f"Optimized Median Filter PSNR: {calculate_psnr(image, comparison_results['optimized'][0]):.2f} dB")
print(f"OpenCV Median Blur PSNR:      {calculate_psnr(image, comparison_results['opencv'][0]):.2f} dB")
print(f"Gaussian Blur PSNR:           {calculate_psnr(image, comparison_results['gaussian'][0]):.2f} dB")
print(f"Mean Filter PSNR:             {calculate_psnr(image, comparison_results['mean'][0]):.2f} dB")


# ============================================================
# 22. PRINT EXECUTION TIMES
# ============================================================

print("\nExecution Time Comparison:")
print(f"Custom Median Filter:    {comparison_results['custom'][1]:.4f} seconds")
print(f"Optimized Median Filter: {comparison_results['optimized'][1]:.4f} seconds")
print(f"OpenCV Median Blur:      {comparison_results['opencv'][1]:.4f} seconds")
print(f"Gaussian Blur:           {comparison_results['gaussian'][1]:.4f} seconds")
print(f"Mean Filter:             {comparison_results['mean'][1]:.4f} seconds")


# ============================================================
# 23. CONVERT IMAGE TO BINARY USING OTSU THRESHOLDING
# ============================================================

ret, img_binary = cv2.threshold(
    image, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU
)

print(f"\nOtsu's Calculated Threshold Value: {ret}")

print("Binary Image:")
cv2_imshow(img_binary)


# ============================================================
# 24. APPLY EROSION AND DILATION
# ============================================================

kernel = np.ones((3, 3), np.uint8)

# Erosion shrinks white regions
eroded_image = cv2.erode(img_binary, kernel, iterations=1)

# Dilation expands white regions
dilated_image = cv2.dilate(img_binary, kernel, iterations=1)

print("\nEroded Image:")
cv2_imshow(eroded_image)

print("Dilated Image:")
cv2_imshow(dilated_image)


# ============================================================
# 25. CREATE SYNTHETIC IMAGE FOR MORPHOLOGY DEMO
# ============================================================

synthetic = np.zeros((200, 200), dtype=np.uint8)

# Main white object
cv2.ellipse(synthetic, (100, 100), (60, 40), 0, 0, 360, 255, -1)

# Small white noise objects
cv2.circle(synthetic, (30, 30), 3, 255, -1)
cv2.circle(synthetic, (170, 30), 3, 255, -1)
cv2.circle(synthetic, (30, 170), 3, 255, -1)
cv2.circle(synthetic, (170, 170), 3, 255, -1)

# Small black holes inside object
cv2.circle(synthetic, (100, 100), 5, 0, -1)
cv2.circle(synthetic, (80, 80), 3, 0, -1)
cv2.circle(synthetic, (120, 120), 4, 0, -1)

# Thin protrusions
cv2.line(synthetic, (140, 60), (180, 40), 255, 2)
cv2.line(synthetic, (60, 140), (20, 160), 255, 2)

# Additional attached objects
cv2.circle(synthetic, (140, 100), 8, 255, -1)
cv2.circle(synthetic, (60, 100), 6, 255, -1)

print("\nSynthetic Image for Morphology:")
cv2_imshow(synthetic)


# ============================================================
# 26. DEFINE MORPHOLOGY KERNELS
# ============================================================

kernel_small = np.ones((3, 3), np.uint8)
kernel_medium = np.ones((5, 5), np.uint8)
kernel_large = np.ones((7, 7), np.uint8)


# ============================================================
# 27. APPLY OPENING AND CLOSING
# ============================================================

# Opening = erosion followed by dilation
opening_small = cv2.morphologyEx(synthetic, cv2.MORPH_OPEN, kernel_small)
opening_medium = cv2.morphologyEx(synthetic, cv2.MORPH_OPEN, kernel_medium)
opening_large = cv2.morphologyEx(synthetic, cv2.MORPH_OPEN, kernel_large)

# Closing = dilation followed by erosion
closing_small = cv2.morphologyEx(synthetic, cv2.MORPH_CLOSE, kernel_small)
closing_medium = cv2.morphologyEx(synthetic, cv2.MORPH_CLOSE, kernel_medium)
closing_large = cv2.morphologyEx(synthetic, cv2.MORPH_CLOSE, kernel_large)

print("\nOpening with 3x3 kernel:")
cv2_imshow(opening_small)

print("Opening with 5x5 kernel:")
cv2_imshow(opening_medium)

print("Opening with 7x7 kernel:")
cv2_imshow(opening_large)

print("Closing with 3x3 kernel:")
cv2_imshow(closing_small)

print("Closing with 5x5 kernel:")
cv2_imshow(closing_medium)

print("Closing with 7x7 kernel:")
cv2_imshow(closing_large)


# ============================================================
# 28. APPLY EXTRA MORPHOLOGICAL OPERATIONS
# ============================================================

# Morphological gradient highlights boundaries
gradient = cv2.morphologyEx(synthetic, cv2.MORPH_GRADIENT, kernel_medium)

# Top hat extracts small bright details
tophat = cv2.morphologyEx(synthetic, cv2.MORPH_TOPHAT, kernel_medium)

# Black hat extracts small dark details
blackhat = cv2.morphologyEx(synthetic, cv2.MORPH_BLACKHAT, kernel_medium)

print("\nMorphological Gradient:")
cv2_imshow(gradient)

print("Top Hat:")
cv2_imshow(tophat)

print("Black Hat:")
cv2_imshow(blackhat)


# ============================================================
# 29. FINAL SUMMARY
# ============================================================

print("\n===== PRACTICAL SUMMARY =====")
print("1. Mean Filter: Smooths image using average of neighbors.")
print("2. Gaussian Filter: Smooths image using weighted average.")
print("3. Sobel Filter: Detects edges using intensity gradients.")
print("4. Median Filter: Removes salt-and-pepper noise effectively.")
print("5. Otsu Thresholding: Converts grayscale image to binary automatically.")
print("6. Erosion: Shrinks white objects.")
print("7. Dilation: Expands white objects.")
print("8. Opening: Removes small white noise.")
print("9. Closing: Fills small black holes and gaps.")
print("10. Morphological Gradient / Top Hat / Black Hat: Useful for shape analysis.")
```

---

### Important note for your exam

This code is written for **Google Colab** because it uses:

```python
from google.colab.patches import cv2_imshow
from google.colab import files
```

If your exam uses **Jupyter Notebook** or **VS Code**, replace:

```python
cv2_imshow(image)
```

with:

```python
plt.imshow(image, cmap='gray')
plt.axis('off')
plt.show()
```


