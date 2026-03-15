# Complete Explained Code: Digital Image Processing Lab 01

```python
# ============================================
# LAB 01: IMAGE MANIPULATION AND COLOR SPACE ANALYSIS
# Digital Image Processing and Computer Vision
# ============================================

# ============================================
# SECTION 1: INSTALLING REQUIRED LIBRARIES
# ============================================

# The '!' symbol allows running terminal commands from Jupyter notebook
# '--quiet' suppresses installation output to keep the notebook clean
!pip install mediapipe opencv-python --quiet

"""
EXPLANATION:
- mediapipe: Google's machine learning framework for computer vision
- opencv-python: The main OpenCV library for image processing
- --quiet: Prevents verbose output during installation
"""

# ============================================
# SECTION 2: IMPORTING LIBRARIES
# ============================================

# Import OpenCV for image processing functions
import cv2

# Import NumPy for array operations (images are stored as arrays)
import numpy as np

# Import pyplot from matplotlib for displaying images and plots
import matplotlib.pyplot as plt

# %matplotlib inline ensures plots appear directly in the notebook
# without needing to call plt.show() explicitly (though we still use it)
%matplotlib inline

# Set default figure size for all plots (width=10 inches, height=6 inches)
plt.rcParams['figure.figsize'] = (10,6)

# Set default colormap to grayscale for single-channel images
plt.rcParams['image.cmap'] = 'gray'

"""
EXPLANATION OF LIBRARIES:
1. cv2 (OpenCV):
   - Provides over 2500 optimized algorithms for computer vision
   - Functions for reading/writing images, color conversion, filtering

2. numpy:
   - Images are 3D arrays (height × width × channels)
   - NumPy enables efficient mathematical operations on these arrays

3. matplotlib.pyplot:
   - MATLAB-like plotting framework
   - Used for displaying images and plotting histograms

4. plt.rcParams:
   - Sets global configuration parameters
   - figure.figsize: Default figure size (width, height) in inches
   - image.cmap: Default colormap for single-channel images
"""

# ============================================
# SECTION 3: LOADING AND DISPLAYING IMAGE (BGR FORMAT)
# ============================================

# Read the image file 'Girl.jpg' using OpenCV
# OpenCV reads images in BGR (Blue-Green-Red) format by default
img = cv2.imread('Girl.jpg')

# ALWAYS check if image loaded successfully
# Returns None if file not found or corrupted
if img is None:
    raise FileNotFoundError("❌ Could not find 'Girl.jpg'. Please upload it to the Colab working directory.")

"""
EXPLANATION OF cv2.imread():
Parameters:
- filename: 'Girl.jpg' (string) - path to image file
Returns:
- 3D numpy array of shape (height, width, 3)
- Each pixel values range from 0-255 for each channel
- Returns None if file cannot be read

Important: OpenCV's default color format is BGR, not RGB!
This is a historical artifact from early OpenCV development.
"""

# Create a new figure with custom size (12 inches wide, 4 inches tall)
plt.figure(figsize=(12,4))

# Display the image using matplotlib
# NOTE: Colors will appear INCORRECT because:
# - OpenCV loaded as BGR
# - Matplotlib displays as RGB
# - Red and Blue channels are swapped
plt.imshow(img)

# Add title to the plot
plt.title('BGR Image (Direct OpenCV Load - Colors Incorrect)')

# Display the plot
plt.show()

"""
EXPECTED RESULT:
The image will appear with incorrect colors because the red and blue channels are swapped.
For example:
- Sky might appear reddish instead of blue
- Skin tones will look unnatural
"""

# ============================================
# SECTION 4: CONVERTING BGR TO RGB
# ============================================

# Convert the image from BGR to RGB color space
# This fixes the color display issue
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

"""
EXPLANATION OF cv2.cvtColor():
Parameters:
- src: img (source image array)
- code: cv2.COLOR_BGR2RGB (conversion code)

What happens:
- Channel 0 (Blue) ↔ Channel 2 (Red) are swapped
- Channel 1 (Green) remains the same
- Result: [B,G,R] → [R,G,B]

Other common conversion codes:
- cv2.COLOR_BGR2GRAY: Convert to grayscale
- cv2.COLOR_BGR2HSV: Convert to HSV color space
- cv2.COLOR_BGR2LAB: Convert to LAB color space
"""

# Display the correctly converted RGB image
plt.figure(figsize=(12,4))
plt.imshow(img_rgb)
plt.title('RGB Image (After Conversion - Colors Correct)')
plt.show()

"""
EXPECTED RESULT:
Now colors appear correctly because the image format matches
what matplotlib expects (RGB).
"""

# ============================================
# SECTION 5: DISPLAYING WITH DIFFERENT FIGURE SIZES
# ============================================

# Create a square figure (6x6 inches) for better viewing
plt.figure(figsize=(6,6))
plt.imshow(img_rgb)
plt.title('RGB Image (Square Format)')
plt.axis('off')  # Turn off axis labels and ticks for cleaner display
plt.show()

"""
EXPLANATION OF plt.axis('off'):
- Removes the x and y axis ticks and labels
- Gives a cleaner image-only display
- Useful for presentations or saving images
"""

# ============================================
# SECTION 6: HISTOGRAM ANALYSIS
# ============================================

# Define colors for each channel plot
# 'r' = red, 'g' = green, 'b' = blue
colors = ('r', 'g', 'b')

# Define labels for the legend
channel_names = ['Red', 'Green', 'Blue']

# Create a new figure for the histogram plot
plt.figure(figsize=(10,6))

# Loop through each color channel (0=Red, 1=Green, 2=Blue in RGB image)
for i, col in enumerate(colors):
    """
    Calculate histogram for channel i
    Parameters of cv2.calcHist():
    - [img_rgb]: List of images (must be in list format)
    - [i]: Channel index (0,1,2 for RGB)
    - None: No mask (use entire image)
    - [256]: Number of bins (0-255 gives 256 bins)
    - [0,256]: Range of pixel values
    """
    hist = cv2.calcHist([img_rgb], [i], None, [256], [0,256])
    
    # Plot the histogram with appropriate color
    plt.plot(hist, color=col, label=f'{channel_names[i]} Channel')
    
    # Set x-axis limits to show full 0-255 range
    plt.xlim([0,256])

"""
EXPLANATION OF HISTOGRAM:
- X-axis: Pixel intensity (0 = black, 255 = white)
- Y-axis: Frequency (number of pixels at each intensity)
- Each channel plotted separately with its color
- Shows distribution of red, green, and blue intensities
"""

# Add title to the histogram plot
plt.title('RGB Channel Histograms - Girl.png')

# Label the axes
plt.xlabel('Pixel Intensity (0–255)')
plt.ylabel('Frequency')

# Add legend to identify each channel
plt.legend()

# Add light grid lines for better readability
# alpha=0.3 makes the grid semi-transparent
plt.grid(alpha=0.3)

# Display the histogram
plt.show()

"""
INTERPRETING THE HISTOGRAM:

1. CHANNEL DISTRIBUTION:
   - Each colored line shows distribution for that channel
   - Overlapping lines indicate color balance
   - Separated lines indicate color cast

2. EXPOSURE ANALYSIS:
   - Peaks in dark region (0-50): Underexposed/shadows
   - Peaks in mid region (100-150): Well-exposed
   - Peaks in bright region (200-255): Overexposed/highlights

3. CONTRAST ANALYSIS:
   - Wide distribution: High contrast
   - Narrow distribution: Low contrast (flat image)

4. COLOR BALANCE:
   - If all three histograms are similar: Good color balance
   - If one channel dominates: Color cast present
"""

# ============================================
# SECTION 7: COMPARATIVE DISPLAY
# ============================================

# Display BGR and RGB images side by side for comparison
plt.figure(figsize=(15,5))

# First subplot: BGR image (incorrect colors)
plt.subplot(1,2,1)  # 1 row, 2 columns, first plot
plt.imshow(img)
plt.title('BGR Image (Incorrect Colors)')
plt.axis('off')

# Second subplot: RGB image (correct colors)
plt.subplot(1,2,2)  # 1 row, 2 columns, second plot
plt.imshow(img_rgb)
plt.title('RGB Image (Correct Colors)')
plt.axis('off')

plt.tight_layout()  # Adjust spacing between subplots
plt.show()

"""
EXPLANATION OF plt.subplot(rows, cols, index):
- rows=1: One row of plots
- cols=2: Two columns of plots
- index: Which plot to use (1=left, 2=right)

plt.tight_layout():
- Automatically adjusts subplot parameters
- Prevents overlapping labels and titles
"""

# ============================================
# SECTION 8: SAVING PROCESSED IMAGES (Optional)
# ============================================

# Save the RGB converted image
# cv2.imwrite expects BGR format, so convert back
cv2.imwrite('Girl_RGB.jpg', cv2.cvtColor(img_rgb, cv2.COLOR_RGB2BGR))

"""
EXPLANATION OF cv2.imwrite():
Parameters:
- filename: 'Girl_RGB.jpg' (output file name)
- img: Image array to save

Note: cv2.imwrite() expects BGR format, so we convert RGB back to BGR
before saving to maintain correct colors in the saved file.
"""

# ============================================
# COMPLETE FUNCTIONAL SUMMARY
# ============================================

"""
LAB OBJECTIVES ACHIEVED:
✓ Install and import required libraries
✓ Load images using OpenCV
✓ Understand BGR vs RGB color spaces
✓ Convert between color spaces
✓ Display images with matplotlib
✓ Analyze color channels using histograms
✓ Interpret histogram data

KEY TAKEAWAYS:
1. OpenCV uses BGR, matplotlib uses RGB - always convert!
2. Images are 3D numpy arrays (height × width × channels)
3. Histograms reveal exposure, contrast, and color balance
4. Always check if images load successfully
5. Use plt.subplot() for side-by-side comparisons
"""
```

