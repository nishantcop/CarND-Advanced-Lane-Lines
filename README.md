## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[undistorted]: ./output_images/writeup/undistorted_comparison.PNG "Undistorted"
[distortionCorrection]: ./output_images/writeup/distortion_correction.PNG "distortionCorrection"
[transformed]: ./output_images/writeup/transformed.png "transformed"
[transformedLarge]: ./output_images/writeup/transformed_large.PNG "transformedLarge"
[warped]: ./output_images/writeup/warped_image.PNG "warped"
[masked]: ./output_images/writeup/masked.PNG "masked"
[lines]: ./output_images/writeup/lines.PNG "lines"
[curvature]: ./output_images/writeup/curvature.PNG "curvature"
[final]: ./output_images/writeup/final.PNG "Final"
[video]: ./output_images/writeup/video.gif "Video"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in block3  (`In[3]`) line #24-28of the IPython notebook located in `./advance_lane_line` 

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][undistorted]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

While doing camera calibration, I've stored `mtx` and `dist` information using pickle. Please check line #12 in `Block 4`. We load this information and calibrate the test images.  Please check `Block 5` line # `6` to `19` calibration done in last step was To demonstrate this step. After applying distortion correction to one of the test image, result looks like this one:
![alt text][distortionCorrection]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at lines #2 through #38 in `Block 5`).  Here's an example of my output for this step. 

![alt text][transformedLarge]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `calculatePerspectiveTransform()`, which appears in lines 2 through 16 in `BLOCK 6` . The `calculatePerspectiveTransform()` calculates the transformation value `M` required to wrap an image. This function have hardcoded values for source (`src`) and destination (`dst`) points.  I've another function called `getPerspectiveTransform()` that apply perspective transform on image and return a warped image. I chose the hardcode the source and destination points in the following manner:

```python
src_corner = [
        [580,460], # top left
        [280,680], # bottom left
        [740,460], # top right
        [1050,680]  # bottom right
    ]
dst = [
        [200,0], 
        [200,680], 
        [1000,0], 
        [1000,680]
      ]
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 580,460       | 200, 0        | 
| 280, 680      | 200, 680      |
| 740, 460      | 1000, 0      |
| 1050, 680     | 1000, 680        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][warped]

Also, I did apply the additional masking for the region of interest to remove unwanted noise in images

![alt text][masked]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

Related code could be found in `Block 10` function named `findlines`. I used `Sliding Window` metod to find lines. After drawing the lines on a test image result looks like: 
![alt text][lines]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The next step is to calcuate the readious of curvature and identify position of the car. This is being handled in `Blcok 10` line #99 through 106 in function `pipeline()`. 

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines 1 through 18 in my code in `Block 11` in the function `drawLine()`.  Here is an example of my result on a test image including writing the vehich position information:

![alt text][final]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my youtube video result](https://youtu.be/3myEpU1TVQs)

![alt text][video]

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

My pipeline is still not working good for challange video. My pipeline still not performing well for diverse lighting condition and when there are road sign on road itself e.g. car-pool or variable coloring on road due to roadwork. This will be my next step to improve it and drive it on challange and hard challange video. 
