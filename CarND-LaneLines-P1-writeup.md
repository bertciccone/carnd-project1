# **Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

---

### Reflection

I used 8 steps in my pipeline:
* Color selection
* Grayscale
* Gaussian smoothing
* Canny edge detection
* Region of interest
* Hough transform
* Fitline to create left/right solid lines
* Overlaying lines onto the original image

### Test function
I created a test function that enabled me to show intermediate results of my pipeline during testing. It can either show one specified step in the pipeline for all the images, or show all the pipeline steps for a specified image. This enabled me to experiment with pipeline parameters easily and see the results.

Example:

![Pipeline on first image][image1] ![][image2] ![][image3]

### Color selection
In early testing, random and near-horizontal lines appeared in the solidYellowLeft video, which were eliminated using these thresholds in color selection:
red_threshold = 225
green_threshold = 200
blue_threshold = 128

### Grayscale
I used the provided grayscale helper function.

### Gaussian smoothing
I used a kernel size of 5 for Gaussian blur, which seemed to work well.

### Canny edge detection
A low threshold of 64 and high threshold of 128 were used for Canny edge detection.

### Region of interest
My region of interest consisted of a rectangle in the lower 40% of the image, with angled sides.

### Hough transform
I modified the hough_lines function to return the lines obtained from cv2.HoughLinesP, enabling me to pass the lines to cv2.fitline in the next step.

### Fitline to create left/right solid lines
In my_draw_lines I created left and right solid lines with these steps:
* calculated the slope of each line segment to separate the right lines (positive slope) from the left lines (negative slope)
* cv2.fitline takes each set of line segments and finds a best fit line through them; the parameter DIST_L2 specifies use of "simplest and fastest least squares method" of determining the best fit line.
* Given the collinear vector and point on the line obtained from fitline, I found m, b and x_lower/x_upper by solving the equation of a line Y=mX+b for the left and right lines
* These x coordinates combined with the y coordinates of my region of interest determined the endpoints of the lane line overlays
* Error handling: some line segments from HoughLinesP caused ValueError or OverflowError in these calculations; in these cases I caught the error and skipped inclusion of that segment. A better implementation would filter out the values that would cause the calculation of an infinite result.

### Overlaying the lines onto the original image
The lines calculated in the last step were overlayed onto the original image using cv2.line.

### 2. Shortcomings of the current pipeline
* The current pipeline performs relatively well in the static test images, but in the videos creates instances of missing lines and lines that vary slightly in direction. These effects are more significant in the solidYellowLeft video than in the solidWhiteRight video.
* The pipeline does not handle the challenge video very well at all, only occasionally drawing a reasonable line successfully.
* In terms of the approach, the overall strategy only handles straight roads with painted lane markers. Therefore a completely different strategy would need to be employed to handle curved roads and unpainted residential streets.
* As demonstrated in the challenge video, less ideal lighting conditions and shadows also need to be incorporated into the strategy.

### 3. Suggest possible improvements to your pipeline
Some Internet research turned up several possible improvements:
* Conversion to HSV color space to better identify color
* Eliminating image distortion from the camera lens used to capture images
* Performing processing on a virtual "birds eye view" of the region of interest, changing perspective by stretching the upper edge of the image.
