##Writeup Template
###You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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

[image1]: ./output_images/img2.png "Undistorted"
[image2]: ./output_images/img3.png "Undistorted"
[image3]: ./output_images/img4.png "Undistorted"
[image4]: ./output_images/img5.png "Undistorted"
[image5]: ./output_images/img6.png "Undistorted"
[image6]: ./output_images/img7.png "Undistorted"
[image7]: ./output_images/img8.png "Undistorted"
[image8]: ./output_images/img10.png "Undistorted"
[image9]: ./output_images/img11.png "Undistorted"
[image10]: ./output_images/img12.png "Undistorted"
[image11]: ./output_images/img13.png "Undistorted"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!
###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

Following the code whithin the lessons, I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.
To demonstrate this step, attached an image with the distortion correction applied:
![alt text][image2]
####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
I used a combination of channel and gradient thresholds to generate a binary image following the steps recommended in the lesson. The examples of the outputs for each one of this gradientes are:

![alt text][image3]

![alt text][image4]

![alt text][image5]

![alt text][image6]


####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `unwarp()`, that takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  Whith the base of the points suggested in the template I ended choosing the hardcode of the source and destination points, by trial and error, in the following manner:

```
src = np.float32(
        [[(img_size[0] / 2) - 60, img_size[1] / 2 + 100],
        [((img_size[0] / 6) - 10), img_size[1]],
        [(img_size[0] * 5 / 6) + 50, img_size[1]],
        [(img_size[0] / 2 + 65), img_size[1] / 2 + 100]])
    dst = np.float32(
        [[(img_size[0] / 4), 0],
        [(img_size[0] / 4), img_size[1]],
        [(img_size[0] * 3 / 4), img_size[1]],
        [(img_size[0] * 3 / 4), 0]])
```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image7]

After that I was prepared to compute what I called the Pipeline stage 1 Lane detection, which resulted in the detection of lane pixels in all the test-images.

As an example here the one correspondint to test6.jpeg

![alt text][image8]

I also tested and alternative pipeline performing the perspective transform as the first step, but the results where poorer than the original function.

####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I followed the code examples in the lesson to fit my lane lines with a 2nd order polynomial using two functions, the first one using sliding windows called ´sliding_window_polyfit´, and a second one ´polyfit()´ to skip the sliding windows step once you know where the lines are:

The test result for both functions are:

![alt text][image9]

![alt text][image10]

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I compute the radius of curvature in my code in using the function ´calc_curvature´, following the code-examples in the lesson. The offset of the position of the vehicle respect to center it is computed as the mean of the values of right and left polynomials in the bottom of the image ´image.shape[0]´, using an auxilary function ´get val()´

This data is inserted in the resulting image.

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in `draw_output()`   Here is an example of my result on a test image:

![alt text][image11]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

