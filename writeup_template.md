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

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./examples/test1.jpg "Distorted Road"
[image7]: ./examples/undistorted_straight.jpg "Undistorted Road"
[image3]: ./examples/thresholded.jpg "Binary Example"
[image4]: ./examples/lines_drawn.png "Lines Example"
[image8]: ./examples/warped.jpg "Warp Example"
[image5]: ./examples/poly.png "Fit Visual"
[image6]: ./examples/output.jpg "Output"
[video1]: ./edited.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook called code.ipynb  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

I used the distortion matrix from the camera calibration step and the undistortion function to undistort the image. It gives me this image:
![alt text][image7]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used an R and G channel threshold to ensure those values were above a certain value. Then I converted to HSV and isolated the S channel a combination of S channel and x gradient thresholds to generate a binary image. (thresholding steps at code block 6. Here's an example of my output for this step.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warp()`, which appears in code block 7. Examples of its use are shown.  The `warp()` function takes as inputs an image (`img`), and uses as source`src`and destination (`dst`) points defined above the function.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(192, 720],
    [578, 460],
    [704, 460],
    [1118, 720]])
dst = np.float32(
    [[210, 720],
    [210, 0],
    [1070, 0],
    [1070, 720]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 192, 720      | 210, 720      | 
| 578, 460      | 210, 0        |
| 704, 460      | 1070, 0       |
| 1118, 720     | 1070, 720     |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]
![alt text][image8]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I used sliding window search to isolate the left and right lanes. After isolating the pixels within the windows, I used np.polyfit() to my get a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this code block 9 in the function curve() that takes in a list of polynomial coefficients and a y location to evaluate.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in code block 10 in the function `pipeline()` using all the functions I've defined up until now. Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./edited.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further. The dark section of the road would sometimes have high saturation values, so I added an R and G channel threshold. The lane fit would go far away from the actual lane so I changed it so the window location could only change a certain number of pixels per frame. Near the lane lines there are some spots that have some of the properties 
