# **Self Driving Car ND Project 1: Finding Lane Lines on the Road** 

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)
[image_orig]: ./test_images/solidYellowCurve.jpg "Original"
[image_gray]: ./test_images_output/solidYellowCurve_gray.jpg "Grayscale"
[image_gaussian]: ./test_images_output/solidYellowCurve_guassian.jpg "Gausian"
[image_edges]: ./test_images_output/solidYellowCurve_edges.jpg "Canny Edges"
[image_masked_edges]: ./test_images_output/solidYellowCurve_masked_edges.jpg "Masked Edges with ROI"
[image_hough]: ./test_images_output/solidYellowCurve_HoughLine.jpg "Hough"
[image_result]: ./test_images_output/solidYellowCurve_result.jpg "Result"
---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consists the following steps:

(1) Convert the original image into grayscale. Using solidYellowCurve.jpg as an example, the original image:
![alt text][image_orig]
After converting it to gray scale:
![alt text][image_gray]

(2) Apply Gaussian kernel smoothing:
![alt text][image_guassian]

(3) Edges detetion with Canny algorithm to find candidates of line segments:
![alt text][image_edges]

(4)Create the masked edges image with a trapezoid as region of interest to get rig of line segements outside of the region: 
![alt text][image_masked_edges]

(5)Draw the left and right line:
In order to draw a single line on the left and right lanes, I calculated the slopes of the line segments and grouped them together as the left and right lane segments depending on whether the slope is negative and postive. Then using the average slope and interception of those left and right segments to draw the lines. The minimum value for coordinate y is find by the minimum y of all those segments.

While the negative and postive slopes seperation work for the first two videos, I found it did not work well for the frames in the challenge video because after Hough detection, there are a lot of noise segments in the line detection results from tree shadows etc. To get rid of those outliers, I only kept those with slopes in [-0.85, -0.65] for the left lane line and [0.40,.80] for the rigt. 
![alt text][image_hough]

(6)Draw the left and right lines on the original image:
![alt text][image_result]

### 2. Identify potential shortcomings with your current pipeline
While my current pipeline worked on the two required videos and worked quite well on the challenge video, it did not do a good detection of the lanes on several frames in the challenge video. In one frame, the left yellow lane is not apparent with tree shadow. It indicates that the current pipeline might not work well with different light condition, complicated scenes, and heavy traffics.

There is another missing detection of the right white lane in the challenge video. In this frame, the right white lane only shows several white points in the lower bottom, while the upper right has clear curved lane lines. This shows that the pipleline does not work well with curvatured roads and lane lines. 

Another limitation of the pipe line is that it has many hard coded paramters, such as the position of the ROI, the limit of the slopes to group left and right lanes together.

### 3. Suggest possible improvements to your pipeline

A possible improvement would be to fit the lane lines with a curve rather than the straight line.  By doing this, it would improve the possibility to not miss the white right lane mentioned above. 

My pipe line only uses use the lower and upper thresholds that is good to detect white lanes. One potential improvement could be to convert the original RGB images into HSV space, and apply thresholds to keep the pixels with yellow color and this would enhance the detection for yellow lane lines.

The region of interest for the mask was the same trapezoid for all the videos frames in my current pipeline. It would be possible to have a dynamic moving region of interest for each image frame.
