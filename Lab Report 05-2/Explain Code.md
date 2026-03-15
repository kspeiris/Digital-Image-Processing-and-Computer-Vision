

# Complete Full Explanation of the Code



## 1. Import required libraries

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt
import time
from scipy.ndimage import convolve
from google.colab.patches import cv2_imshow
from google.colab import files
```

### Explanation

This section imports all the libraries needed for the practical.

### What each part means

* `import cv2`
  Imports the **OpenCV** library, which is used for:

  * reading images
  * filtering images
  * thresholding
  * edge detection
  * morphological operations

* `import numpy as np`
  Imports **NumPy**, which is used for:

  * arrays
  * matrices
  * kernels
  * padding
  * mathematical operations on images

* `import matplotlib.pyplot as plt`
  Imports **Matplotlib** for plotting and displaying images in figure format.

* `import time`
  Used to measure execution time of filters for performance comparison.

* `from scipy.ndimage import convolve`
  Imports the `convolve` function from SciPy.
  It is used to apply convolution between an image and a filter kernel.

* `from google.colab.patches import cv2_imshow`
  Used in **Google Colab** to display OpenCV images correctly.

* `from google.colab import files`
  Used to upload image files inside Google Colab.

### Why this is used

Before applying filters or image processing operations, the required libraries must be imported.

---

## 2. Define the Mean Filter function

```python
def mean_filter(image, kernel_size):
    # Ensure kernel size is odd
    if kernel_size % 2 == 0:
        kernel_size += 1

    # Calculate padding size
    pad_size = kernel_size // 2

    # Add zero padding around the image
    padded_image = cv2.copyMakeBorder(
        image,
        pad_size, pad_size, pad_size, pad_size,
        cv2.BORDER_CONSTANT,
        value=0
    )

    # Create output image
    filtered_image = np.zeros_like(image, dtype=np.float32)

    # Apply mean filtering
    for y in range(image.shape[0]):
        for x in range(image.shape[1]):
            window = padded_image[y:y+kernel_size, x:x+kernel_size]
            filtered_image[y, x] = np.mean(window)

    # Convert back to original type
    filtered_image = np.clip(filtered_image, 0, 255).astype(image.dtype)

    return filtered_image
```

### Explanation

This function manually applies a **mean filter** to an image.

### What each part means

* `def mean_filter(image, kernel_size):`
  Defines a function named `mean_filter` that takes:

  * `image` → the input grayscale image
  * `kernel_size` → size of the filtering window such as `3`, `5`, or `7`

* `if kernel_size % 2 == 0:`
  Checks whether the kernel size is even.

* `kernel_size += 1`
  If the size is even, it changes it to the next odd number.
  Filters usually need odd dimensions like `3x3`, `5x5`, `7x7`.

* `pad_size = kernel_size // 2`
  Calculates how much padding is needed around the image.

* `cv2.copyMakeBorder(...)`
  Adds a border around the image using zeros.
  This helps process pixels at the edges.

* `filtered_image = np.zeros_like(image, dtype=np.float32)`
  Creates an empty output image with the same shape as the original image.

* `for y in range(image.shape[0]):`
  Loops through all rows of the image.

* `for x in range(image.shape[1]):`
  Loops through all columns of the image.

* `window = padded_image[y:y+kernel_size, x:x+kernel_size]`
  Extracts a local neighborhood around the current pixel.

* `filtered_image[y, x] = np.mean(window)`
  Replaces the pixel with the average value of the neighborhood.

* `np.clip(filtered_image, 0, 255)`
  Ensures values stay within the valid image range.

* `.astype(image.dtype)`
  Converts the output back to the same data type as the original image.

* `return filtered_image`
  Returns the filtered image.

### Why this is used

The mean filter is used to:

* reduce noise
* smooth an image
* blur fine details

### Important note

The mean filter is simple, but it may blur edges.

---

## 3. Upload and read the image for Mean Filtering

```python
print("Please upload 'Girl.jpg' file")
uploaded = files.upload()

if 'Girl.jpg' in uploaded:
    image = cv2.imread('Girl.jpg', cv2.IMREAD_GRAYSCALE)

    if image is None:
        image = cv2.imread('Girl.jpg')
        if image is not None:
            image = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
        else:
            print("Error: Could not load image")
            exit()
```

### Explanation

This section uploads and reads the image file.

### What each part means

* `print("Please upload 'Girl.jpg' file")`
  Displays a message asking the user to upload the image.

