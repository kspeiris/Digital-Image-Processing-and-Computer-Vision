

---

# Complete Full Code with Explain Comments

This includes:

1. Mean Filter
2. Gaussian Filter
3. Sobel Filter
4. Median Filter

All comments are written clearly so you can study for your practical exam.

---

## 1. Mean Filter Code with Explain Comments

```python
# ------------------------------------------------------------
# MEAN FILTER / AVERAGE FILTER
# ------------------------------------------------------------

# Import OpenCV for image processing
import cv2

# Import NumPy for numerical and array operations
import numpy as np


# Define a function to apply the mean filter
def mean_filter(image, kernel_size):
    """
    Apply a mean (average) filter to a grayscale image.

    Parameters:
        image       : Input grayscale image
        kernel_size : Size of the filter window (must be odd, e.g. 3, 5, 7)

    Returns:
        filtered_image : Smoothed output image
    """

    # Add padding around the image borders
    # This helps the filter work on edge pixels too
    # kernel_size//2 means half of the kernel size
    padded_image = cv2.copyMakeBorder(
        image,
        kernel_size // 2,   # top padding
        kernel_size // 2,   # bottom padding
        kernel_size // 2,   # left padding
        kernel_size // 2,   # right padding
        cv2.BORDER_CONSTANT # fill padded area with 0
    )

    # Create an empty output image with the same size as the original image
    filtered_image = np.zeros_like(image)

    # Loop through each pixel in the original image
    for y in range(image.shape[0]):  # rows
        for x in range(image.shape[1]):  # columns

            # Extract the local neighborhood (window) around the current pixel
            window = padded_image[y:y + kernel_size, x:x + kernel_size]

            # Calculate the average value of all pixels inside the window
            mean_value = np.mean(window)

            # Assign the average value to the output image
            filtered_image[y, x] = mean_value

    # Return the smoothed image
    return filtered_image


# Load the image in grayscale mode
# Each pixel will contain one intensity value (0 to 255)
image = cv2.imread("girl.jpg", cv2.IMREAD_GRAYSCALE)

# Check whether the image is loaded correctly
if image is None:
    print("Error: Image not found.")
else:
    # Set the kernel size
    # A 5x5 kernel means each pixel is replaced using a 5x5 neighborhood
    kernel_size = 5

    # Apply the mean filter
    filtered_image = mean_filter(image, kernel_size)

    # Show the original image
    cv2.imshow("Original Image", image)

    # Show the mean filtered image
    cv2.imshow("Mean Filtered Image", filtered_image)

    # Wait until a key is pressed
    cv2.waitKey(0)

    # Close all display windows
    cv2.destroyAllWindows()
```

---

## 2. Gaussian Filter Code with Explain Comments

```python
# ------------------------------------------------------------
# GAUSSIAN FILTER
# ------------------------------------------------------------

# Import OpenCV
import cv2

# Import NumPy
import numpy as np


# Define a function to create a Gaussian kernel
def gaussian_kernel(size, sigma=1.0):
    """
    Create a 2D Gaussian kernel.

    Parameters:
        size  : Size of the kernel (must be odd, e.g. 3, 5, 7)
        sigma : Standard deviation of the Gaussian distribution

    Returns:
        kernel : Normalized Gaussian kernel
    """

    # Create a 1D array of coordinate values
    # Example for size=5 -> [-2, -1, 0, 1, 2]
    ax = np.arange(-(size // 2), size // 2 + 1)

    # Convert 1D coordinates into 2D coordinate matrices
    xx, yy = np.meshgrid(ax, ax)

    # Apply the Gaussian formula to generate kernel values
    kernel = np.exp(-(xx**2 + yy**2) / (2 * sigma**2))

    # Normalize the kernel so that all values sum to 1
    # This keeps image brightness balanced after filtering
    kernel = kernel / np.sum(kernel)

    return kernel


# Define a function to perform convolution manually
def convolve(image, kernel):
    """
    Apply convolution between an image and a given kernel.

    Parameters:
        image  : Input grayscale image
        kernel : Filter kernel

    Returns:
        output : Filtered image
    """

    # Calculate required padding size from kernel dimensions
    pad = kernel.shape[0] // 2

    # Pad the image with zeros around the border
    padded_image = cv2.copyMakeBorder(
        image, pad, pad, pad, pad, cv2.BORDER_CONSTANT
    )

    # Create an empty output image using float values
    # Float is used because convolution may produce decimal values
    output = np.zeros_like(image, dtype=np.float32)

    # Loop through all pixels of the image
    for y in range(image.shape[0]):
        for x in range(image.shape[1]):

            # Extract the local region matching the kernel size
            window = padded_image[y:y + kernel.shape[0], x:x + kernel.shape[1]]

            # Multiply the window by the kernel and sum all values
            output[y, x] = np.sum(window * kernel)

    # Convert the output image back to unsigned 8-bit integer
    return output.astype(np.uint8)


# Load the grayscale image
image = cv2.imread("girl.jpg", cv2.IMREAD_GRAYSCALE)

# Check whether the image loaded properly
if image is None:
    print("Error: Image not found.")
else:
    # Define kernel size and sigma value
    kernel_size = 5
    sigma = 1.0

    # Generate the Gaussian kernel
    g_kernel = gaussian_kernel(kernel_size, sigma)

    # Apply convolution using the Gaussian kernel
    filtered_image = convolve(image, g_kernel)

    # Display original image
    cv2.imshow("Original Image", image)

    # Display Gaussian filtered image
    cv2.imshow("Gaussian Filtered Image", filtered_image)

    # Wait until user presses a key
    cv2.waitKey(0)

    # Close all windows
    cv2.destroyAllWindows()
```

