
# **Finding Lane Lines (Advanced)** 
***

Goal of this project is the implementation of a software pipeline to identify and draw the lane boundaries in a video from a front-facing dashcam.
## Compute the camera calibration matrix and distortion coefficients given a set of chessboard images

As a first step, "object points" are prepared, representing the (x, y, z) coordinates of the chessboard corners in the world. The subsequent calculations are based on the assumption of a chessboard, which is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time all chessboard corners are succesfully detected in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection. The chessboard images with the found corners are saved to */output_images/corners/*.

## Apply a distortion correction to raw images

Next, the `objpoints` and `imgpoints` are used to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  The  distortion correction is then applied to several test images using the `cv2.undistort()` function, and the undistorted test images are saved to *output_images/undistorted/*.

## Apply a perspective transform to rectify binary image ("birds-eye view")
The following code warps the test images to a bird-eye view using the *cv2.getPerspectiveTransform* and the *cv2.warpPerspective* functions. Based on a source and a destination trapezoid, *cv2.getPerspectiveTransform* generates a transformation matrix that is used by *cv2.warpPerspective* to transform the perspective of the initial image according to the source/destination mapping. The source and destination trapezoids were found empirically, by using a test image with straight lines and defining the source and destination points such that the lines in the transformed image are parallel. The warped test images are saved to *output_images/warped/*.

## Determine the curvature of the lane and vehicle position with respect to center
The radius of the found lane fits is calculated based on a formula introduced at https://classroom.udacity.com/nanodegrees/nd013/parts/fbf77062-5703-404e-b60c-95b78b2f3f9e/modules/2b62a1c3-e151-4a0e-b6b6-e424fa46ceab/lessons/40ec78ee-fb7c-4b53-94a8-028c5c60b858/concepts/2f928913-21f6-4611-9055-01744acc344f. Since the original lane fits were calculated in pixel space and the actual curve radius has to be calculated for real world space, the values are transformed based on an estimated meter / pixel ratio. 

The position of the car relative to the image center is calculated by first finding the center between the lowest y-positions of both lanes and then subtracting this value from the image center. Again, all values are transformed to real world space. 

## Warp the detected lane boundaries back onto the original image
The following code rewarps the lane-fits back into the original image perspective by using the *corners_unwarp* function defined above and just reversing the source and destination points. The undistorted test images with the unwarped lane markers are saved to *output_images/final/*.

## Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position
Finally, all steps from are combined in the *process* function, which calculates the lane fits for an image in one go and outputs the original image with the lane markers. This function is then used by the *fl_image* of the *moviepy.editor.VideoFileClip* class, to generate the lane markers for all frames of a video. The pipeline performs some basic plausibility checks on the calculated curvatures and the lane distance and uses the previous values if the current ones are found to be implausible. A counter ensures that the previous fits are only reused 3 times, before the lanes are reculculated completely using the sliding window approach. The final video is saved as *challenge_processed.mp4* 

## Problems and issues 
The implemented pipeline works is able to detect lane lines in all frames and the found lines are in most cases consistent with the real lines. There are still some glitches however, especially in cases where the light conditions change drastically from one frame to the next. Since the plausibility checks are not able to filter out all such cases, a transformation to another color space and/or the usage of different channels which are less susceptible to changing light conditions, could make the pipeline more robust. 
Another problem are the unreliable curvature calculations, which differ widely from frame to frame and rarely indicate parallel lines. A more sophisticated algorithms may help here.
A generaly problem is, that the implemented pipeline is overfitted to the project video and fails to detect lines in different videos. 
