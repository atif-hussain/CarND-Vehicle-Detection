---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./examples/car_not_car.png
[image2]: ./examples/HOG_example.jpg
[image3]: ./examples/sliding_windows.jpg
[image4]: ./examples/sliding_window.jpg
[image5]: ./examples/bboxes_and_heat.png
[image6]: ./examples/labels_map.png
[image7]: ./examples/output_bboxes.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the code cell #6 of the IPython notebook. In code cell #7, we call hog with visual=true, to illustrate hog of images. 

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of samples of each of the `vehicle` and `non-vehicle` classes:

![car and noncar][./output_images/car_noncar.png]

I then explored different color spaces and different `cv2-hog` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed a randomized image from each of the two classes and displayed them to get a feel for what the `hog` output looks like.

Here is an example using the `BGR`, `YUV`, `YCrCb` color spaces and HOG parameters of `orientations=12`, `pixels_per_cell=(8, 8)` and `cells_per_block=(1, 1)`:

![HOG outputs][./output_images/HOG.png]

####2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and...
pixel_per_cell = 8x8
cells_per_block = 2x2, 1x1
hog_orientations = 8, 12, 16
![HOG outputs][./output_images/HOG_orient.png]

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I extracted the features in code cell #8, and trained a linear SVM on scaled fearures, in code cell #9. 

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

* I first picked the window sizes to search with, scaling all over the image to cover the relevant part of the image. Square windows of sizes 192, 128, 96, 64 were taken. 
* Next, picked the x & y range within which to fit the windows. Identified start and stop for boundary box windows for x and y axes. 
* Then, selected all windows in x and y dimension, based on an overlap  of 0.75 and 0.5 based on window size

![sliding windows][./output_images/windows.png]

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on four scales using 9-channel HOG features (BGR, YUV, YCrCb) plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:
To speed things up, extracted HOG features just once for the entire region of interest and subsampled that array for each sliding window. 

![bounding boxes][./output_images/bboxes.png]
---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_out.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are six frames and their corresponding heatmaps:

![heatmaps][./output_images/heatmap_6frames.png]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from all six frames:
![integrated-heatmaps][./output_images/heatmap_6frames.png]

### Here the resulting bounding boxes are drawn onto the last frame in the series:
![bounds][./output_images/bboxes.png]



---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

