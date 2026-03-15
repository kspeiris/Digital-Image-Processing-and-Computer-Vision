

---

# Complete Full Explanation of the Code

---

## 1. Import required libraries

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt
import os
%matplotlib inline
```

### Explanation

This block imports the main libraries needed for image processing and visualization.

### What each part means

* `import cv2`
  Imports **OpenCV**, which is used for image processing operations such as reading images, filtering, padding, drawing shapes, and saving images.

* `import numpy as np`
  Imports **NumPy**, which is used for array and matrix operations.
  In image processing, images are stored as NumPy arrays.

* `import matplotlib.pyplot as plt`
  Imports **Matplotlib**, used to display images and plots inside the notebook.

* `import os`
  Imports the **os** module, which is generally used for file and directory operations.

* `%matplotlib inline`
  This is a Jupyter Notebook magic command.
  It tells the notebook to display plots directly below the code cell.

### Why this is used

These libraries are necessary because the practical performs:

* image creation
* image loading
* convolution filtering
* border padding
* image visualization

---

## 2. Create a checkerboard image

```python
def create_checkerboard(size=8, num_tiles=8):
    """Create a checkerboard pattern for edge effect visualization"""
    re = np.r_[num_tiles*[0,1]]
    ro = np.r_[num_tiles*[1,0]]
    board = np.vstack(num_tiles*(re, ro))
    board = cv2.resize(board.astype("uint8")*255, (size*20, size*20),
                      interpolation=cv2.INTER_NEAREST)
    return board
```

### Explanation

This function creates a **checkerboard image**.
A checkerboard is useful because it has strong black–white transitions, which makes border and filtering effects easy to see.

### What each part means

* `def create_checkerboard(size=8, num_tiles=8):`
  Defines a function with two parameters:

  * `size=8` → controls the output size scaling
  * `num_tiles=8` → number of black/white tile repetitions

* `re = np.r_[num_tiles*[0,1]]`
  Creates one row pattern like:

```python
[0, 1, 0, 1, 0, 1, ...]
```

* `ro = np.r_[num_tiles*[1,0]]`
  Creates the alternate row pattern like:

```python
[1, 0, 1, 0, 1, 0, ...]
```

* `board = np.vstack(num_tiles*(re, ro))`
  Stacks the rows vertically to form a full checkerboard pattern.

* `board.astype("uint8")*255`
  Converts values:

  * `0 → 0` (black)
  * `1 → 255` (white)

* `cv2.resize(..., interpolation=cv2.INTER_NEAREST)`
  Enlarges the checkerboard image without smoothing.
  `INTER_NEAREST` keeps sharp block edges.

* `return board`
  Returns the generated checkerboard image.

### Why this is used

The checkerboard helps clearly observe:

* convolution behavior
* smoothing effect
* border padding effect

---

## 3. Save generated checkerboard and create rectangle image

```python
checkerboard = create_checkerboard()
cv2.imwrite("checkerboard.png", checkerboard)

rectangle = np.ones((300, 300), dtype=np.uint8) * 255
rectangle[50:250, 80:220] = 0
cv2.imwrite("rectangle.png", rectangle)
```

### Explanation

This block creates two test images and saves them to disk.

### What each part means

#### Checkerboard image

* `checkerboard = create_checkerboard()`
  Calls the previously defined function to create the checkerboard.

* `cv2.imwrite("checkerboard.png", checkerboard)`
  Saves the checkerboard image as a PNG file.

#### Rectangle image

* `np.ones((300, 300), dtype=np.uint8) * 255`
  Creates a **300×300 white image**.

  * `np.ones(...)` creates all values as `1`
  * multiplying by `255` makes the whole image white

* `rectangle[50:250, 80:220] = 0`
  Sets a rectangular region to black.

This means:

* rows `50` to `249`
* columns `80` to `219`

become black.

* `cv2.imwrite("rectangle.png", rectangle)`
  Saves this image as `rectangle.png`.

### Why this is used

These images provide simple patterns for testing the effect of filtering and padding.

---

## 4. Print image information

```python
print("Test images created: checkerboard.png and rectangle.png")
print(f"Checkerboard shape: {checkerboard.shape}")
print(f"Rectangle shape: {rectangle.shape}")
```

### Explanation

This block confirms that the images were created and prints their sizes.

### What each part means

* `checkerboard.shape`
  Returns the dimensions of the checkerboard image.

* `rectangle.shape`
  Returns the dimensions of the rectangle image.

### Why this is used

It helps verify that the generated test images are correct before processing.

---

## 5. Load image for processing

```python
# gray = cv2.imread('checkerboard.png', cv2.IMREAD_GRAYSCALE)
gray = cv2.imread('rectangle.png', cv2.IMREAD_GRAYSCALE)
```

### Explanation

This block loads one of the saved images as a **grayscale image**.

### What each part means

* `cv2.imread(...)`
  Reads the image from file.

* `cv2.IMREAD_GRAYSCALE`
  Loads the image as a single-channel grayscale image.

### Why this is used

The practical focuses on convolution and padding.
Using grayscale makes the processing simpler because each pixel has only one intensity value.

---

## 6. Check whether image loaded correctly

```python
if gray is None:
    print("Error: Could not load image. Using generated checkerboard instead.")
    gray = create_checkerboard()
