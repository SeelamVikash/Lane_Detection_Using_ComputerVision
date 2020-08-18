**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Apply a perspective transform to distorted image ("birds-eye view").
* Use color transforms, gradients, etc., to create a thresholded binary image of warped image.
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/original_vs_correctedboard.png "Undistorted"
[image2]: ./output_images/original_vs_correctedboard.png "Undistorted test_image"
[image3]: ./output_images/warped_img.png "Warp Example"
[image4]: ./output_images/warped_to_binarythreshold.png "Binary Example"
[image5]: ./output_images/window_image.png "Fit Visual"
[image6]: ./output_images/previous_fit_img.png "Output"
[image7]: ./output_images/final_output_of_7.png "Output"
[video1]: https://youtu.be/X9RJ8_MS6HM "Video" 


### Writeup / README

### Camera Calibration

#### 1. computing the camera matrix and distortion coefficients. 

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Example of a distortion-corrected image.

I have corrected the distorted test image using the function `undistort()` in the note book
![alt text][image2]


#### 2. Performing a perspective transform.

The code for my perspective transform includes a function called `war_img`, which appears in the 7th code cell of the IPython notebook.  The `warp_img()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I have written functions source and destination in the same code cell which could give src and dst points. But, I have used the below points in this project.
```python
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 460      | 320, 0        | 
| 203, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image3]

#### 3. Applying color transforms, gradients or other methods to create a thresholded binary image. 

I used a combination of color and gradient thresholds to generate a binary image. Here's an example of my output for this step.

![alt text][image4]

#### 4. Identifying lane-line pixels and fit their positions with a polynomial?

##### for explanation
I have written functions `find_lane_pixels` to detect lane pixels using the sliding window approach and `fit_polynomial` to fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

Then for faster detection using the previous detected lane coefficients I have written functions `search_around_poly` to search for lane pixels around the previous fitted polynomial with a margin of 100px and `fit_poly` to get updated polynomial coefficients

##### for real time purpose

I have used `find_lane_pixels` to detect lane pixels using the sliding window approach and  `window_search` to fit my lane lines with a 2nd order polynomial kinda like this and this happens only for one frame:

![alt text][image6]

Then for faster detection using the previous detected lane coefficients I have written functions `search_using_prevfit` to search for lane pixels around the previous fitted polynomial with a margin of 100px and `fit_poly` to get updated polynomial coefficients


#### 5. Calculation of the radius of curvature of the lane and the position of the vehicle with respect to center.

I have calculated the radius of curvature and the position fo the vehicle using the knowledge from the lectures and written the code in the cell 21 of the notebook function named as `measure_curvature_real`.

#### 6. Here is an example image of plotted back down onto the road such that the lane area is identified clearly.

To achieve I have written function `path_fill` and uses cv2.polyfill() function. The below is an example of how it looks on the image

![alt text][image7]

---

### Pipeline (video)

1. Read the frame.
2. warp it. 
3. apply color tranform and thresholds for binary image.
4. detect the lane pixels just as explained in the case of image.
5. find radius of curvature and offset of the vehicle from centre.
5. draw a path between the lanes.
6. unwarp the frame to original and put radius of curvature and offset on the frame.

####  link to my final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](https://youtu.be/X9RJ8_MS6HM)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