* `uploaded = files.upload()`
  Opens the upload dialog in Google Colab.

* `if 'Girl.jpg' in uploaded:`
  Checks whether the uploaded file is named `Girl.jpg`.

* `cv2.imread('Girl.jpg', cv2.IMREAD_GRAYSCALE)`
  Reads the image directly in grayscale mode.

* `if image is None:`
  Checks whether the grayscale loading failed.

* `image = cv2.imread('Girl.jpg')`
  Tries to read the image in normal color mode.

* `cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)`
  Converts the color image to grayscale.

* `print("Error: Could not load image")`
  Shows an error if the image cannot be loaded.

* `exit()`
  Stops execution if loading fails.

### Why this is used

The filters in this practical are applied to grayscale images, so the image must be loaded correctly in grayscale.

---

## 4. Display image information

```python
print(f"Image shape: {image.shape}")
print(f"Image dtype: {image.dtype}")
print(f"Pixel range: [{image.min()}, {image.max()}]")
```

### Explanation

This section prints basic details about the image.

### What each part means

* `image.shape`
  Shows the image dimensions as:

  * height
  * width

* `image.dtype`
  Shows the image data type such as `uint8`

* `image.min()` and `image.max()`
  Show the minimum and maximum pixel values in the image.

### Why this is used

This helps confirm:

* the image loaded correctly
* the image is grayscale
* the pixel values are valid

---

## 5. Define kernel sizes and show the original image

```python
kernel_sizes = [3, 5, 7, 9]

print("Original Image:")
cv2_imshow(image)
```

### Explanation

This section defines different kernel sizes and displays the original image.

### What each part means

* `kernel_sizes = [3, 5, 7, 9]`
  Creates a list of kernel sizes to test.

* `cv2_imshow(image)`
  Displays the original grayscale image in Google Colab.

### Why this is used

Using different kernel sizes helps compare how the amount of smoothing changes.

### Important note

Larger kernels produce stronger smoothing, but also more blur.

---

## 6. Apply Mean Filter for different kernel sizes

```python
for kernel_size in kernel_sizes:
    print(f"Applying Mean Filter with Kernel Size {kernel_size}x{kernel_size}")

    filtered_image = mean_filter(image, kernel_size)
    cv2_imshow(filtered_image)

    difference = cv2.absdiff(image.astype(np.float32), filtered_image.astype(np.float32))
    difference_normalized = (difference / difference.max() * 255).astype(np.uint8)

    print("Difference Map:")
    cv2_imshow(difference_normalized)

    print(f"Original mean: {np.mean(image):.2f}")
    print(f"Filtered mean: {np.mean(filtered_image):.2f}")
    print(f"Max change: {difference.max():.2f}")
    print(f"Average change: {np.mean(difference):.2f}")
```

### Explanation

This loop applies the mean filter using each kernel size and compares the results.

### What each part means

* `for kernel_size in kernel_sizes:`
  Loops through each kernel size in the list.

* `filtered_image = mean_filter(image, kernel_size)`
  Applies the custom mean filter.

* `cv2_imshow(filtered_image)`
  Displays the filtered image.

* `cv2.absdiff(...)`
  Calculates the absolute difference between the original and filtered image.

* `difference_normalized = (difference / difference.max() * 255).astype(np.uint8)`
  Normalizes the difference image for better display.

* `cv2_imshow(difference_normalized)`
  Displays the difference map.

* `np.mean(image)`
  Computes the average pixel value in the original image.

* `np.mean(filtered_image)`
  Computes the average pixel value in the filtered image.

* `difference.max()`
  Shows the maximum pixel change caused by filtering.

* `np.mean(difference)`
  Shows the average change across the image.

### Why this is used

This section helps analyze:

* how much smoothing occurred
* how much the image changed
* how kernel size affects the output

---

## 7. Define the Gaussian Kernel function

```python
def gaussian_kernel(size, sigma=None):
    if sigma is None:
        sigma = size / 6

    if size % 2 == 0:
        size += 1

    ax = np.arange(-size // 2 + 1., size // 2 + 1.)
    xx, yy = np.meshgrid(ax, ax)

    kernel = np.exp(-(xx**2 + yy**2) / (2. * sigma**2))

    return kernel / np.sum(kernel)
```

### Explanation

This function creates a **Gaussian kernel**.

### What each part means

* `def gaussian_kernel(size, sigma=None):`
  Defines a function that generates a Gaussian filter kernel.

* `if sigma is None:`
  Checks whether a sigma value is given.

