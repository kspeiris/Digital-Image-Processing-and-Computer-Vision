

---

# Complete Full Explanation of the Code

This lab contains four main image processing techniques:

1. Mean Filter
2. Gaussian Filter
3. Sobel Filter
4. Median Filter

The image used in all sections is:

```python
girl.jpg
```

The image is loaded in **grayscale mode**, so each pixel has only one intensity value from **0 to 255**.

---

# 1. Mean Filter / Average Filter

---

## Code

```python
# Mean Filter/average
import cv2
import numpy as np

def mean_filter(image, kernel_size):
    # Pad the image to handle borders using zero-padding
    padded_image = cv2.copyMakeBorder(image,
                                      kernel_size//2,
                                      kernel_size//2,
                                      kernel_size//2,
                                      kernel_size//2,
                                      cv2.BORDER_CONSTANT)

    # Create an empty image to store the filtered result
    filtered_image = np.zeros_like(image)

    # Iterate over each pixel in the image
    for y in range(image.shape[0]):
        for x in range(image.shape[1]):
            # Extract the local window centered at the current pixel
            window = padded_image[y:y+kernel_size, x:x+kernel_size]
            # Calculate the mean value of the window
            mean_value = np.mean(window)
            # Assign the mean value to the corresponding pixel in the filtered image
            filtered_image[y, x] = mean_value

    return filtered_image

# Load the image in grayscale mode
image = cv2.imread('girl.jpg', cv2.IMREAD_GRAYSCALE)

# Define the kernel size for the mean filter
kernel_size = 5

# Apply the mean filter to the image
filtered_image = mean_filter(image, kernel_size)

# Display the original and filtered images
cv2.imshow('Original Image', image)
cv2.imshow('Mean Filtered Image', filtered_image)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

---

## Explanation

This section applies a **Mean Filter** to smooth the image.

A mean filter replaces each pixel with the **average value of its neighboring pixels**.

This is used for:

* reducing noise
* smoothing the image
* removing small intensity variations

But it can also **blur edges**.

---

## 1.1 Import libraries

```python
import cv2
import numpy as np
```

### Explanation

* `cv2` is the OpenCV library used for image processing.
* `numpy` is used for matrix and numerical operations.

Since images are stored as arrays of pixel values, NumPy is very important.

---

## 1.2 Define the mean filter function

```python
def mean_filter(image, kernel_size):
```

### Explanation

This defines a custom function named `mean_filter`.

### Parameters

* `image` → input grayscale image
* `kernel_size` → size of the filter window, such as 3 or 5

If `kernel_size = 5`, then a **5×5 neighborhood** is used around each pixel.

---

## 1.3 Add padding to the image

```python
padded_image = cv2.copyMakeBorder(image,
                                  kernel_size//2,
                                  kernel_size//2,
                                  kernel_size//2,
                                  kernel_size//2,
                                  cv2.BORDER_CONSTANT)
```

### Explanation

When the filter reaches the borders of the image, some neighboring pixels fall outside the image.

To solve that, padding is added.

### What each part means

* `kernel_size//2` gives the padding thickness
  For kernel size 5:

  ```python
  5 // 2 = 2
  ```

  So 2 rows/columns are added on each side.

* `cv2.BORDER_CONSTANT`
  means padding is done using constant values, usually **0**.

This is called **zero padding**.

---

## 1.4 Create an output image

```python
filtered_image = np.zeros_like(image)
```

### Explanation

This creates an empty image of the same size as the original image.

* `np.zeros_like(image)` creates an array filled with zeros
* same height
* same width
* same data type

This will store the filtered result.

---

## 1.5 Loop through every pixel

```python
for y in range(image.shape[0]):
    for x in range(image.shape[1]):
```

### Explanation

This goes through each pixel one by one.

* `image.shape[0]` = number of rows = image height
* `image.shape[1]` = number of columns = image width

So:

* outer loop → rows
* inner loop → columns

---

## 1.6 Extract local window

```python
window = padded_image[y:y+kernel_size, x:x+kernel_size]
```

### Explanation

This extracts a small region around the current pixel.

If `kernel_size = 5`, then this gives a **5×5 block**.

That local block is used to calculate the average.

---

## 1.7 Compute the mean

