# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:  
* Make a pipeline that finds lane lines on the road  
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/pipeline_illustration.png "illustration"

---


### 1. Description of the pipeline

The top level function of my pipeline is `lane_detector_from_image()`.  It consists of x steps

* Convert the RGB image to gray scale using the V channel of `HSV` space
* Find edges in the image. This is acheived by first apply a Gaussian filter with kernel size of 3, followed by performing `canny` edge detection, and finally mask the edges founds by `region_of_interest` fileter 
* Combine the edges into line segments by using `cv2.HoughLinesP` function. 
* Merge the line segments. See function `merge_lines()` for detail. In the function, I iterate though the line segments found in the previous step, and group them if they are aligned within a threshold. 
* Select the lane lines from the merged line segments. See function `select_lane_from_lines` for details. The selection is based on two simple huristics:
	- The lane lines vanishes at the center of the image. 
	- Left/right lane line has large positive/negative slope.
* Finally, the lane lines are extended to proper length and annotated on the image. 	 

![alt text][image1]


### 2. Shortcomings with the current pipeline and possible improvements 

* When run on a video input, the annotated lane lines are sometimes shakey from frame to frame. This is due to the current pipeline treat each frame in a video as independent input. As an impromvent, we can store the position of lane lines in previous frames and enforce the lane lines to be updated smoothly between frames
* When the lane line is curved, the current detection pipeline will select the portion of lane line with steeper slope. This can be seen in the beginning of `solidYellowLeft.mp4`. This is not ideal. A better approach would be understand the lane line consists of multiple portions and take the average of them.