* `sigma = size / 6`
  Estimates sigma from the kernel size.

* `if size % 2 == 0:`
  Ensures the kernel size is odd.

* `ax = np.arange(...)`
  Creates coordinate values for the kernel.

* `xx, yy = np.meshgrid(ax, ax)`
  Creates 2D coordinate grids.

* `np.exp(-(xx**2 + yy**2) / (2. * sigma**2))`
  Applies the Gaussian equation to compute kernel values.

* `kernel / np.sum(kernel)`
  Normalizes the kernel so its total sum becomes `1`.

### Why this is used

The Gaussian filter smooths the image using weighted averaging.

### Important note

In the Gaussian filter:

* center pixels get higher importance
* distant pixels get lower importance

This usually gives better smoothing than the mean filter.

---

## 8. Upload and read the image for Gaussian Filtering

```python
print("Please upload your 'lena.jpg' file")
uploaded = files.upload()

if 'lena.jpg' in uploaded:
    image = cv2.imread('lena.jpg', cv2.IMREAD_GRAYSCALE)

    if image is None:
        image_color = cv2.imread('lena.jpg')
        if image_color is not None:
            image = cv2.cvtColor(image_color, cv2.COLOR_BGR2GRAY)
```

### Explanation

This section uploads and reads `lena.jpg` as a grayscale image.

### What each part means

* `files.upload()`
  Lets the user upload the image file.

* `cv2.imread('lena.jpg', cv2.IMREAD_GRAYSCALE)`
  Reads the image in grayscale.

* `if image is None:`
  Checks whether direct grayscale loading failed.

* `cv2.cvtColor(image_color, cv2.COLOR_BGR2GRAY)`
  Converts the color image to grayscale.

### Why this is used

The Gaussian filter in this practical is applied to grayscale images.

---

## 9. Display Gaussian kernels

```python
kernel_sizes = [3, 5, 7, 9, 11]

fig_kernels, axes_kernels = plt.subplots(1, len(kernel_sizes), figsize=(15, 4))
for i, size in enumerate(kernel_sizes):
    kernel = gaussian_kernel(size)
    axes_kernels[i].imshow(kernel, cmap='hot')
    axes_kernels[i].set_title(f'Size: {size}x{size}\nσ={size/6:.1f}')
    axes_kernels[i].axis('off')
plt.tight_layout()
plt.show()
```

### Explanation

This section creates and displays Gaussian kernels of different sizes.

### What each part means

* `kernel_sizes = [3, 5, 7, 9, 11]`
  Defines the kernel sizes to test.

* `plt.subplots(1, len(kernel_sizes), figsize=(15, 4))`
  Creates one row of subplots.

* `for i, size in enumerate(kernel_sizes):`
  Loops through each kernel size.

* `kernel = gaussian_kernel(size)`
  Generates a Gaussian kernel for that size.

* `imshow(kernel, cmap='hot')`
  Displays the kernel as an image.

* `set_title(...)`
  Shows kernel size and sigma in the title.

* `axis('off')`
  Hides axis lines and tick marks.

* `plt.tight_layout()`
  Adjusts spacing between plots.

* `plt.show()`
  Displays the figure.

### Why this is used

Visualizing the kernel helps understand:

* the center has higher weights
* values decrease gradually away from the center

---

## 10. Apply Gaussian Filtering

```python
fig, axes = plt.subplots(2, 3, figsize=(15, 10))
axes[0, 0].imshow(image, cmap='gray')
axes[0, 0].set_title('Original Image')
axes[0, 0].axis('off')

for i, kernel_size in enumerate(kernel_sizes[:5]):
    kernel = gaussian_kernel(kernel_size)
    filtered_image = convolve(image, kernel)

    row = (i + 1) // 3
    col = (i + 1) % 3

    axes[row, col].imshow(filtered_image, cmap='gray')
    axes[row, col].set_title(f'Gaussian {kernel_size}x{kernel_size}')
    axes[row, col].axis('off')

    print(f"Kernel Size: {kernel_size}x{kernel_size}")
    cv2_imshow(filtered_image.astype(np.uint8))

plt.tight_layout()
plt.show()
```

### Explanation

This section applies Gaussian filtering using different kernel sizes.

### What each part means

* `plt.subplots(2, 3, figsize=(15, 10))`
  Creates a `2x3` grid for displaying images.

* `axes[0, 0].imshow(image, cmap='gray')`
  Displays the original image in the first position.

