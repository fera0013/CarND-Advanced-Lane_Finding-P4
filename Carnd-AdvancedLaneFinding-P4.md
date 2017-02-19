
# **Finding Lane Lines (Advanced)** 
***

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
Finally, all steps from are combined in the *process* function, which calculates the lane fits for an image in one go and outputs the original image with the lane markers. This function is then used by the *fl_image* of the *moviepy.editor.VideoFileClip* class, to generate the lane markers for all frames of a video. the process function performs a few basic plausibilty checks, based on the assumption that consecutive images of a video are similar. If the radius and the position of the car are not sufficiently similar between two consecutive images, the current fit is rejected and replaced by the previous calculation. The final video is saved as *challenge_processed.mp4* 