```

### Explanation

This block checks whether the image was successfully loaded.

### What each part means

* `if gray is None:`
  If the image file could not be read, OpenCV returns `None`.

* `gray = create_checkerboard()`
  If loading fails, the code uses a generated checkerboard instead.

### Why this is used

It prevents the notebook from crashing if the file is missing.

---

## 7. Define the averaging kernel

```python
kernel = np.ones((3, 3), np.float32) / 9
pad = 1
```

### Explanation

This block creates the **3×3 averaging filter** used for smoothing.

### What each part means

* `np.ones((3, 3), np.float32)`
  Creates a 3×3 matrix filled with `1`.

* `/ 9`
  Divides each value by `9`, so the total sum becomes `1`.

The kernel becomes:

```python
[[1/9, 1/9, 1/9],
 [1/9, 1/9, 1/9],
 [1/9, 1/9, 1/9]]
```

* `pad = 1`
  Since the kernel is `3×3`, one pixel of padding is needed around the border.

### Why this is used

This kernel replaces each pixel with the average of its 3×3 neighborhood, which smooths the image.

---

## 8. Print kernel information

```python
print(f"Image loaded successfully! Shape: {gray.shape}")
print(f"3×3 Averaging Kernel:\n{kernel}")
```

### Explanation

This block prints confirmation of the loaded image and displays the kernel values.

### Why this is used

It helps verify the exact filter being applied.

---

## 9. Define padding modes

```python
padding_modes = [
    ("BORDER_CONSTANT (Zero)",     cv2.BORDER_CONSTANT,     0),
    ("BORDER_REPLICATE (Edge)",    cv2.BORDER_REPLICATE,    None),
    ("BORDER_REFLECT",             cv2.BORDER_REFLECT,      None),
    ("BORDER_REFLECT_101",         cv2.BORDER_REFLECT_101,  None),
    ("BORDER_WRAP",                cv2.BORDER_WRAP,         None),
    ("No Padding (Valid)",         None,                    None),
]
```

### Explanation

This block defines the border-handling methods that will be compared.

### What each padding method means

* `BORDER_CONSTANT`
  Adds a constant value around the image border.
  Here the constant value is `0`, so black borders are added.

* `BORDER_REPLICATE`
  Repeats the edge pixels outward.

* `BORDER_REFLECT`
  Reflects the image at the border.

* `BORDER_REFLECT_101`
  Similar to reflect, but excludes the border pixel itself.

* `BORDER_WRAP`
  Wraps the image around like a circular/toroidal surface.

* `No Padding (Valid)`
  No artificial border is added.
  The output becomes smaller because border pixels cannot be fully filtered.

### Why this is used

Padding strongly affects the results near the borders of the image.

---

## 10. Print the padding modes

```python
print("Padding modes to be compared:")
for i, (name, border_type, value) in enumerate(padding_modes, 1):
    print(f"{i}. {name}")
```

### Explanation

This block prints all the padding methods in numbered form.

### Why this is used

It provides a clear list of the methods being tested.

---

## 11. Apply convolution using different padding methods

```python
results = []