* `for i, kernel_size in enumerate(kernel_sizes[:5]):`
  Loops through the first five kernel sizes.

* `kernel = gaussian_kernel(kernel_size)`
  Creates a Gaussian kernel.

* `filtered_image = convolve(image, kernel)`
  Applies convolution between the image and the kernel.

* `row = (i + 1) // 3` and `col = (i + 1) % 3`
  Compute subplot positions.

* `imshow(filtered_image, cmap='gray')`
  Displays the filtered image.

* `cv2_imshow(filtered_image.astype(np.uint8))`
  Also shows the result in OpenCV display format.

### Why this is used

This allows comparison of Gaussian smoothing for different kernel sizes.

### Important note

As kernel size increases:

* smoothing increases
* noise reduces more
* details blur more

---

## 11. Compare custom Gaussian filter with OpenCV GaussianBlur

```python
test_kernel_size = 7
sigma = test_kernel_size / 6

your_kernel = gaussian_kernel(test_kernel_size, sigma)
your_filtered = convolve(image, your_kernel)

opencv_filtered = cv2.GaussianBlur(image, (test_kernel_size, test_kernel_size), sigma)

difference = np.abs(your_filtered.astype(np.float32) - opencv_filtered.astype(np.float32))

print(f"Maximum difference: {difference.max():.6f}")
print(f"Mean difference: {difference.mean():.6f}")
```

### Explanation

This section compares the custom Gaussian filter with OpenCV’s built-in Gaussian filter.

### What each part means

* `test_kernel_size = 7`
  Chooses a `7x7` kernel for testing.

* `sigma = test_kernel_size / 6`
  Sets the sigma value.

* `your_kernel = gaussian_kernel(...)`
  Creates the custom Gaussian kernel.

* `your_filtered = convolve(image, your_kernel)`
  Applies the custom Gaussian filter.

* `opencv_filtered = cv2.GaussianBlur(...)`
  Applies OpenCV’s built-in Gaussian filter.

* `difference = np.abs(...)`
  Computes the absolute difference between the two results.

* `difference.max()`
  Shows the maximum difference.

* `difference.mean()`
  Shows the average difference.

### Why this is used

This checks whether the custom implementation is close to the standard OpenCV implementation.

---

## 12. Define the Sobel Filter function

```python
def sobel_filter(image):
    sobel_x_kernel = np.array([
        [-1, 0, 1],
        [-2, 0, 2],
        [-1, 0, 1]
    ])

    sobel_y_kernel = np.array([
        [-1, -2, -1],
        [ 0,  0,  0],
        [ 1,  2,  1]
    ])

    sobel_x = cv2.filter2D(image.astype(np.float32), cv2.CV_32F, sobel_x_kernel)
    sobel_y = cv2.filter2D(image.astype(np.float32), cv2.CV_32F, sobel_y_kernel)

    gradient_magnitude = np.sqrt(sobel_x**2 + sobel_y**2)

    normalized_gradient = cv2.normalize(
        gradient_magnitude, None, 0, 255, cv2.NORM_MINMAX, cv2.CV_8U
    )

    gradient_direction = np.arctan2(sobel_y, sobel_x) * (180 / np.pi)

    return normalized_gradient, gradient_magnitude, gradient_direction, sobel_x, sobel_y
```

### Explanation

This function applies the **Sobel operator** for edge detection.

### What each part means

* `sobel_x_kernel`
  Detects horizontal intensity changes.

* `sobel_y_kernel`
  Detects vertical intensity changes.

* `cv2.filter2D(...)`
  Applies the Sobel kernels to the image.

* `gradient_magnitude = np.sqrt(sobel_x**2 + sobel_y**2)`
  Computes the strength of the edge.

* `cv2.normalize(...)`
  Scales the gradient values into the display range `0–255`.

* `gradient_direction = np.arctan2(sobel_y, sobel_x) * (180 / np.pi)`
  Computes the edge direction in degrees.

* `return ...`
  Returns:

  * normalized edge image
  * exact gradient magnitude
  * direction
  * x-gradient
  * y-gradient

### Why this is used

The Sobel filter is used to detect edges by measuring changes in brightness.

---

## 13. Define manual convolution

```python
def manual_convolution(image, kernel):
    kernel_size = kernel.shape[0]
    pad_size = kernel_size // 2

    padded = np.pad(image, pad_size, mode='constant', constant_values=0)
    output = np.zeros_like(image, dtype=np.float32)

    for y in range(image.shape[0]):
        for x in range(image.shape[1]):
            window = padded[y:y+kernel_size, x:x+kernel_size]
            output[y, x] = np.sum(window * kernel)

    return output
```

