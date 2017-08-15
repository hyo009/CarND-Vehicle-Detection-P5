# Vehicle Detection

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector.
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.
This is the Writeup.

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.
I read two type of images from dataset (cars and not-cars).

![alt text](https://github.com/hyo009/CarND-Vehicle-Detection-P5/blob/master/output_images/car_not_car.png)

I used get_hog_features() function to get HOG (P5.ipynb cell 7). The parameters used in HOG function are: orient = 9, pix_per_cell = 8 and cell_per_block = 2.

![alt text](https://github.com/hyo009/CarND-Vehicle-Detection-P5/blob/master/output_images/HOG.png)

I combined color feature histogram and spatial binning with HOG as feature to train the SVM classifier.
The parameters of spatial binning are: spatial_size = (16, 16), hist_bins = 16.

![alt text](https://github.com/hyo009/CarND-Vehicle-Detection-P5/blob/master/output_images/spatial_binning.png)

I also compare the color features in different color areas such as RGB, HSV and YCrCb. And chose YCrCb for the best test accuracy.

![alt text](https://github.com/hyo009/CarND-Vehicle-Detection-P5/blob/master/output_images/color_features_histogram.png)

Finally, I normalized the features I extracted from the dataset.

![alt text](https://github.com/hyo009/CarND-Vehicle-Detection-P5/blob/master/output_images/normalized_features.png)

#### 2. Explain how you settled on your final choice of HOG parameters.
I tried several values for HOG parameters. I was able to reduce the training time by increasing orient number. But I found the parameters given by the example of the lectures had great result and the length of features was not too long. So I chose orient = 9, pix_per_cell = 8 and cell_per_block = 2. The prediction of SVM is 0.9825. (P5.ipynb cell 15)

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).
Cell 5-10 of P5.ipynb contains the parameters and functions I used to extract features to train SVM in cell 15. I tried several combinations of parameters. Finally, I chose the paramenters similar to the lectures. I trained the SVM classifier in 3.64s on a I5 CPU laptop with 4G RAM. The test accuracy is 0.9825.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?
My parameters for window search are: xy_overlap = (0.8, 0.8), x_start_stop = [760, 1260],
 xy_window = [75, 75] for y_start_stop = [400, 500],
 xy_window = [96, 96] for y_start_stop = [400, 500],
 xy_window = [140, 140] for y_start_stop = [400, 600].
 I tried various combinations of these parameters. I found if the search area was big, the search time will be too slow. And I found vehicles only appeared in specific area. So narrow the search area to x_start_stop = [760, 1260] and y_start_stop = [400, 600]. It accelerated my algorithm. Since for different areas, vehicles' sizes are different. So I used three types of search windows 75, 96 and 140; Large windows for near vehicles and small windows for farther vehicles. After some experiments, I used 0.8 overlap for the best balance of accuracy and searching time.
 ![alt text](https://github.com/hyo009/CarND-Vehicle-Detection-P5/blob/master/output_images/draw_boxes.png)

#### 2. Show some examples of test images to demonstrate how your pipeline is working. What did you do to optimize the performance of your classifier?
After getting the windows from searching windows function. I used heatmap method to reduce the error and combine the windows of the same vehicle.

![alt text](https://github.com/hyo009/CarND-Vehicle-Detection-P5/blob/master/output_images/heatmap.png)

Here are the result of test images used the pipeline.

 ![alt text](https://github.com/hyo009/CarND-Vehicle-Detection-P5/blob/master/output_images/test_results.png)

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a link to my video result

[![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/wrIQtbU0YOM/0.jpg)](https://www.youtube.com/watch?v=wrIQtbU0YOM)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.
As taught in the classroom lectures, heatmap and thresholding was used to sum up the nearby frames detected as vehicle. The functions for this are defined in cell 18-20 of P5.ipynb.
I also used collections.deque to store the heatmap of teh last 5 frames to stabilize the boxes of vehicles.

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?
Like I mentioned above, I was was first facing the issue that my pipeline was detecting only back portion of cars and not the side portion. This was resolved by adding more training data.

I find the size of window changing dramatically in some scenes. I think I can record the history of the size and position of vehicle and show the average size of previous three or four images. Then the change of window size will be smooth.

I believe that this pipeline will likely fail for any new type of car it sees as it heavily depends on the models of cars and their specific shapes. It cannot do a job similar to human eye by detecting any new type of car with a different shape then usual car shapes. I believe the pipeline can be optimized to become more faster and accurate relatively.