```python
mean_value = np.mean(window)
```

### Explanation

This calculates the average of all values inside the window.

For example, if the window is:

```python
[10, 20, 30
 40, 50, 60
 70, 80, 90]
```

The mean is:

[
\frac{10+20+30+40+50+60+70+80+90}{9}
]

So the center pixel is replaced by that average.

---

## 1.8 Store the new value

```python
filtered_image[y, x] = mean_value
```

### Explanation

The calculated mean value is assigned to the corresponding output pixel.

This creates the smoothed image.

---

## 1.9 Return filtered image

```python
return filtered_image
```

### Explanation

The function returns the processed image.

---

## 1.10 Load grayscale image

```python
image = cv2.imread('girl.jpg', cv2.IMREAD_GRAYSCALE)
```

### Explanation

This loads the image as grayscale.

* `'girl.jpg'` is the file name
* `cv2.IMREAD_GRAYSCALE` means load as a single-channel image

So each pixel has one intensity value.

---

## 1.11 Set kernel size

```python
kernel_size = 5
```

### Explanation

This sets the neighborhood size to **5×5**.

Larger kernels create more smoothing, but they also increase blur.

---

## 1.12 Apply the filter

```python
filtered_image = mean_filter(image, kernel_size)
```

### Explanation

This calls the custom function and stores the output in `filtered_image`.

---

## 1.13 Display results

```python
cv2.imshow('Original Image', image)
cv2.imshow('Mean Filtered Image', filtered_image)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### Explanation

* `cv2.imshow()` displays an image window
* `cv2.waitKey(0)` waits until a key is pressed
* `cv2.destroyAllWindows()` closes all image windows

---

## Mean Filter Summary

The mean filter:

* smooths the image
* reduces noise
* replaces each pixel with the average of nearby pixels
* may blur sharp edges

---

# 2. Gaussian Filter

---

## Code

```python
# Gaussian Filter
import cv2
import numpy as np