### Explanation

This function performs convolution manually.

### What each part means

* `kernel.shape[0]`
  Gets the kernel size.

* `np.pad(...)`
  Adds padding to the image border.

* `np.zeros_like(...)`
  Creates an empty output image.

* `window = padded[y:y+kernel_size, x:x+kernel_size]`
  Extracts the neighborhood for the current pixel.

* `np.sum(window * kernel)`
  Multiplies the window with the kernel and sums the result.

### Why this is used

This helps understand the basic idea of convolution, which is used in many filters.

---

## 14. Define manual Sobel implementation

```python
def sobel_manual(image):
    sobel_x_kernel = np.array([
        [-1, 0, 1],
        [-2, 0, 2],
        [-1, 0, 1]
    ])

    sobel_y_kernel = np.array([
        [-1, -2, -1],
        [ 0,  0,  0],
        [ 1,  2,  1]
    ])

    sobel_x = manual_convolution(image.astype(np.float32), sobel_x_kernel)
    sobel_y = manual_convolution(image.astype(np.float32), sobel_y_kernel)

    gradient_magnitude = np.sqrt(sobel_x**2 + sobel_y**2)
    grad_magnitude_normalized = cv2.normalize(
        gradient_magnitude, None, 0, 255, cv2.NORM_MINMAX, cv2.CV_8U
    )

    return grad_magnitude_normalized, sobel_x, sobel_y
```

### Explanation

This function applies the Sobel operator using the manual convolution method.

### What each part means

* `manual_convolution(...)`
  Applies the Sobel kernels without using OpenCV’s built-in filter function.

* `np.sqrt(sobel_x**2 + sobel_y**2)`
  Computes the edge strength.

* `cv2.normalize(...)`
  Converts the result into displayable range.

### Why this is used

This is useful for learning how Sobel edge detection works internally.

---

## 15. Define the custom Median Filter

```python
def custom_median_filter(image, kernel_size):
    height, width = image.shape
    filtered_image = np.zeros_like(image)
    kernel_half = kernel_size // 2

    for i in range(height):
        for j in range(width):
            kernel = []

            for ki in range(-kernel_half, kernel_half + 1):
                for kj in range(-kernel_half, kernel_half + 1):
                    ni = i + ki
                    nj = j + kj

                    if 0 <= ni < height and 0 <= nj < width:
                        kernel.append(image[ni, nj])

            median_value = np.median(kernel)
            filtered_image[i, j] = median_value

    return filtered_image
```

### Explanation

This function manually applies a **median filter**.

### What each part means

* `height, width = image.shape`
  Gets the image dimensions.

* `filtered_image = np.zeros_like(image)`
  Creates an empty output image.

* `kernel_half = kernel_size // 2`
  Calculates the radius of the kernel.

* `for i in range(height):` and `for j in range(width):`
  Visit every pixel in the image.

* `kernel = []`
  Creates a list to store neighborhood pixel values.

* `for ki ...` and `for kj ...`
  Loop through the neighborhood window.

* `if 0 <= ni < height and 0 <= nj < width:`
  Checks that the neighboring pixel is inside the image.

* `kernel.append(image[ni, nj])`
  Adds valid neighboring pixels to the list.

* `median_value = np.median(kernel)`
  Finds the median value of the neighborhood.

* `filtered_image[i, j] = median_value`
  Replaces the current pixel with the median value.

### Why this is used

The median filter is very effective for removing:

* salt-and-pepper noise
* impulse noise

### Important note

Unlike the mean filter, the median filter usually preserves edges better.

---

## 16. Define the optimized Median Filter

```python
def optimized_median_filter(image, kernel_size):
    height, width = image.shape
    filtered_image = np.zeros_like(image)
    kernel_half = kernel_size // 2

    padded = np.pad(image, kernel_half, mode='reflect')

    for i in range(height):
        for j in range(width):
            window = padded[i:i+kernel_size, j:j+kernel_size]
            filtered_image[i, j] = np.median(window)

    return filtered_image
```

### Explanation

This is an improved version of the median filter.

### What each part means

* `np.pad(image, kernel_half, mode='reflect')`
  Adds reflected padding around the image.

* `window = padded[i:i+kernel_size, j:j+kernel_size]`
  Extracts the full neighborhood window directly.

* `np.median(window)`
  Finds the median value.

### Why this is used

This version is:

* faster
* cleaner
* easier to manage at image borders

---

## 17. Add noise to the image

```python
def add_noise_to_image(image, noise_type='salt_and_pepper', noise_level=0.05):
    noisy_image = image.copy().astype(np.float32)
    height, width = image.shape

    if noise_type == 'salt_and_pepper':
        salt = np.random.rand(height, width) < noise_level / 2
        pepper = np.random.rand(height, width) < noise_level / 2
        noisy_image[salt] = 255
        noisy_image[pepper] = 0

    elif noise_type == 'gaussian':
        noise = np.random.normal(0, noise_level * 255, (height, width))
        noisy_image = image + noise
        noisy_image = np.clip(noisy_image, 0, 255)

    elif noise_type == 'speckle':
        noise = np.random.randn(height, width) * noise_level
        noisy_image = image * (1 + noise)
        noisy_image = np.clip(noisy_image, 0, 255)

    return noisy_image.astype(np.uint8)
```

### Explanation

This function artificially adds noise to the image.

### What each part means

* `image.copy().astype(np.float32)`
  Creates a floating-point copy of the image.

* `noise_type='salt_and_pepper'`
  Sets the default noise type.

* `noise_level=0.05`
  Controls the amount of noise.

#### Salt-and-Pepper Noise

* `np.random.rand(height, width) < noise_level / 2`
  Randomly selects pixels for salt and pepper noise.

* `noisy_image[salt] = 255`
  Sets some pixels to white.

* `noisy_image[pepper] = 0`
  Sets some pixels to black.

#### Gaussian Noise

* `np.random.normal(...)`
  Generates Gaussian-distributed noise.

* `noisy_image = image + noise`
  Adds the noise to the image.

#### Speckle Noise

* `np.random.randn(...) * noise_level`
  Creates random multiplicative noise.

* `image * (1 + noise)`
  Applies speckle noise to the image.

* `np.clip(noisy_image, 0, 255)`
  Keeps values inside valid image range.

### Why this is used

Noise is added to test how well each filter removes it.

---

## 18. Compare different filters

```python
def compare_filters(noisy_image, kernel_size):
    start_time = time.time()
    custom_result = custom_median_filter(noisy_image, kernel_size)
    custom_time = time.time() - start_time

    start_time = time.time()
    optimized_result = optimized_median_filter(noisy_image, kernel_size)
    optimized_time = time.time() - start_time

    start_time = time.time()
    opencv_result = cv2.medianBlur(noisy_image, kernel_size)
    opencv_time = time.time() - start_time

    start_time = time.time()
    gaussian_result = cv2.GaussianBlur(noisy_image, (kernel_size, kernel_size), 0)
    gaussian_time = time.time() - start_time

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
```

### Explanation

This function compares several filters and measures their execution time.

### What each part means

* `start_time = time.time()`
  Records the current time before running a filter.

* `custom_median_filter(...)`
  Applies the manual median filter.

* `time.time() - start_time`
  Computes how long the filter took.

* `cv2.medianBlur(...)`
  Applies OpenCV’s built-in median filter.

* `cv2.GaussianBlur(...)`
  Applies Gaussian smoothing.

* `cv2.blur(...)`
  Applies a mean filter.

* `return { ... }`
  Returns a dictionary containing:

  * filtered images
  * execution times

### Why this is used

This helps compare:

* filtering quality
* speed
* manual implementation vs built-in implementation

---

## 19. Convert image to binary using Otsu Thresholding

```python
img_color = cv2.imread('lena.jpg')

if img_color is None:
    print("Error: Could not read image file.")
else:
    img_gray = cv2.imread('lena.jpg', cv2.IMREAD_GRAYSCALE)

    ret, img_binary = cv2.threshold(
        img_gray, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU
    )

    print(f"Otsu's calculated threshold value: {ret}")
    cv2.imwrite('lena_binary.jpg', img_binary)
```

### Explanation

This section converts the grayscale image into a binary image.

### What each part means

* `cv2.imread('lena.jpg')`
  Loads the original image.

* `if img_color is None:`
  Checks if the file was read correctly.

* `cv2.imread('lena.jpg', cv2.IMREAD_GRAYSCALE)`
  Loads the image in grayscale.

* `cv2.threshold(...)`
  Applies thresholding.

* `cv2.THRESH_BINARY + cv2.THRESH_OTSU`
  Uses binary thresholding with **Otsu’s method**.

* `ret`
  Stores the threshold value automatically chosen by Otsu’s method.

