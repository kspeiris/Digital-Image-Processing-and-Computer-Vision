

---

# Digital Image Processing and Computer Vision

# Complete Practical Study Notes

---

# 1. What is Digital Image Processing

Digital Image Processing (DIP) is the technique of using computer algorithms to **analyze, manipulate, and improve digital images**.

A digital image is represented as a **matrix of pixel values**.

For a grayscale image:

[
Image(x,y) = Pixel\ intensity
]

Each pixel contains an intensity value between:

```
0 = black
255 = white
```

For color images (RGB):

```
R = Red channel
G = Green channel
B = Blue channel
```

Each channel has intensity values from **0–255**.

Source: Gonzalez & Woods, *Digital Image Processing*

---

# 2. Libraries Used in the Practical

### OpenCV (cv2)

OpenCV is a widely used **computer vision library** for:

* Image processing
* Video processing
* Object detection
* Feature extraction

Example functions:

```
cv2.imread()
cv2.cvtColor()
cv2.filter2D()
cv2.blur()
```

Source: OpenCV Documentation

---

### NumPy

NumPy is used for **numerical operations and matrix manipulation**.

Images in OpenCV are stored as **NumPy arrays**.

Example:

```
image.shape
image.dtype
```

---

### Matplotlib

Matplotlib is used for **visualizing images and graphs**.

Example:

```
plt.imshow()
plt.show()
```

---

# 3. Image Representation

Images are stored as matrices.

Example grayscale image:

```
[
  [120 125 130]
  [118 122 128]
  [115 120 125]
]
```

Each number represents **pixel intensity**.

---

# 4. Image Smoothing

Image smoothing is a technique used to **reduce noise and small variations in images**.

Noise may occur due to:

* camera sensors
* lighting conditions
* transmission errors

Smoothing helps improve image quality.

---

# 5. Averaging Filter (Mean Filter)

The practical uses a **3×3 averaging filter**.

The filter kernel:

[
\frac{1}{9}
\begin{bmatrix}
1 & 1 & 1 \
1 & 1 & 1 \
1 & 1 & 1
\end{bmatrix}
]

This replaces each pixel with the **average of its neighbors**.

Example:

```
Input pixels

120 122 125
121 124 126
118 120 122

Average = sum / 9
```

This smooths the image and removes noise.

Source: Gonzalez & Woods, *Digital Image Processing*

---

# 6. Convolution in Image Processing

Convolution is the process of applying a **filter kernel over an image**.

Steps:

1. Place kernel over pixel
2. Multiply kernel values with pixel values
3. Sum the results
4. Replace the center pixel

Example:

```
Output(x,y) = Σ Kernel(i,j) * Image(x+i,y+j)
```

Convolution is used for:

* smoothing
* sharpening
* edge detection
* feature extraction

Source: Computer Vision — Szeliski

---

# 7. Manual Averaging Filter Implementation

The notebook implements a **manual 3×3 averaging filter**.

Steps:

1. Pad the image
2. Slide a 3×3 window
3. Compute the average
4. Store result in output image

Example logic:

```
for each pixel:
   take 3x3 neighborhood
   calculate average
   assign to output
```

Purpose:

* educational understanding of filtering
* understanding convolution mechanics

---

# 8. Image Padding

Padding is used because convolution needs **neighbor pixels at borders**.

Without padding:

```
edges cannot be processed
```

Padding adds artificial pixels around the image.

Example:

Original image:

```
1 2 3
4 5 6
7 8 9
```

After padding:

```
1 1 2 3 3
1 1 2 3 3
4 4 5 6 6
7 7 8 9 9
7 7 8 9 9
```

---

# 9. Types of Padding

Different padding methods affect results.

---

## Constant Padding

Adds constant value (usually 0).

Example:

```
0 0 0
0 image 0
0 0 0
```

Effect:

Creates dark borders.

---

## Edge Padding

Repeats border pixels.

Example:

```
A A B C C
A A B C C
D D E F F
```

Used in the practical.

---

## Reflect Padding

Reflects the image values.

Example:

```
B A B C B
A image C
B C B A B
```

Produces smoother borders.

Source: OpenCV Documentation

---

# 10. OpenCV Filtering Functions

The notebook compares several filtering methods.

---

# cv2.filter2D()

General convolution function.

Example:

```
cv2.filter2D(image, -1, kernel)
```

Parameters:

```
image → input image
-1 → output depth same as input
kernel → convolution matrix
```

---

# cv2.blur()

Fast implementation of mean filtering.

Example:

```
cv2.blur(image, (3,3))
```

Advantages:

* faster than manual implementation
* optimized internally

Source: OpenCV Documentation

---

# 11. Adding Noise to Images

The practical artificially adds **random noise** to test smoothing.

Noise generation:

```
noise = np.random.randint(-30, 31)
```

Noise simulates real-world problems:

* sensor noise
* environmental interference
* transmission errors

---

# 12. Noise Reduction Using Smoothing

After adding noise:

```
noisy_image = original + noise
```

Then smoothing filter is applied:

```
cv2.blur(noisy_image, (3,3))
```

Result:

* noise reduced
* image becomes smoother

---

# 13. Comparing Filtering Methods

The notebook compares:

| Method        | Description                |
| ------------- | -------------------------- |
| Manual filter | Educational implementation |
| cv2.filter2D  | Generic convolution        |
| cv2.blur      | Optimized mean filter      |

Comparison metrics:

```
cv2.absdiff()
```

Used to calculate differences between results.

---

# 14. Visualization of Results

Images are displayed using **Matplotlib**.

Example:

```
plt.imshow(image, cmap='gray')
plt.title("Smoothed Image")
plt.axis("off")
```

Multiple images are displayed using:

```
plt.subplots()
```

This allows comparison between:

* original image
* noisy image
* filtered image

---

# 15. Practical Learning Outcomes

From this lab you should understand:

✔ Image smoothing
✔ Convolution filtering
✔ Kernel operations
✔ Noise reduction
✔ Image padding methods
✔ OpenCV filtering functions
✔ Manual vs optimized filtering
✔ Visualization of image processing results

---

# 16. Common Practical Exam Questions

### Question 1

What is image smoothing?

Answer:

Image smoothing is a technique used to **reduce noise and small variations in an image using filters such as mean filters or Gaussian filters**.

---

### Question 2

What is convolution in image processing?

Answer:

Convolution is the process of applying a **kernel matrix over an image to compute new pixel values based on neighboring pixels**.

---

### Question 3

What is a kernel?

Answer:

A kernel is a **small matrix used for filtering operations such as smoothing, sharpening, and edge detection**.

---

### Question 4

Why is padding used in convolution?

Answer:

Padding is used to **handle image borders so convolution can be applied to edge pixels**.

---

### Question 5

Difference between cv2.blur and cv2.filter2D?

| Function     | Description           |
| ------------ | --------------------- |
| cv2.filter2D | General convolution   |
| cv2.blur     | Optimized mean filter |

---

# 17. Real World Applications

Image smoothing is used in:

### Medical Imaging

* MRI image enhancement
* CT scan noise reduction

### Computer Vision

* object detection preprocessing
* face recognition

### Autonomous Vehicles

* noise reduction in camera frames

### Satellite Imaging

* removing sensor noise

---

# 18. Important Practical Commands

Load image

```
cv2.imread()
```

Convert to grayscale

```
cv2.cvtColor()
```

Apply filter

```
cv2.filter2D()
```

Mean filter

```
cv2.blur()
```

Show image

```
plt.imshow()
```

---

# 19. Key Practical Concepts to Remember

Most important topics for exam:

* Image smoothing
* Averaging filter
* Convolution
* Kernel
* Padding
* Noise reduction
* cv2.filter2D
* cv2.blur
* Image visualization

---