## Additional Helper Functions (Bonus)

```python
# ============================================
# BONUS: HELPER FUNCTIONS FOR REUSABILITY
# ============================================

def load_and_check_image(image_path):
    """
    Safely load an image and check for errors.
    
    Parameters:
        image_path (str): Path to the image file
        
    Returns:
        numpy.ndarray: Loaded image or None if failed
    """
    img = cv2.imread(image_path)
    if img is None:
        print(f"❌ Error: Could not load image from '{image_path}'")
        return None
    print(f"✅ Image loaded successfully: {image_path}")
    print(f"   Dimensions: {img.shape[1]} x {img.shape[0]} pixels")
    print(f"   Channels: {img.shape[2] if len(img.shape) > 2 else 1}")
    return img


def display_image_comparison(img_bgr, title1='BGR', title2='RGB'):
    """
    Display BGR and RGB versions side by side.
    
    Parameters:
        img_bgr (numpy.ndarray): BGR image
        title1 (str): Title for BGR image
        title2 (str): Title for RGB image
    """
    img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
    
    plt.figure(figsize=(15,5))
    
    plt.subplot(1,2,1)
    plt.imshow(img_bgr)
    plt.title(f'{title1} (Incorrect Colors)')
    plt.axis('off')
    
    plt.subplot(1,2,2)
    plt.imshow(img_rgb)
    plt.title(f'{title2} (Correct Colors)')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()


def plot_histograms(img_rgb, title='RGB Channel Histograms'):
    """
    Plot histograms for all three RGB channels.
    
    Parameters:
        img_rgb (numpy.ndarray): RGB image
        title (str): Title for the plot
    """
    colors = ('r', 'g', 'b')
    channel_names = ['Red', 'Green', 'Blue']
    
    plt.figure(figsize=(10,6))
    
    for i, col in enumerate(colors):
        hist = cv2.calcHist([img_rgb], [i], None, [256], [0,256])
        plt.plot(hist, color=col, label=f'{channel_names[i]} Channel')
        plt.xlim([0,256])
    
    plt.title(title)
    plt.xlabel('Pixel Intensity (0–255)')
    plt.ylabel('Frequency')
    plt.legend()
    plt.grid(alpha=0.3)
    plt.show()


# Example usage of helper functions
img = load_and_check_image('Girl.jpg')
if img is not None:
    display_image_comparison(img)
    img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
    plot_histograms(img_rgb, 'RGB Histograms - Girl.jpg')
```

