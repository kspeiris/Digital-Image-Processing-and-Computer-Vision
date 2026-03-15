# Digital Image Processing and Computer Vision

> Practical lab repository for learning image processing and introductory computer vision with Python and OpenCV.

This repository is a practical lab collection for core Digital Image Processing (DIP) and introductory Computer Vision concepts using Python, OpenCV, NumPy, and Matplotlib. It contains step-by-step lab materials, runnable notebooks, full code listings, explanatory notes, and summary documents built around a single sample image, `Girl.jpg`.

The project is organized as a sequence of hands-on exercises covering image loading, color-space handling, histogram analysis, histogram equalization, smoothing, convolution padding, spatial filtering, edge detection, thresholding, and morphology.

## :star2: Project Highlights

- Structured lab progression from basic image loading to morphology
- Notebook, full-code, and explanation files for each lab
- Practical focus using OpenCV, NumPy, and Matplotlib
- Suitable for coursework, self-study, and viva/practical exam revision

## :blue_book: Overview

The material in this repository focuses on:

- Reading and displaying images with OpenCV and Matplotlib
- Understanding BGR vs RGB image formats
- Splitting and analyzing RGB channels
- Computing and interpreting histograms
- Grayscale conversion and cumulative distribution functions
- Working with HSV and YUV color spaces
- Exposure and brightness analysis
- Histogram equalization in grayscale and color images
- Image smoothing using averaging and Gaussian filters
- Convolution and border padding behavior
- Edge detection using the Sobel operator
- Noise removal using median filtering
- Binary conversion using Otsu thresholding
- Morphological operations such as erosion, dilation, opening, closing, gradient, top-hat, and black-hat

## :file_folder: Repository Structure

```text
.
|-- Girl.jpg
|-- Summary.md
|-- Final Summary Explain Code.md
|-- Final Summary Explain Note.md
|-- Final Summary Running Code.ipynb
|-- Lab Report 01/
|   |-- Full Code.md
|   |-- Explain Code.md
|   |-- Explain Note.md
|   `-- Running Code.ipynb
|-- Lab Report 02/
|-- Lab Report 02-2/
|-- Lab Report 03/
|-- Lab Report 04/
|-- Lab Report 05/
`-- Lab Report 05-2/
```

Each lab folder generally contains:

- `Running Code.ipynb`: executable notebook version
- `Full Code.md`: complete code with comments
- `Explain Code.md`: detailed line-by-line explanation
- `Explain Note.md`: shorter conceptual study note

Top-level summary files provide consolidated revision material across all labs.

## :test_tube: Lab Guide

### :framed_picture: Lab 01: RGB Image Loading, Channel Splitting, and Histogram Analysis

Topics covered:

- Loading an image safely with OpenCV
- Converting BGR to RGB for correct plotting
- Displaying the original image
- Splitting red, green, and blue channels
- Plotting RGB histograms
- Calculating mean, standard deviation, and peak intensity for each channel

Core idea:
This lab establishes how a digital color image is represented and how channel-wise intensity distributions can be analyzed numerically and visually.

### :bar_chart: Lab 02: Advanced Histogram Analysis and Color Space Exploration

Topics covered:

- Channel-only visualizations
- Overlayed RGB histograms
- Grayscale histogram analysis
- Cumulative Distribution Function (CDF)
- HSV channel histograms
- YUV brightness-channel comparison
- Exposure simulation
- Brightness simulation

Core idea:
This lab extends histogram interpretation beyond RGB and introduces brightness-oriented analysis using grayscale, HSV, and YUV spaces.

### :microscope: Lab 02-2: Alternative / Extended Histogram Variant

This appears to be an additional or alternative version of the Lab 02 material, kept as a separate working variant.

### :sparkles: Lab 03: Histogram Equalization

Topics covered:

- Grayscale histogram equalization
- Color equalization through YUV luminance equalization
- Before/after image comparison
- Before/after histogram comparison

Core idea:
Contrast can be improved by redistributing intensity values, and equalizing only luminance is usually better than equalizing RGB channels independently.

### :soap: Lab 04: Image Smoothing and Padding Methods

Topics covered:

- Manual 3x3 averaging filter implementation
- Smoothing with `cv2.filter2D()`
- Smoothing with `cv2.blur()`
- Noise addition and denoising
- Difference image comparison
- Padding method comparison: zero, replicate, reflect, reflect_101, wrap, and valid convolution

Core idea:
This lab connects convolution theory to implementation details, especially border handling and its effect on output quality.

