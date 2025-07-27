# Real-Time Object Dimension Measurement with OpenCV

This Python project leverages the OpenCV library to perform real-time object detection and measurement from a webcam feed. The script identifies prominent objects, draws a bounding box around them, and calculates their approximate width and height in centimeters.

![Project Demo]<img width="1682" height="1012" alt="image" src="https://github.com/user-attachments/assets/9942ff6a-d008-4a29-956f-f8b1f12e2c2f" />


## Features
-   **Live Video Analysis**: Processes video feed directly from your webcam in real-time.
-   **Automatic Object Detection**: Identifies and isolates objects in the frame based on their size and shape.
-   **Dimension Calculation**: Measures the width (labeled 'B' for Breadth) and height (labeled 'L' for Length) of detected objects.
-   **Visual Feedback**: Overlays the bounding box, measurement axes, and calculated dimensions directly onto the video stream for easy visualization.
-   **Object Counting**: Displays a live count of the objects currently being measured.

***

## How It Works

The script processes each frame from the camera using a standard computer vision pipeline:

1.  **Image Preprocessing**:
    -   The frame is converted to **grayscale** to simplify the image data.
    -   A **Gaussian Blur** is applied to reduce image noise and smooth out details.
    -   **Adaptive Thresholding** is used to create a binary (black and white) image, which effectively separates the object from the background.
    -   A **Morphological Closing** operation is performed to fill small holes and gaps in the object's outline, resulting in a more solid and reliable shape.

2.  **Contour Detection**:
    -   The script uses `cv2.findContours()` to find the outlines of all the shapes present in the processed binary image.

3.  **Object Filtering and Measurement**:
    -   It iterates through all detected contours and filters out any that are too small or too large. This step is crucial for ignoring noise and focusing only on the intended object of measurement.
    -   For a valid contour, it calculates the **minimum area rotated rectangle** that can enclose the object. This is important for accurately measuring objects that may be tilted.
    -   The **Euclidean distance** between the midpoints of the rectangle's opposing sides is calculated to determine the object's width and height in pixels.

4.  **Pixel-to-Centimeter Conversion**:
    -   The calculated pixel dimensions are converted to centimeters using a hardcoded calibration factor.
    -   The final, real-world measurements are then displayed on the screen.

***

## Technology Stack
-   **Python 3.x**
-   **OpenCV** (`opencv-python`): The core library for all computer vision tasks.
-   **imutils**: Provides convenience functions for basic image processing operations like perspective ordering.
-   **NumPy**: Used for numerical operations and efficient array manipulation.
-   **SciPy**: Used for its efficient implementation of the Euclidean distance calculation.

***

## Setup and Installation

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git](https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git)
    cd YOUR_REPOSITORY
    ```

2.  **Create a virtual environment (highly recommended):**
    ```bash
    python -m venv venv
    ```
    * On Windows, activate it with:
        ```bash
        venv\Scripts\activate
        ```
    * On macOS/Linux, activate it with:
        ```bash
        source venv/bin/activate
        ```

3.  **Install the required libraries:**
    ```bash
    pip install opencv-python numpy imutils scipy
    ```

***

## How to Run the Script

To start the application, simply run the Python script from your terminal:
```bash
python your_script_name.py
```
A window showing your webcam feed will appear. Place an object in the frame to see it being measured.

To **stop the script and close the window**, press the **`ESC`** key.

***

## ⚠️ Important: Calibration

The accuracy of the measurements depends entirely on a **hardcoded calibration factor**. In the code, the pixel measurements are divided by `25.5` to get centimeters:

```python
cv2.putText(orig, "L: {:.1f}CM".format(lebar_pixel/25.5), ... )
cv2.putText(orig, "B: {:.1f}CM".format(panjang_pixel/25.5), ... )
```

This `25.5` value represents the **pixels-per-centimeter ratio**. This ratio will **change** depending on your camera's resolution and, most importantly, its distance from the object. To get accurate results, you **must calibrate it for your specific setup**:

1.  Place a reference object with a **known width** (e.g., a credit card is ~8.56 cm wide, a ruler) in the camera's view. Make sure it's at the exact distance you plan to measure other objects from.
2.  Temporarily modify the code to print the raw pixel width (`panjang_pixel`) to the console.
3.  Run the script and note the pixel width for your reference object.
4.  Calculate your new ratio: `your_ratio = raw_pixel_width / known_width_in_cm`
5.  Replace the hardcoded `25.5` in the code with `your_ratio`.