---

## 3. Sobel Filter Code with Explain Comments

```python
# ------------------------------------------------------------
# SOBEL FILTER FOR EDGE DETECTION
# ------------------------------------------------------------

# Import OpenCV
import cv2

# Import NumPy
import numpy as np


# Load the image in grayscale mode
image = cv2.imread("girl.jpg", cv2.IMREAD_GRAYSCALE)

# Check whether the image is loaded correctly
if image is None:
    print("Error: Image not found.")
else:
    # Define Sobel kernel for x-direction
    # Detects horizontal intensity change
    # Highlights vertical edges in the image
    sobel_x_kernel = np.array([
        [-1, 0, 1],
        [-2, 0, 2],
        [-1, 0, 1]
    ], dtype=np.float32)

    # Define Sobel kernel for y-direction
    # Detects vertical intensity change
    # Highlights horizontal edges in the image
    sobel_y_kernel = np.array([
        [-1, -2, -1],
        [ 0,  0,  0],
        [ 1,  2,  1]
    ], dtype=np.float32)

    # Apply Sobel kernel in x-direction
    # cv2.CV_64F is used so negative and large gradient values can be stored
    sobel_x = cv2.filter2D(image, cv2.CV_64F, sobel_x_kernel)

    # Apply Sobel kernel in y-direction
    sobel_y = cv2.filter2D(image, cv2.CV_64F, sobel_y_kernel)

    # Calculate the gradient magnitude
    # This combines x and y edge responses into one final edge image
    gradient_magnitude = np.sqrt(sobel_x**2 + sobel_y**2)

    # Normalize the result into the range 0 to 255 for display
    normalized_gradient = cv2.normalize(
        gradient_magnitude, None, 0, 255, cv2.NORM_MINMAX
    )

    # Convert the normalized image to unsigned 8-bit format
    normalized_gradient = normalized_gradient.astype(np.uint8)

    # Display the original image
    cv2.imshow("Original Image", image)

    # Display the Sobel edge detection result
    cv2.imshow("Sobel Edge Detection", normalized_gradient)

    # Wait for a key press
    cv2.waitKey(0)

    # Close all image windows
    cv2.destroyAllWindows()
```

---

## 4. Median Filter Code with Explain Comments

```python
# ------------------------------------------------------------
# MEDIAN FILTER
# ------------------------------------------------------------

# Import OpenCV
import cv2

# Import NumPy
import numpy as np


# Define a custom median filter function
def custom_median_filter(image, kernel_size):
    """
    Apply a median filter to a grayscale image.

    Parameters:
        image       : Input grayscale image
        kernel_size : Size of the filter window (must be odd)

    Returns:
        filtered_image : Output image after median filtering
    """

    # Get image dimensions
    height, width = image.shape

    # Create an empty output image
    filtered_image = np.zeros_like(image)

    # Calculate half kernel size
    kernel_half = kernel_size // 2

    # Pad the image to handle border pixels
    padded_image = cv2.copyMakeBorder(
        image,
        kernel_half, kernel_half,   # top and bottom padding
        kernel_half, kernel_half,   # left and right padding
        cv2.BORDER_CONSTANT         # zero padding
    )

    # Loop through all pixels in the original image
    for i in range(height):
        for j in range(width):

            # Create an empty list to store neighborhood pixel values
            window = []

            # Traverse through the local kernel area
            for ki in range(-kernel_half, kernel_half + 1):
                for kj in range(-kernel_half, kernel_half + 1):

                    # Add each neighboring pixel value to the window list
                    window.append(
                        padded_image[i + ki + kernel_half, j + kj + kernel_half]
                    )

            # Sort the window values in ascending order
            window.sort()

            # Pick the middle value (median)
            median_value = window[len(window) // 2]

            # Assign the median value to the output image
            filtered_image[i, j] = median_value

    # Return the denoised image
    return filtered_image


# Load the image in grayscale mode
image = cv2.imread("girl.jpg", cv2.IMREAD_GRAYSCALE)

# Check whether the image loaded correctly
if image is None:
    print("Error: Image not found.")
else:
    # Define kernel size
    kernel_size = 5

    # Apply the custom median filter
    filtered_image = custom_median_filter(image, kernel_size)

    # Display the original image
    cv2.imshow("Original Image", image)

    # Display the median filtered image
    cv2.imshow("Median Filtered Image", filtered_image)

    # Wait for a key press
    cv2.waitKey(0)

    # Close all display windows
    cv2.destroyAllWindows()
```