for name, border_type, const_value in padding_modes:
    if border_type is None:
        full = cv2.filter2D(gray, -1, kernel, borderType=cv2.BORDER_CONSTANT)
        cropped = full[pad:-pad, pad:-pad]
        results.append((name, cropped))
        print(f"✓ {name}: Output shape {cropped.shape}")

    elif border_type == cv2.BORDER_CONSTANT:
        padded = cv2.copyMakeBorder(gray, pad, pad, pad, pad,
                                   borderType=cv2.BORDER_CONSTANT,
                                   value=const_value)
        filtered_full = cv2.filter2D(padded, -1, kernel,
                                    borderType=cv2.BORDER_CONSTANT)
        filtered = filtered_full[pad:-pad, pad:-pad]
        results.append((name, filtered))
        print(f"✓ {name} (value={const_value}): Output shape {filtered.shape}")

    elif border_type == cv2.BORDER_WRAP:
        padded = cv2.copyMakeBorder(gray, pad, pad, pad, pad,
                                   borderType=cv2.BORDER_WRAP)
        filtered_full = cv2.filter2D(padded, -1, kernel,
                                    borderType=cv2.BORDER_REPLICATE)
        filtered = filtered_full[pad:-pad, pad:-pad]
        results.append((name, filtered))
        print(f"✓ {name}: Output shape {filtered.shape}")

    else:
        filtered = cv2.filter2D(gray, -1, kernel, borderType=border_type)
        results.append((name, filtered))
        print(f"✓ {name}: Output shape {filtered.shape}")

print(f"\nAll {len(results)} padding methods processed successfully!")
```

### Explanation

This is the **main processing section** of the practical.

The code applies the same averaging filter to the image using different padding methods and stores the results.

---

### Part A — Create list to store output

```python
results = []
```

Stores all filtered output images for later visualization.

---

### Part B — Loop through each padding mode

```python
for name, border_type, const_value in padding_modes:
```

Processes each border-handling method one by one.

---

### Case 1 — No padding (valid convolution)

```python
if border_type is None:
    full = cv2.filter2D(gray, -1, kernel, borderType=cv2.BORDER_CONSTANT)
    cropped = full[pad:-pad, pad:-pad]
```

#### Explanation

For valid convolution, the code removes border pixels after filtering so the output only contains pixels that had a full neighborhood.

#### What this means

* `cv2.filter2D(...)` performs convolution.
* `full[pad:-pad, pad:-pad]` crops out the outer border.

Because `pad = 1`, the first and last rows and columns are removed.

#### Why this is used

This simulates filtering **without padding**.

---

### Case 2 — Constant padding

```python
elif border_type == cv2.BORDER_CONSTANT:
    padded = cv2.copyMakeBorder(gray, pad, pad, pad, pad,
                               borderType=cv2.BORDER_CONSTANT,
                               value=const_value)
```

#### Explanation

This manually adds a black border around the image.

* `cv2.copyMakeBorder(...)` adds padding.
* `value=const_value` sets the constant border value to `0`.

Then filtering is applied:

```python
filtered_full = cv2.filter2D(padded, -1, kernel,
                            borderType=cv2.BORDER_CONSTANT)
filtered = filtered_full[pad:-pad, pad:-pad]
```

The padded border is later removed so the output has the same size as the original.

#### Why this is used

OpenCV needs manual padding here to control the border value explicitly.

---

### Case 3 — Wrap padding

```python
elif border_type == cv2.BORDER_WRAP:
    padded = cv2.copyMakeBorder(gray, pad, pad, pad, pad,
                               borderType=cv2.BORDER_WRAP)
```

#### Explanation

Wrap padding copies pixels from the opposite side of the image.

Then:

```python
filtered_full = cv2.filter2D(padded, -1, kernel,
                            borderType=cv2.BORDER_REPLICATE)
```

This uses `BORDER_REPLICATE` inside `filter2D` because `filter2D` cannot directly use wrap in the same way for this setup.

Then the extra border is cropped.

#### Why this is used

It demonstrates circular border behavior.

---

### Case 4 — Other padding types

```python
else:
    filtered = cv2.filter2D(gray, -1, kernel, borderType=border_type)
```

#### Explanation

For reflect and replicate types, OpenCV can directly handle the padding inside `filter2D`.

---

### Final result storage

```python
results.append((name, filtered))
```

Each output image is stored along with its title.

---

## 12. Visualize the results

```python
plt.figure(figsize=(15, 12))