* `img_binary`
  Stores the binary output image.

* `cv2.imwrite('lena_binary.jpg', img_binary)`
  Saves the binary image.

### Why this is used

Morphological operations are usually applied to binary images.

### Important note

Otsu’s method automatically finds a suitable threshold value.

---

## 20. Apply Erosion and Dilation

```python
kernel = np.ones((3, 3), np.uint8)

eroded_image = cv2.erode(img_binary, kernel, iterations=1)
dilated_image = cv2.dilate(img_binary, kernel, iterations=1)

print("Binary Image:")
cv2_imshow(img_binary)

print("Eroded Image:")
cv2_imshow(eroded_image)

print("Dilated Image:")
cv2_imshow(dilated_image)
```

### Explanation

This section applies basic morphological operations.

### What each part means

* `kernel = np.ones((3, 3), np.uint8)`
  Creates a `3x3` structuring element.

* `cv2.erode(img_binary, kernel, iterations=1)`
  Applies erosion once.

* `cv2.dilate(img_binary, kernel, iterations=1)`
  Applies dilation once.

* `cv2_imshow(...)`
  Displays the original and processed images.

### Why this is used

#### Erosion

* shrinks white objects
* removes small white noise
* breaks thin connections

#### Dilation

* enlarges white objects
* fills small holes
* connects nearby components

---

## 21. Create a synthetic binary image for morphology testing

```python
img = np.zeros((200, 200), dtype=np.uint8)

cv2.ellipse(img, (100, 100), (60, 40), 0, 0, 360, 255, -1)

cv2.circle(img, (30, 30), 3, 255, -1)
cv2.circle(img, (170, 30), 3, 255, -1)
cv2.circle(img, (30, 170), 3, 255, -1)
cv2.circle(img, (170, 170), 3, 255, -1)

cv2.circle(img, (100, 100), 5, 0, -1)
cv2.circle(img, (80, 80), 3, 0, -1)
cv2.circle(img, (120, 120), 4, 0, -1)

cv2.line(img, (140, 60), (180, 40), 255, 2)
cv2.line(img, (60, 140), (20, 160), 255, 2)

cv2.circle(img, (140, 100), 8, 255, -1)
cv2.circle(img, (60, 100), 6, 255, -1)
```

### Explanation

This section creates an artificial binary image.

### What each part means

* `np.zeros((200, 200), dtype=np.uint8)`
  Creates a black image of size `200x200`.

* `cv2.ellipse(...)`
  Draws a white ellipse as the main object.

* `cv2.circle(...)`
  Adds small white dots and small black holes.

* `cv2.line(...)`
  Adds thin extensions or protrusions.

### Why this is used

This synthetic image is useful for clearly observing the effects of morphological operations.

---

## 22. Define different structuring elements

```python
kernel_small = np.ones((3, 3), np.uint8)
kernel_medium = np.ones((5, 5), np.uint8)
kernel_large = np.ones((7, 7), np.uint8)
```

### Explanation

This section creates three structuring elements of different sizes.

### What each part means

* `3x3` kernel
  Small effect

* `5x5` kernel
  Medium effect

* `7x7` kernel
  Stronger effect

### Why this is used

Different kernel sizes produce different strengths of morphological change.

---

## 23. Apply Opening and Closing

```python
opening_small = cv2.morphologyEx(img, cv2.MORPH_OPEN, kernel_small)
opening_medium = cv2.morphologyEx(img, cv2.MORPH_OPEN, kernel_medium)
opening_large = cv2.morphologyEx(img, cv2.MORPH_OPEN, kernel_large)

closing_small = cv2.morphologyEx(img, cv2.MORPH_CLOSE, kernel_small)
closing_medium = cv2.morphologyEx(img, cv2.MORPH_CLOSE, kernel_medium)
closing_large = cv2.morphologyEx(img, cv2.MORPH_CLOSE, kernel_large)
```

### Explanation

This section applies **opening** and **closing** using different kernel sizes.

### What each part means

* `cv2.MORPH_OPEN`
  Performs:

  * erosion
  * then dilation

* `cv2.MORPH_CLOSE`
  Performs:

  * dilation
  * then erosion

### Why this is used

#### Opening

Used to:

* remove small white noise
* separate small attached objects
* smooth outer boundaries

#### Closing

Used to:

* fill small black holes
* connect small gaps
* smooth inner boundaries

### Important note

Larger kernels produce stronger changes in object shape.

---

## 24. Apply Morphological Gradient, Top Hat, and Black Hat

