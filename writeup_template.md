## Writeup Template
### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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
[image1]: https://github.com/akhilumat/Self-Driving-Car-Term-1-Project5-Vehicle-detection-and-Tracking/blob/master/output_images/Capture1.PNG
[image2]: https://github.com/akhilumat/Self-Driving-Car-Term-1-Project5-Vehicle-detection-and-Tracking/blob/master/output_images/Capture2.PNG
[image3]: https://github.com/akhilumat/Self-Driving-Car-Term-1-Project5-Vehicle-detection-and-Tracking/blob/master/output_images/Capture3.PNG
[image4]: https://github.com/akhilumat/Self-Driving-Car-Term-1-Project5-Vehicle-detection-and-Tracking/blob/master/output_images/Capture4.PNG
[image5]: https://github.com/akhilumat/Self-Driving-Car-Term-1-Project5-Vehicle-detection-and-Tracking/blob/master/output_images/Capture5.PNG
[image6]: https://github.com/akhilumat/Self-Driving-Car-Term-1-Project5-Vehicle-detection-and-Tracking/blob/master/output_images/Capture6.PNG
[image7]: https://github.com/akhilumat/Self-Driving-Car-Term-1-Project5-Vehicle-detection-and-Tracking/blob/master/output_images/Capture7.PNG
[image8]: https://github.com/akhilumat/Self-Driving-Car-Term-1-Project5-Vehicle-detection-and-Tracking/blob/master/output_images/Capture8.PNG
[image9]: https://github.com/akhilumat/Self-Driving-Car-Term-1-Project5-Vehicle-detection-and-Tracking/blob/master/output_images/Capture9.PNG
[image10]: https://github.com/akhilumat/Self-Driving-Car-Term-1-Project5-Vehicle-detection-and-Tracking/blob/master/output_images/Capture10.PNG
[image11]: https://github.com/akhilumat/Self-Driving-Car-Term-1-Project5-Vehicle-detection-and-Tracking/blob/master/output_images/Capture11.PNG
[image12]: https://github.com/akhilumat/Self-Driving-Car-Term-1-Project5-Vehicle-detection-and-Tracking/blob/master/output_images/Capture12.PNG
[video1]: https://github.com/akhilumat/Self-Driving-Car-Term-1-Project5-Vehicle-detection-and-Tracking/FinalOutput_Video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how you extracted HOG, Color Histogram and  Pixel values with spatial binning, features from the training images.

Firstly I separated cars and notcars images from training images. I also added few of my images to augment the dataset. Total number of car images were 8841 and not car images were 8968. Images are shown below

![alt text][image1]

![alt text][image2]

I then built three functions to extract HOG features, Color Histogram and Raw pixel values with spatial binning from an image. Three fuctions used are in cell 3 of my code. After that I also built one more fuction which would extract all the features from an image using above mentioned 3 fuctions. This function would take set of images, read them individually, resize them, convert 4 channel images to 3 channel, convert image into desired colorspace and then extact HOG features, Color historam features and Pixel values. This function could extract HOG feature from individual channel or all the 3 three channels. After taking out all features this function concatenated all the features in one row for each image and appended it to features matrix. One of the car image and HOG features of its 3 channels are shown below

![alt text][image3]
![alt text][image4]
![alt text][image5]
![alt text][image6]
 
#### 2. Explain how you settled on your final choice of HOG parameters.

While training the model using Linear SVC, I used various color spaces, orientation number, pixels per cell and cells per block. Testing accuracy was used to decide on the final parameters. Best test accuracy was achieved when I used colorspace 'YCrCb', orientations = 10, pixels per cell = 8 and cell per block = 2 and HOG channel = 'ALL'. For histogram number of bins used were 16 and for spatial binning function rezise value of 16x16. Final test accuracy achieved was of 98.68%


### Sliding Window Search

#### 1. Describe how you implemented a sliding window search?

For sliding window search, I defined a function that would extract Hog features from an image once and make predictons. Function defined find_cars is in cell 5 of my code. This function would take image, search area, scale value, number of orientations, pixels per cell, cells per block, spatial size and histogram bins as input. It would then clip the image only to search the desired area, convert it into YCrCb color space, resize the image as per scale values, calculate number of steps in x and y direction as per window size and cells per step, extract features for each window and draw a blue box if the prediction came true. Some of the output of this function on test images are shown below 

![alt text][image7]
![alt text][image8]
![alt text][image9]
![alt text][image10]

For this function all the parameters were same as used while training the classifier. I used colorspace 'YCrCb', orientations = 10, pixels per cell = 8 and cell per block = 2 and HOG channel = 'ALL'.

---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](https://github.com/akhilumat/Self-Driving-Car-Term-1-Project5-Vehicle-detection-and-Tracking/blob/master/FinalOutput_Video.mp4)

#### 2. Describe how you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

To avoid false positives and combine overlapping boxes I used three fuctions. Firstly I took an image and defined a zero template with same size of image and one channel. Then I used add heat fuction to add 1 to pixel values for each bounding box. Then applied threshhold to avoid false detections. Then from scipy.ndimage.measurements I used label() function to find out how many cars are in the image and which pixel belongs to which car. Then this label values and image was made input to draw labeled boxes fuction, which drew separate boxes around each car. Heatmaps and Car detections for some images are shown below.

![alt text][image11]

![alt text][image12]

#### 3. Defining Pipeline for video

While defining pipeline for the video, there was issue of missing boxes in few frames. So to avoid this issue, I defined a deque variable heatmaps whose maximum length was set to be 50. This variable was used to store heatmaps of 50 frames and there value were added and made input to draw labeled boxes function. Threshhold value was set as 25 to avoid false detections in the video stream. After applying this improvement missing boxes in the video stream was avoided. 
---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I faced issues while training the model. It took me lots of experimentations to find out which features to extract from the image. After doing lots of experimentations I finally achieved good testing accuracy for the model. I also faced problems to avoid false detections for which heatmaps were used. Finally to make the formation of boxes smooth I added 50 heatmaps using deque variable and applied threshhold of 25 to form boxes. The pipeline can sometimes fail to detect cars. This can be improved by augmenting the dataset and training the classifier on more images.  
