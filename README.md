**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/hog.png
[image2]: ./output_images/boxes.png
[image3]: ./output_images/heat.png
[image4]: ./output_images/track.png
[image5]: ./output_images/car.png
[image6]: ./output_images/noncar.png
[image7]: ./output_images/final_heat.png

[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the cell 5 and 6 of the IPython notebook [Vehicle_Detection](./Vehicle_Detection.ipynb).  

I started by reading in all the `car` and `non-car` images.  Here are some examples of each of the `car`:

![alt text][image5]

and `non-car` images:

![alt text][image6]
I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `RGB` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image1]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters to train the model on. `YCrCb`, `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)` seems to be the best combination to build the model. Other color spaces like RGB and YUV also yield high accuracy, but seem to predict more false positives.
With this combination, the test accuracy is 99%. I also tried `block_norm = L2-Hys`, but it does not provide improvement to the model.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using HOG features and color features in cell 10 to 18 of the IPython notebook [Vehicle_Detection](./Vehicle_Detection.ipynb).

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I used the approach described in the lesson in cell 20 of the notebook [Vehicle_Detection](./Vehicle_Detection.ipynb). Instead of overlap, I define how many cells to step in `cells_per_step = 2`. The following is the result on the test images after sliding the window across the search area:


![alt text][image2]


#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

I tried different scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector. I settled with 1.5 since it gives the best reesult on test images. Here are some example images:

![alt text][image4]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_output.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video. Since there are false positives and a lot of boxes around true positives, I used a heatmap and a threshold to only get the true car detections in cell 27 to 37 of the notebook [Vehicle_Detection](./Vehicle_Detection.ipynb). I also define a class `Vehicle_History` to track the car and save previously found positions and set higher threshold to combat false positives. Below is an example of the result on test images:

![alt text][image3]

The final results on test images are:

![alt text][image7]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?
Most of the problems I had were due to false positives. I had to train a better model or change the threshold to filter out those.
The pipeline will likely fail in cases where the cars do not resemble the training set. It could be due to lighting and other environment factors. On-coming traffic could create false positives and distant cars could create false negatives. We could uuse speed calculation to track the car better. 
We also could use more than one scale to find the cars and apply heat map to them. 
We can also use multiple window sizes to search for cars for different Y's of the frame. 
We could decrease the number of features or lose the color features to perform faster. The accuracy of the model will not change much without the color features.
Deep learning (CNN) could be use for vehicle detection and tracking.