# Function to create a Gaussian kernel
def gaussian_kernel(size, sigma=1):
    """
    Create a 2D Gaussian kernel.
    size: kernel size (must be odd)
    sigma: standard deviation of the Gaussian
    """
    ax = np.arange(-size // 2 + 1., size // 2 + 1.)
    xx, yy = np.meshgrid(ax, ax)
    kernel = np.exp(-(xx**2 + yy**2) / (2. * sigma**2))
    kernel = kernel / np.sum(kernel)  # Normalize
    return kernel

# Function to convolve an image with a kernel
def convolve(image, kernel):
    """
    Perform convolution between an image and a kernel
    """
    # Padding the image
    pad = kernel.shape[0] // 2
    padded_image = cv2.copyMakeBorder(image, pad, pad, pad, pad, cv2.BORDER_CONSTANT)
    output = np.zeros_like(image, dtype=np.float32)

    # Convolution operation
    for y in range(image.shape[0]):
        for x in range(image.shape[1]):
            window = padded_image[y:y+kernel.shape[0], x:x+kernel.shape[1]]
            output[y, x] = np.sum(window * kernel)

    return output.astype(np.uint8)

# Load the image in grayscale
image = cv2.imread('girl.jpg', cv2.IMREAD_GRAYSCALE)

# Define kernel size and sigma
kernel_size = 5
sigma = 1.0

# Generate Gaussian kernel
gaussian_kernel_array = gaussian_kernel(kernel_size, sigma)

# Apply Gaussian filter to the image
filtered_image = convolve(image, gaussian_kernel_array)

# Display the original and filtered images
cv2.imshow('Original Image', image)
cv2.imshow('Gaussian Filtered Image', filtered_image)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

---

## Explanation

The Gaussian filter is another smoothing filter.

Unlike the mean filter, Gaussian filtering does **weighted averaging**.

That means:

* center pixels get higher importance
* far pixels get lower importance

This makes Gaussian filtering better than mean filtering for preserving structure.

---

## 2.1 Function to create Gaussian kernel

```python
def gaussian_kernel(size, sigma=1):
```

### Explanation

This function generates a Gaussian kernel matrix.

### Parameters

* `size` → kernel size, such as 3 or 5
* `sigma` → standard deviation controlling the spread of the Gaussian curve

---

## 2.2 Create coordinate axis

```python
ax = np.arange(-size // 2 + 1., size // 2 + 1.)
```

### Explanation

This creates coordinate values around the center.

For example, if `size = 5`, this produces values approximately like:

```python
[-2, -1, 0, 1, 2]
```

These are used to build the kernel grid.

---

## 2.3 Create 2D coordinate grid

```python
xx, yy = np.meshgrid(ax, ax)
```

### Explanation

This converts the 1D coordinate list into two 2D matrices:

* `xx` holds x-coordinates
* `yy` holds y-coordinates

This is needed to compute the Gaussian value at each position.

---

## 2.4 Apply Gaussian formula

```python
kernel = np.exp(-(xx**2 + yy**2) / (2. * sigma**2))
```

### Explanation

This calculates the Gaussian value for each position.

The mathematical idea is:

[
G(x,y)=e^{-\frac{x^2+y^2}{2\sigma^2}}
]

This gives higher values near the center and smaller values further away.

So nearby pixels influence the result more strongly.

---

## 2.5 Normalize the kernel

```python
kernel = kernel / np.sum(kernel)
```

### Explanation

This makes the sum of all kernel values equal to 1.

Why this is important:

* prevents the image from becoming too bright
* prevents the image from becoming too dark

Normalization keeps the output intensity balanced.

---

## 2.6 Return kernel

```python
return kernel
```

### Explanation

The function returns the Gaussian kernel matrix.

---

## 2.7 Define convolution function

```python
def convolve(image, kernel):
```

### Explanation

This function manually applies convolution between the image and the kernel.

Convolution means:

* place the kernel over a local image region
* multiply corresponding values
* add them together
* assign the sum to the output pixel

---

## 2.8 Compute padding amount

```python
pad = kernel.shape[0] // 2
```

### Explanation

This calculates how much border padding is needed.

If kernel is 5×5:

```python
pad = 5 // 2 = 2
```

---

## 2.9 Pad the image

```python
padded_image = cv2.copyMakeBorder(image, pad, pad, pad, pad, cv2.BORDER_CONSTANT)
```

### Explanation

This adds a border around the image so convolution can be performed at the edges.

Again, `cv2.BORDER_CONSTANT` means zero padding.

---

## 2.10 Create floating-point output array

```python
output = np.zeros_like(image, dtype=np.float32)
```

### Explanation

This creates an empty output image.

`dtype=np.float32` is used because convolution results may contain decimal values.

If integer type were used immediately, decimal precision would be lost too early.

---

## 2.11 Loop through all pixels

```python
for y in range(image.shape[0]):
    for x in range(image.shape[1]):
```

### Explanation

This visits every pixel in the input image.

---

## 2.12 Extract matching window

```python
window = padded_image[y:y+kernel.shape[0], x:x+kernel.shape[1]]
```

### Explanation

This extracts a local image block the same size as the kernel.

If the kernel is 5×5, then the window is also 5×5.

---

## 2.13 Multiply and sum

```python
output[y, x] = np.sum(window * kernel)
```

### Explanation

This is the actual convolution step.

* `window * kernel` multiplies each pixel by the corresponding kernel weight
* `np.sum(...)` adds all products together

That final value becomes the new pixel value.

---

## 2.14 Convert to unsigned integer

```python
return output.astype(np.uint8)
```

### Explanation

Images are usually displayed as `uint8` values from 0 to 255.

So the floating-point result is converted back to standard image format.

---

## 2.15 Load image

```python
image = cv2.imread('girl.jpg', cv2.IMREAD_GRAYSCALE)
```

### Explanation

Loads the grayscale image.

---

## 2.16 Define parameters

```python
kernel_size = 5
sigma = 1.0
```

### Explanation

* `kernel_size = 5` means use a 5×5 Gaussian kernel
* `sigma = 1.0` controls how spread out the Gaussian weights are

Higher sigma means stronger blur.

---

## 2.17 Generate kernel

```python
gaussian_kernel_array = gaussian_kernel(kernel_size, sigma)
```

### Explanation

This creates the Gaussian kernel matrix.

---

## 2.18 Apply filter

```python
filtered_image = convolve(image, gaussian_kernel_array)
```

### Explanation

This smooths the image using Gaussian convolution.

---

## 2.19 Display results

```python
cv2.imshow('Original Image', image)
cv2.imshow('Gaussian Filtered Image', filtered_image)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### Explanation

Shows original and Gaussian-smoothed images.

---

## Gaussian Filter Summary

Gaussian filtering:

* reduces noise
* smooths image
* uses weighted average
* keeps center pixels more important
* usually gives better smoothing than mean filter

---

# 3. Sobel Filter

---

## Code

```python
# Sobel filter
import cv2
import numpy as np

# Load the image in grayscale
image_path = 'girl.jpg'  # Change to your image path
image = cv2.imread(image_path, cv2.IMREAD_GRAYSCALE)

# Define Sobel kernels (3x3)
sobel_x_kernel = np.array([[-1, 0, 1],
                           [-2, 0, 2],
                           [-1, 0, 1]], dtype=np.float32)

sobel_y_kernel = np.array([[-1, -2, -1],
                           [ 0,  0,  0],
                           [ 1,  2,  1]], dtype=np.float32)

# Apply convolution with Sobel kernels
sobel_x = cv2.filter2D(image, cv2.CV_64F, sobel_x_kernel)
sobel_y = cv2.filter2D(image, cv2.CV_64F, sobel_y_kernel)

# Calculate gradient magnitude
gradient_magnitude = np.sqrt(sobel_x**2 + sobel_y**2)

# Normalize gradient magnitude to the range [0, 255]
normalized_gradient = cv2.normalize(gradient_magnitude, None, 0, 255, cv2.NORM_MINMAX)
normalized_gradient = normalized_gradient.astype(np.uint8)

# Display the images
cv2.imshow('Original Image', image)
cv2.imshow('Sobel Edge Detection', normalized_gradient)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

---

## Explanation

The Sobel filter is used for **edge detection**.

Edges are places where pixel intensity changes sharply.

Examples:

* object boundaries
* face outlines
* shape borders
* texture transitions

The Sobel method calculates the gradient in:

* x-direction
* y-direction

Then combines them.

---

## 3.1 Load image

```python
image_path = 'girl.jpg'
image = cv2.imread(image_path, cv2.IMREAD_GRAYSCALE)
```

### Explanation

Loads the image in grayscale so edge detection is easier and faster.

Edge detection usually works on intensity values rather than color.

---

## 3.2 Define Sobel X kernel

```python
sobel_x_kernel = np.array([[-1, 0, 1],
                           [-2, 0, 2],
                           [-1, 0, 1]], dtype=np.float32)
```

### Explanation

This kernel detects **horizontal intensity change**, which highlights **vertical edges**.

Why?

Because it compares left-side pixels with right-side pixels.

If there is a strong change from left to right, the output becomes large.

---

## 3.3 Define Sobel Y kernel

```python
sobel_y_kernel = np.array([[-1, -2, -1],
                           [ 0,  0,  0],
                           [ 1,  2,  1]], dtype=np.float32)
```

### Explanation

This kernel detects **vertical intensity change**, which highlights **horizontal edges**.

It compares top-side pixels with bottom-side pixels.

---

## 3.4 Apply convolution

```python
sobel_x = cv2.filter2D(image, cv2.CV_64F, sobel_x_kernel)
sobel_y = cv2.filter2D(image, cv2.CV_64F, sobel_y_kernel)
```

### Explanation

`cv2.filter2D()` applies a kernel to the image.

### Parameters

* `image` → input image
* `cv2.CV_64F` → output data type is 64-bit float
* kernel → Sobel kernel

Why use `cv2.CV_64F`?

Because gradient values can become:

* negative
* larger than 255

So normal `uint8` is not enough.

---

## 3.5 Compute gradient magnitude

```python
gradient_magnitude = np.sqrt(sobel_x**2 + sobel_y**2)
```

### Explanation

This combines the horizontal and vertical gradients into one edge-strength image.

Formula:

[
G = \sqrt{G_x^2 + G_y^2}
]

Where:

* `Gx` = edge response in x direction
* `Gy` = edge response in y direction

If both are strong, the final edge is strong.

---

## 3.6 Normalize result

```python
normalized_gradient = cv2.normalize(gradient_magnitude, None, 0, 255, cv2.NORM_MINMAX)
```

### Explanation

The gradient magnitude may contain values outside the display range.

So this rescales the values into the range **0 to 255**.

### Parameters

* `gradient_magnitude` → input array
* `None` → output array created automatically
* `0, 255` → new minimum and maximum values
* `cv2.NORM_MINMAX` → normalize using min-max scaling

---

## 3.7 Convert to displayable image type

```python
normalized_gradient = normalized_gradient.astype(np.uint8)
```

### Explanation

Converts the normalized result into standard image type.

---

## 3.8 Display images

```python
cv2.imshow('Original Image', image)
cv2.imshow('Sobel Edge Detection', normalized_gradient)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### Explanation

Shows the original image and the detected edges.

---

## Sobel Filter Summary

Sobel filtering:

* detects edges
* finds intensity changes
* uses x and y derivative kernels
* is useful in object detection and feature extraction

---

# 4. Median Filter

---

## Code

```python
# Median Filter
import cv2
import numpy as np

def custom_median_filter(image, kernel_size):
    """
    Apply median filter to an image
    """
    height, width = image.shape
    filtered_image = np.zeros_like(image)
    kernel_half = kernel_size // 2

    # Pad the image
    padded_image = cv2.copyMakeBorder(image,
                                      kernel_half, kernel_half,
                                      kernel_half, kernel_half,
                                      cv2.BORDER_CONSTANT)

    for i in range(height):
        for j in range(width):
            # Extract the kernel window
            window = []
            for ki in range(-kernel_half, kernel_half + 1):
                for kj in range(-kernel_half, kernel_half + 1):
                    window.append(padded_image[i + ki + kernel_half, j + kj + kernel_half])

            # Sort and get median
            window.sort()
            median_value = window[len(window) // 2]
            filtered_image[i, j] = median_value

    return filtered_image

# Load the image
image = cv2.imread('girl.jpg', cv2.IMREAD_GRAYSCALE)

# Apply median filter
kernel_size = 5
filtered_image = custom_median_filter(image, kernel_size)

# Display results
cv2.imshow('Original Image', image)
cv2.imshow('Median Filtered Image', filtered_image)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

---

## Explanation

The median filter is used to remove noise, especially:

* salt-and-pepper noise
* impulse noise

Unlike mean filtering, it does **not average** values.

Instead, it takes the **middle value after sorting** the neighborhood pixels.

This helps preserve edges better.

---

## 4.1 Define median filter function

```python
def custom_median_filter(image, kernel_size):
```

### Explanation

Defines a custom function to apply median filtering.

---

## 4.2 Get image size

```python
height, width = image.shape
```

### Explanation

For a grayscale image:

* `height` = number of rows
* `width` = number of columns

---

## 4.3 Create output image

```python
filtered_image = np.zeros_like(image)
```

### Explanation

Creates an empty output image to store results.

---

## 4.4 Compute half kernel size

```python
kernel_half = kernel_size // 2
```

### Explanation

If kernel size is 5:

```python
kernel_half = 2
```

This tells how far the window extends from the center pixel.

---

## 4.5 Pad the image

```python
padded_image = cv2.copyMakeBorder(image,
                                  kernel_half, kernel_half,
                                  kernel_half, kernel_half,
                                  cv2.BORDER_CONSTANT)
```

### Explanation

Adds zero padding around the image borders.

This is needed because the kernel must still work near edges.

---

## 4.6 Visit every pixel

```python
for i in range(height):
    for j in range(width):
```

### Explanation

Loops over all pixels in the image.

---

## 4.7 Create a list for neighborhood values

```python
window = []
```

### Explanation

This list will store all pixel values inside the current filter window.

---

## 4.8 Collect neighborhood pixels

```python
for ki in range(-kernel_half, kernel_half + 1):
    for kj in range(-kernel_half, kernel_half + 1):
        window.append(padded_image[i + ki + kernel_half, j + kj + kernel_half])
```

### Explanation

This extracts every pixel in the neighborhood.

For a 5×5 kernel, it collects 25 values.

These values are stored in `window`.

---

## 4.9 Sort the values

```python
window.sort()
```

### Explanation

Sorts all neighborhood values from smallest to largest.

Example:

```python
[7, 20, 3, 100, 10]
```

becomes:

```python
[3, 7, 10, 20, 100]
```

---

## 4.10 Select middle value

```python
median_value = window[len(window) // 2]
```

### Explanation

This picks the middle value in the sorted list.

That value is the **median**.

This is why extreme noise values do not affect the result as much as in mean filtering.

---

## 4.11 Assign median to output

```python
filtered_image[i, j] = median_value
```

### Explanation

The current output pixel is replaced by the median of its neighborhood.

---

## 4.12 Return result

```python
return filtered_image
```

### Explanation

Returns the filtered image.

---

## 4.13 Load image and apply filter

```python
image = cv2.imread('girl.jpg', cv2.IMREAD_GRAYSCALE)

kernel_size = 5
filtered_image = custom_median_filter(image, kernel_size)
```

### Explanation

Loads the grayscale image and applies the custom median filter using a 5×5 neighborhood.

---

## 4.14 Display images

```python
cv2.imshow('Original Image', image)
cv2.imshow('Median Filtered Image', filtered_image)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### Explanation

Displays the original and median-filtered images.

---

## Median Filter Summary

Median filtering:

* removes salt-and-pepper noise
* preserves edges better than mean filter
* uses the median value instead of average
* is a non-linear filter

---

# 5. Important Overall Concepts in This Lab

---

## 5.1 Grayscale Image

All sections use:

```python
cv2.IMREAD_GRAYSCALE
```

This means the image is loaded with one channel only.

Why grayscale is used:

* simpler processing
* faster computation
* many filters work directly on intensity values

---

## 5.2 Kernel

A kernel is a small matrix used to process an image.

Examples:

* Mean kernel
* Gaussian kernel
* Sobel kernel

The kernel moves over the image and changes pixel values.

---

## 5.3 Padding

Padding is used because border pixels do not have enough neighbors.

In this code, the padding type is:

```python
cv2.BORDER_CONSTANT
```

This means zero padding.

---

## 5.4 Smoothing vs Edge Detection

### Smoothing filters

Used to reduce noise:

* Mean filter
* Gaussian filter
* Median filter

### Edge detection filter

Used to highlight boundaries:

* Sobel filter

---

# 6. Difference Between the Four Filters

| Filter          | Main Purpose   | How it Works                           | Effect on Edges      |
| --------------- | -------------- | -------------------------------------- | -------------------- |
| Mean Filter     | Smoothing      | Takes average of neighbors             | Blurs edges          |
| Gaussian Filter | Smoothing      | Weighted average using Gaussian values | Better than mean     |
| Sobel Filter    | Edge detection | Finds gradients in x and y directions  | Highlights edges     |
| Median Filter   | Noise removal  | Takes median of neighbors              | Preserves edges well |

---

# 7. Very Important Practical Exam Points

You should remember these:

### Mean Filter

* linear filter
* uses average
* smooths image
* blurs edges

### Gaussian Filter

* linear filter
* weighted smoothing
* based on Gaussian distribution
* better than mean filter for smoothing

### Sobel Filter

* detects edges
* uses x-direction and y-direction kernels
* calculates gradient magnitude

### Median Filter

* non-linear filter
* uses median
* very good for salt-and-pepper noise
* preserves edges better

---

# 8. Possible Viva Questions with Answers

### What is the purpose of a mean filter?

To smooth the image by replacing each pixel with the average of neighboring pixels.

### Why is Gaussian better than mean filter?

Because Gaussian gives higher weight to closer pixels and lower weight to farther pixels.

### What does Sobel detect?

It detects edges by calculating intensity gradients.

### Why are there two Sobel kernels?

One detects horizontal change and the other detects vertical change.

### What type of noise is best removed by median filter?

Salt-and-pepper noise.

### Why do we use padding?

To handle border pixels during filtering.

### Why is grayscale used?

Because processing is simpler and faster.

---

# 9. Final Lab Summary

This lab teaches four very important image processing operations:

* **Mean Filter** for simple smoothing
* **Gaussian Filter** for weighted smoothing
* **Sobel Filter** for edge detection
* **Median Filter** for noise removal

These are basic but very important topics in:

* Digital Image Processing
* Computer Vision
* Image enhancement
* Preprocessing before AI or vision tasks

---