plt.subplot(3, 3, 1)
plt.imshow(gray, cmap='gray')
plt.title('Original Image', fontsize=14, fontweight='bold')
plt.xlabel(f'{gray.shape[1]}×{gray.shape[0]}', fontsize=10)
plt.axis('off')
```

### Explanation

This starts a figure and places the original image in the first subplot.

### What each part means

* `plt.figure(figsize=(15, 12))`
  Creates a large plotting window.

* `plt.subplot(3, 3, 1)`
  Divides the figure into a 3×3 grid and selects the first position.

* `plt.imshow(gray, cmap='gray')`
  Displays the image in grayscale.

* `plt.axis('off')`
  Hides axis lines and numbers.

---

## 13. Plot each filtered result

```python
for i, (title, img_out) in enumerate(results, start=2):
    if len(img_out.shape) == 3:
        img_out = cv2.cvtColor(img_out, cv2.COLOR_BGR2GRAY)

    plt.subplot(3, 3, i)
    plt.imshow(img_out, cmap='gray')
    plt.title(title, fontsize=12)
    plt.xlabel(f'{img_out.shape[1]}×{img_out.shape[0]}', fontsize=10)
    plt.axis('off')
```

### Explanation

This loop displays each filtered image in the remaining subplot positions.

### Important note

The code also checks:

```python
if len(img_out.shape) == 3:
```

If the image has 3 channels, it converts it to grayscale before display.

### Why this is used

This makes all outputs visually comparable.

---

## 14. Add overall title and show figure

```python
plt.suptitle('Convolution Results with Different Padding Methods',
            fontsize=16, fontweight='bold', y=1.02)
plt.tight_layout()
plt.show()
```

### Explanation

This adds a main title to the entire figure and displays it neatly.

---

## 15. Zoomed corner comparison

```python
zoom_y, zoom_x, size = 20, 20, 40
```

### Explanation

This defines a small rectangular region for close inspection.

* `zoom_y`, `zoom_x` → top-left position of the zoom area
* `size` → width and height of the zoomed region

---

## 16. Create zero-padded result for zoom comparison

```python
zero_padded = cv2.copyMakeBorder(gray, pad, pad, pad, pad,
                                borderType=cv2.BORDER_CONSTANT, value=0)
zero_full = cv2.filter2D(zero_padded, -1, kernel,
                        borderType=cv2.BORDER_CONSTANT)
zero_result = zero_full[pad:-pad, pad:-pad]
```

### Explanation

This manually creates the filtered image using zero padding so its border effect can be compared closely.

---

## 17. Create reflect result for zoom comparison

```python
reflect_result = cv2.filter2D(gray, -1, kernel,
                             borderType=cv2.BORDER_REFLECT_101)
```

### Explanation

This creates a filtered version using `BORDER_REFLECT_101`.

### Why this is used

Reflect padding usually produces more natural borders than zero padding.

---

## 18. Show corner comparison

```python
fig, axes = plt.subplots(1, 3, figsize=(12, 4))
```

### Explanation

Creates a row of 3 images for comparison.

---

### Original region

```python
axes[0].imshow(gray[zoom_y:zoom_y+size, zoom_x:zoom_x+size], cmap='gray')
axes[0].set_title('Original (Top-Left Corner)', fontsize=12)
axes[0].axis('off')
```

Shows the original image corner.

---

### Zero padding region

```python
axes[1].imshow(zero_result[zoom_y:zoom_y+size, zoom_x:zoom_x+size],
               cmap='gray', vmin=0, vmax=255)
axes[1].set_title('Zero Padding (BORDER_CONSTANT)', fontsize=12)
axes[1].axis('off')
```

Shows how zero padding affects the corner.

* `vmin=0, vmax=255` ensures consistent grayscale display.

---

### Reflect padding region

```python
axes[2].imshow(reflect_result[zoom_y:zoom_y+size, zoom_x:zoom_x+size],
               cmap='gray', vmin=0, vmax=255)
axes[2].set_title('Reflect 101 Padding (BORDER_REFLECT_101)', fontsize=12)
axes[2].axis('off')
```

Shows the same corner filtered with reflect padding.

---

### Show final zoom comparison

```python
plt.suptitle('Detailed Corner Comparison: Border Effects Magnified',
            fontsize=14, fontweight='bold', y=1.05)
plt.tight_layout()
plt.show()
```

### Explanation

Displays the border comparison figure with a main title.

---

## 19. Print summary text

```python
print("=" * 60)
print("PADDING METHODS SUMMARY")
print("=" * 60)