---

# 5. One Combined Full Code

If you want to keep everything in **one file**, use this combined version:

```python
import cv2
import numpy as np


# ------------------------------------------------------------
# MEAN FILTER FUNCTION
# ------------------------------------------------------------
def mean_filter(image, kernel_size):
    padded_image = cv2.copyMakeBorder(
        image,
        kernel_size // 2,
        kernel_size // 2,
        kernel_size // 2,
        kernel_size // 2,
        cv2.BORDER_CONSTANT
    )

    filtered_image = np.zeros_like(image)

    for y in range(image.shape[0]):
        for x in range(image.shape[1]):
            window = padded_image[y:y + kernel_size, x:x + kernel_size]
            filtered_image[y, x] = np.mean(window)

    return filtered_image


# ------------------------------------------------------------
# GAUSSIAN KERNEL FUNCTION
# ------------------------------------------------------------
def gaussian_kernel(size, sigma=1.0):
    ax = np.arange(-(size // 2), size // 2 + 1)
    xx, yy = np.meshgrid(ax, ax)
    kernel = np.exp(-(xx**2 + yy**2) / (2 * sigma**2))
    kernel = kernel / np.sum(kernel)
    return kernel


# ------------------------------------------------------------
# CONVOLUTION FUNCTION
# ------------------------------------------------------------
def convolve(image, kernel):
    pad = kernel.shape[0] // 2
    padded_image = cv2.copyMakeBorder(image, pad, pad, pad, pad, cv2.BORDER_CONSTANT)
    output = np.zeros_like(image, dtype=np.float32)

    for y in range(image.shape[0]):
        for x in range(image.shape[1]):
            window = padded_image[y:y + kernel.shape[0], x:x + kernel.shape[1]]
            output[y, x] = np.sum(window * kernel)

    return output.astype(np.uint8)


# ------------------------------------------------------------
# MEDIAN FILTER FUNCTION
# ------------------------------------------------------------
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
            filtered_image[i, j] = window[len(window) // 2]

    return filtered_image


# ------------------------------------------------------------
# LOAD IMAGE
# ------------------------------------------------------------
image = cv2.imread("girl.jpg", cv2.IMREAD_GRAYSCALE)

if image is None:
    print("Error: Image not found.")
else:
    # --------------------------------------------------------
    # APPLY MEAN FILTER
    # --------------------------------------------------------
    mean_result = mean_filter(image, 5)

    # --------------------------------------------------------
    # APPLY GAUSSIAN FILTER
    # --------------------------------------------------------
    g_kernel = gaussian_kernel(5, 1.0)
    gaussian_result = convolve(image, g_kernel)

    # --------------------------------------------------------
    # APPLY SOBEL FILTER
    # --------------------------------------------------------
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

    sobel_x = cv2.filter2D(image, cv2.CV_64F, sobel_x_kernel)
    sobel_y = cv2.filter2D(image, cv2.CV_64F, sobel_y_kernel)

    sobel_result = np.sqrt(sobel_x**2 + sobel_y**2)
    sobel_result = cv2.normalize(sobel_result, None, 0, 255, cv2.NORM_MINMAX)
    sobel_result = sobel_result.astype(np.uint8)

    # --------------------------------------------------------
    # APPLY MEDIAN FILTER
    # --------------------------------------------------------
    median_result = custom_median_filter(image, 5)

    # --------------------------------------------------------
    # DISPLAY RESULTS
    # --------------------------------------------------------
    cv2.imshow("Original Image", image)
    cv2.imshow("Mean Filter", mean_result)
    cv2.imshow("Gaussian Filter", gaussian_result)
    cv2.imshow("Sobel Edge Detection", sobel_result)
    cv2.imshow("Median Filter", median_result)

    cv2.waitKey(0)
    cv2.destroyAllWindows()
```

---

# 6. Important Exam Note

Remember these quickly:

* **Mean Filter** → average of nearby pixels
* **Gaussian Filter** → weighted average using Gaussian values
* **Sobel Filter** → detects edges using gradients
* **Median Filter** → uses median value, very good for salt-and-pepper noise

---

