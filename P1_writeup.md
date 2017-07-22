# Project 1: Finding Lane Lines on the Road 

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)
[image_orig]: ./test_images/solidYellowCurve.jpg "Original"
[image_gray]: ./test_images_output/solidYellowCurve_gray.jpg "Grayscale"
[image_gaussian]: ./test_images_output/solidYellowCurve_guassian.jpg "Gausian"
[image_edges]: ./test_images_output/solidYellowCurve_edges.jpg "Canny Edges"
[image_masked_edges]: ./test_images_output/solidYellowCurve_masked_edges.jpg "Masked Edges with ROI"
[image_hough]: ./test_images_output/solidYellowCurve_Houghline.jpg "Hough"
[image_result]: ./test_images_output/solidYellowCurve_result.jpg "Result"

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consists the following steps:

* Convert the original image into grayscale: <br/>
As an example, here is the original image SolidYellowCurve.jpg, one of the test images:
![alt text][image_orig]
After converting it to gray scale:
![alt text][image_gray]

* Apply Gaussian kernel smoothing/blurring:<br/>
The kernel size I chose for Gaussian smoothing is 3. 
![alt text][image_gaussian]

* Edges detetion with Canny algorithm: <br/>
This is to find the candidates of line segments and the parameters that I chose are: <br/>
low_threshold = 50 <br/>
high_threshold = 150 <br/>
The Canny edge image: 
![alt text][image_edges]

* Create the masked edges image:<br/>
A trapezoid maskk was chosen as to keep only those edges in the region of interest, and get rig of the outlier line segements outside of the region. <br/><br/>
The four corners of the trapezoid was chosen with some ratios of the length and width of the image, while the ratios are hard coded values.
![alt text][image_masked_edges]

* Draw the left and right lane line:<br/>
In order to draw a single line on the left and right lanes, first calculate the slopes of the line segments and grouped them together as the left and right lane segments depending on whether the slopes are negative and postive. Then using the average slope and interception of those left and right segments to draw the lines. The minimum value for coordinate y is found by the minimum y of all those segments.<br/><br/>
While the negative and postive slopes seperation work for the first two videos, it did not work well for the frames in the challenge video because there are a lot of outlier line segements in the Hough line detection, resulting from tree shadows etc. To get rid of those outliers, only those line segements with slopes in [-0.85, -0.65] for the left and [0.40,.80] for the right were kept and the outliers were filtered out. <br/><br/>
To avoid the two drawed lines crossing each other on the top, the length of the lines are further limited so that all the x coordinates on the left lines are smaller than those on the right lines.
![alt text][image_hough]

* Draw the left and right lines on the original image:<br/>
![alt text][image_result]

### 2. Identify potential shortcomings with your current pipeline
While the current pipeline worked on the two required videos and worked quite well on the challenge video, it did not do a good finding on several frames in the challenge video. In one frame, the left yellow lane is not so bright because of the tree shadows. This missing detection indicates that the current pipeline will not work well with complicated images, such as images with different light conditions, images with complicated scenes, and images with heavy traffic.

The right white lane in one of the frames in the challenge video was not found with the pipeline. In this frame, the right white lane were only visiable as several white points in the lower bottom of the image. Although the lane could be easiuly identified with eyes onb the upper right part, it is not a straight line, but has a large curvature. This shows that the pipleline does not work well with curvatured roads and/or lane lines. 

Another limitation of the pipe line is that it has many hard coded paramters, such as the threshld to choose the white color, the position of the ROI and the limit of the slopes to group left and right lanes together.

### 3. Suggest possible improvements to your pipeline

A possible improvement would be to fit the lane lines with other shaped curves rather than simply straight line.  By doing this, it would improve the detection of curved lanes on the road. 

The current pipe line only uses use the lower and upper thresholds that is good to choose the white color. One potential improvement is to choose both white color and yellow color. One idea would be converting the original RGB images into HSV space, and apply thresholds to keep the pixels with yellow and white colors. This would enhance the detection for yellow lane lines.

The region of interest for the mask was the same trapezoid for all the videos frames in the current pipeline. It would be possible to have a dynamic moving region of interest for each image frame based on intensity analysis of the images. OTher hard coded parameters could potentially be changed to dynamic values too for better results. 