print("\n1. BORDER_CONSTANT (Zero Padding):")
print("   - Adds zeros around the image")
print("   - Creates dark borders in filtered output")
print("   - Useful when border values should not influence result")

print("\n2. BORDER_REPLICATE (Edge Padding):")
print("   - Replicates the edge pixels")
print("   - Preserves border intensity")
print("   - Most common for natural images")

print("\n3. BORDER_REFLECT / BORDER_REFLECT_101:")
print("   - Mirrors the image at borders")
print("   - 101 variant excludes the edge pixel")
print("   - Good for periodic-like signals")

print("\n4. BORDER_WRAP:")
print("   - Treats image as toroidal (wraps around)")
print("   - Useful for truly periodic patterns")
print("   - Rarely used in practice")

print("\n5. No Padding (Valid Convolution):")
print("   - Output is smaller than input")
print("   - Loses border information")
print("   - Used when border effects must be avoided")

print("\n" + "=" * 60)
print("Key Insight: Padding choice significantly affects")
print("border pixels but has minimal effect on interior pixels.")
print("=" * 60)
```

### Explanation

This block prints the theoretical meaning of each padding method.

### Why this is important

This is very useful for the viva or practical exam because you may be asked:

* What is zero padding?
* What is reflect padding?
* Why does padding matter?

---

# Second Version of the Same Practical in the Notebook

Your notebook also contains another version of the same idea using a directly created test image.

---

## 20. Create a custom test image

```python
def create_test_image(size=200):
    img = np.zeros((size, size), dtype=np.uint8)

    for i in range(size):
        img[:, i] = i * 255 // size

    img[50:150, 50:150] = 255
    cv2.circle(img, (150, 150), 30, 0, -1)

    return img
```

### Explanation

This function creates a synthetic grayscale test image.

### What happens here

* `np.zeros((size, size), dtype=np.uint8)`
  Creates a black image.

* `for i in range(size): img[:, i] = i * 255 // size`
  Creates a horizontal gradient from black to white.

* `img[50:150, 50:150] = 255`
  Adds a white rectangle.

* `cv2.circle(img, (150, 150), 30, 0, -1)`
  Draws a filled black circle.

### Why this is used

This image contains:

* smooth gradient
* sharp rectangle edges
* curved shape

So it is very useful for testing filtering and border effects.

---

## 21. Generate and display the custom image

```python
gray = create_test_image(200)
print(f"Test image created. Shape: {gray.shape}")
print(f"Image dtype: {gray.dtype}")
print(f"Min value: {gray.min()}, Max value: {gray.max()}")

plt.figure(figsize=(5, 5))
plt.imshow(gray, cmap='gray')
plt.title(f'Original Test Image\n{gray.shape[1]}×{gray.shape[0]}')
plt.axis('off')
plt.show()
```

### Explanation

This block:

* creates the test image
* prints image properties
* displays the original image

### Important values

* `shape` → size of image
* `dtype` → data type, usually `uint8`
* `min()` and `max()` → smallest and largest pixel values

---

## 22. Define kernel and padding modes again

```python
kernel = np.ones((3, 3), np.float32) / 9
pad = 1
```

### Explanation

Same as before: creates a 3×3 averaging kernel.

---

## 23. Process each padding type again

```python
results = []
descriptions = []

for name, border_type, const_value in padding_modes:
    print(f"\nProcessing: {name}")

    if border_type is None:
        filtered = cv2.filter2D(gray, -1, kernel, borderType=cv2.BORDER_CONSTANT)
        cropped = filtered[pad:-pad, pad:-pad]
        results.append(cropped)
        descriptions.append(f"{name}\n{cropped.shape[1]}×{cropped.shape[0]}")
```

### Explanation

This loop is similar to the earlier one, but this version stores:

* only the output image in `results`
* title plus size in `descriptions`

### Why this is useful

It makes plotting easier later.

---

## 24. Visualize all results in a grid

```python
fig, axes = plt.subplots(3, 3, figsize=(15, 12))
axes_flat = axes.flatten()
```

### Explanation

This creates a 3×3 grid of subplots and flattens it into a 1D list for easier indexing.

---

### Plot original image

```python
axes_flat[0].imshow(gray, cmap='gray')
axes_flat[0].set_title(f'Original Image\n{gray.shape[1]}×{gray.shape[0]}', fontsize=12, fontweight='bold')
axes_flat[0].axis('off')
```

### Plot filtered images