```python
gradient = cv2.morphologyEx(img, cv2.MORPH_GRADIENT, kernel_medium)
tophat = cv2.morphologyEx(img, cv2.MORPH_TOPHAT, kernel_medium)
blackhat = cv2.morphologyEx(img, cv2.MORPH_BLACKHAT, kernel_medium)
```

### Explanation

This section applies extra morphological operations.

### What each part means

* `cv2.MORPH_GRADIENT`
  Computes the difference between dilation and erosion.
  It highlights boundaries.

* `cv2.MORPH_TOPHAT`
  Computes the difference between the original image and its opening.
  It extracts small bright details.

* `cv2.MORPH_BLACKHAT`
  Computes the difference between the closing and the original image.
  It extracts small dark details.

### Why this is used

These operations are useful for:

* boundary detection
* bright object extraction
* dark object extraction

---

## 25. Define the PSNR calculation function

```python
def calculate_psnr(original, filtered):
    mse = np.mean((original.astype(np.float32) - filtered.astype(np.float32)) ** 2)

    if mse == 0:
        return float('inf')

    max_pixel = 255.0
    psnr = 20 * np.log10(max_pixel / np.sqrt(mse))
    return psnr
```

### Explanation

This function calculates **PSNR**.

### What each part means

* `mse = np.mean(...)`
  Computes the **Mean Squared Error** between the original and filtered image.

* `if mse == 0:`
  If there is no difference, the images are identical.

* `return float('inf')`
  Returns infinity because PSNR is infinite when error is zero.

* `max_pixel = 255.0`
  Sets the maximum possible pixel value for an 8-bit image.

* `psnr = 20 * np.log10(max_pixel / np.sqrt(mse))`
  Computes the PSNR formula.

### Why this is used

PSNR is used to measure image quality.

### Important note

* Higher PSNR → better image quality
* Lower PSNR → more distortion

---

## 26. Print performance comparison

```python
print(f"Custom Median Filter:    {comparison_results['custom'][1]:.4f} seconds")
print(f"Optimized Median Filter: {comparison_results['optimized'][1]:.4f} seconds")
print(f"OpenCV Median Blur:      {comparison_results['opencv'][1]:.4f} seconds")
print(f"Gaussian Blur:           {comparison_results['gaussian'][1]:.4f} seconds")
print(f"Mean Filter:             {comparison_results['mean'][1]:.4f} seconds")
```

### Explanation

This section prints the execution times of different filtering methods.

### What each part means

* `comparison_results['custom'][1]`
  Gets the runtime of the custom median filter.

* `comparison_results['optimized'][1]`
  Gets the runtime of the optimized median filter.

* `comparison_results['opencv'][1]`
  Gets the runtime of OpenCV’s built-in median filter.

* `comparison_results['gaussian'][1]`
  Gets the runtime of Gaussian blur.

* `comparison_results['mean'][1]`
  Gets the runtime of mean filtering.

### Why this is used

This helps compare the efficiency of the filters.

### Important note

Usually:

* custom manual code is slower
* optimized code is faster
* OpenCV built-in functions are the fastest

---

## 27. Final practical understanding

```python
# Mean Filter -> smoothing by averaging neighbors
# Gaussian Filter -> weighted smoothing using Gaussian distribution
# Sobel Filter -> edge detection using gradients
# Median Filter -> noise removal using neighborhood median
# Morphology -> shape-based processing on binary images
```

### Explanation

This summarizes the purpose of each major technique used in the practical.

### What each one means

* `Mean Filter`
  Smooths the image by replacing each pixel with the average of nearby pixels.

* `Gaussian Filter`
  Smooths the image using weighted values, giving more importance to the center.

* `Sobel Filter`
  Detects edges by finding changes in brightness.

* `Median Filter`
  Removes salt-and-pepper noise by replacing each pixel with the median value of nearby pixels.

* `Morphology`
  Processes shapes in binary images using operations such as erosion, dilation, opening, and closing.

---

## 28. Short exam conclusion

```python
# This practical demonstrates the use of different image filters
# and morphological operations in Digital Image Processing.
# It compares manual implementations with built-in OpenCV functions
# and shows how different methods affect smoothing, edge detection,
# noise reduction, and binary shape processing.
```

### Explanation

This is the main conclusion of the whole practical.

### Why this is important

In your practical exam, this helps you explain that the lab covers:

* image smoothing
* edge detection
* noise removal
* morphology
* manual vs built-in implementation
* performance comparison

---