### :brain: Lab 05: Mean, Gaussian, Sobel, and Median Filters

Topics covered:

- Mean filtering
- Gaussian kernel generation and convolution
- Sobel edge detection
- Median filtering

Core idea:
Different spatial filters solve different problems: smoothing, weighted smoothing, edge detection, and impulsive-noise removal.

### :mag: Lab 05-2: Filters and Morphological Operations

Topics covered:

- Extended custom filter implementations
- Manual and OpenCV Sobel comparison
- Noise addition and PSNR evaluation
- Otsu thresholding
- Erosion and dilation
- Opening and closing with different kernel sizes
- Morphological gradient, top-hat, and black-hat
- Synthetic binary-image experiments

Core idea:
This is the most advanced lab in the repository and expands the earlier filtering work into binary image processing and morphology.

### :memo: Final Summary: Consolidated Notebook and Notes

The top-level summary files combine material from the lab set into revision-oriented documentation:

- `Summary.md`: concise cross-lab summary with cleaned-up code and explanations
- `Final Summary Explain Code.md`: detailed explanation across labs
- `Final Summary Explain Note.md`: theory and exam-style notes
- `Final Summary Running Code.ipynb`: consolidated runnable notebook

## :hammer_and_wrench: Tech Stack

- Python 3
- OpenCV (`cv2`)
- NumPy
- Matplotlib
- Jupyter Notebook

Some notebook variants also reference:

- SciPy
- MediaPipe
- Google Colab helpers such as `google.colab.files` and `cv2_imshow`

## :gear: Setup

### 1. Create and activate a virtual environment

Windows PowerShell:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

### 2. Install required packages

```powershell
pip install opencv-python numpy matplotlib jupyter
```

Optional packages for some notebook variants:

```powershell
pip install scipy mediapipe
```

## :arrow_forward: Run the Project

### Option 1: Run the notebooks

Best for learning and step-by-step study.

Start Jupyter:

```powershell
jupyter notebook
```

Then open any of the following files:

- `Lab Report 01/Running Code.ipynb`
- `Lab Report 02/Running Code.ipynb`
- `Lab Report 02-2/Running Code.ipynb`
- `Lab Report 03/Running Code.ipynb`
- `Lab Report 04/Running Code.ipynb`
- `Lab Report 05/Running Code.ipynb`
- `Lab Report 05-2/Running Code.ipynb`
- `Final Summary Running Code.ipynb`

### Option 2: Use the markdown code files

Best for quick reference or converting into `.py` scripts.

The `Full Code.md` files contain the full implementation for each practical. These can be copied into a Python file or notebook cell and executed directly.

## :framed_picture: Input Data

Sample image used across labs:

- `Girl.jpg`

Place this file in the project root when running code that expects relative paths. Most of the cleaned notebook versions are already written to search for it in the repository root.

## :dart: Learning Outcomes

After working through this repository, a student should be able to:

- Explain how OpenCV stores color images
- Convert between BGR, RGB, grayscale, HSV, and YUV
- Compute and interpret image histograms
- Describe the difference between exposure and brightness
- Apply histogram equalization properly
- Implement simple convolution-based filters manually
- Compare built-in OpenCV filters with custom implementations
- Detect edges using Sobel filtering
- Use median filtering for salt-and-pepper noise
- Perform thresholding and basic morphology on binary images

## :warning: Important Notes

This repository contains both original lab material and cleaned-up versions. A few inconsistencies exist across files:

- Some files refer to `girl.jpg` while the repository image is `Girl.jpg`
- Some explanation files mention `Girl.png` even though the project file is `Girl.jpg`
- A few notebooks include Google Colab-specific imports or commands
- `mediapipe` is installed in some notes but is not central to the visible lab workflows

If you run code locally on Windows, use the cleaned versions or update image paths to `Girl.jpg`.

## :books: Suggested Reading Order

For study:

1. Read `Explain Note.md`
2. Review `Explain Code.md`
3. Run `Running Code.ipynb`
4. Use `Summary.md` and the final summary files for revision

For implementation practice:

1. Start with Lab 01
2. Proceed sequentially through Lab 05-2
3. Finish with the final summary notebook

## :busts_in_silhouette: Who This Repository Is For

This project is useful for:

- Students studying Digital Image Processing
- Students preparing for a practical or viva exam
- Beginners learning OpenCV fundamentals
- Anyone who wants a compact, example-driven DIP study repository

## :page_facing_up: License

No license file is currently included in this repository. If you plan to distribute or reuse the material publicly, add an explicit license.