```python
for i, (img, title) in enumerate(zip(results, descriptions), 1):
    axes_flat[i].imshow(img, cmap='gray')
    axes_flat[i].set_title(title, fontsize=11)
    axes_flat[i].axis('off')
```

### Hide unused spaces

```python
for i in range(len(results) + 1, len(axes_flat)):
    axes_flat[i].axis('off')
```

### Final display

```python
plt.suptitle('Effects of Different Padding Methods in Convolution',
             fontsize=16, fontweight='bold', y=1.02)
plt.tight_layout()
plt.show()
```

### Explanation

This version produces a cleaner comparison layout.

---

## 25. Detailed border comparison using top-left corner

```python
fig, axes = plt.subplots(1, 4, figsize=(16, 4))

corner_size = 50
y_start, x_start = 0, 0
```

### Explanation

This prepares a 4-image comparison of the top-left corner.

---

### Original corner

```python
orig_corner = gray[y_start:y_start+corner_size, x_start:x_start+corner_size]
axes[0].imshow(orig_corner, cmap='gray')
axes[0].set_title('Original\n(Top-Left Corner)', fontsize=12)
axes[0].axis('off')
```

---

### Zero padding corner

```python
zero_pad_result = results[0]
zero_corner = zero_pad_result[y_start:y_start+corner_size, x_start:x_start+corner_size]
axes[1].imshow(zero_corner, cmap='gray')
axes[1].set_title('Zero Padding\n(Dark edges appear)', fontsize=12)
axes[1].axis('off')
```

### Explanation

This shows that zero padding can produce darker borders because outside pixels are assumed to be zero.

---

### Edge padding corner

```python
edge_pad_result = results[1]
edge_corner = edge_pad_result[y_start:y_start+corner_size, x_start:x_start+corner_size]
axes[2].imshow(edge_corner, cmap='gray')
axes[2].set_title('Edge Padding\n(Extends border values)', fontsize=12)
axes[2].axis('off')
```

### Explanation

This shows how edge padding repeats the nearest border pixels.

---

### Mirror 101 padding corner

```python
mirror_pad_result = results[3]
mirror_corner = mirror_pad_result[y_start:y_start+corner_size, x_start:x_start+corner_size]
axes[3].imshow(mirror_corner, cmap='gray')
axes[3].set_title('Mirror 101 Padding\n(Reflects without edge)', fontsize=12)
axes[3].axis('off')
```

### Explanation

This shows reflected border behavior.

---

### Final border comparison display

```python
plt.suptitle('Detailed Border Effects Comparison (Zoomed to Top-Left Corner)',
             fontsize=14, fontweight='bold', y=1.05)
plt.tight_layout()
plt.show()
```

---

# What This Entire Lab Is Teaching

Your notebook mainly teaches these practical concepts:

## 1. Convolution

Applying a kernel over an image to compute new pixel values.

## 2. Averaging filter

A 3×3 mean filter smooths the image by averaging neighboring pixels.

## 3. Padding

Padding is needed because border pixels do not have full neighbors.

## 4. Border effects

Different padding methods change the result mainly at the image borders.

## 5. Visualization

Plotting helps compare the output of different methods.

---

# Most Important Exam Points to Remember

## Averaging kernel

```python
kernel = np.ones((3, 3), np.float32) / 9
```

This is a **mean filter**.

---

## Why `pad = 1`?

Because a 3×3 kernel extends 1 pixel outward from the center.

---

## Why use `cv2.copyMakeBorder()`?

To manually add border pixels before filtering.

---

## Why crop after padding?

Because padding is only added temporarily for convolution, and then removed to restore the original image size.

---

## Difference between padding methods

* **Zero padding** → dark borders
* **Replicate padding** → repeats border pixels
* **Reflect padding** → mirrors image at edges
* **Wrap padding** → circular continuation
* **Valid convolution** → no border assumption, smaller output

---

# Simple Viva Answers

## What is convolution?

Convolution is the process of sliding a kernel over an image and computing new pixel values using neighboring pixels.

## What is a kernel?

A kernel is a small matrix used for filtering, smoothing, sharpening, or edge detection.

## Why do we need padding?

Padding is needed because border pixels do not have enough neighboring pixels for convolution.

## What is the effect of zero padding?

Zero padding can create darker edges in the result.

## What does averaging filter do?

It smooths the image by replacing each pixel with the average of nearby pixels.

---

