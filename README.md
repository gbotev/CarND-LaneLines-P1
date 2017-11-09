# **Finding Lane Lines on the Road** 

## The goals of this project are the following:
* Make a pipeline that finds lane lines on the road


[//]: # (Image References)

[image1]: ./examples/canny.png "Canny"
[image2]: ./examples/lines.png "Lines"

---

### 1. Pipeline

My pipeline consisted of 2 main logical steps which are defined as separate functions:

* Detecting Canny Edges
* Deriving two lines that limit the current lane 

To perform the first step I needed to:
1. Convert the image from 3 channel to a single channel (gray) image
2. Blur it in order to smooth out pixel artefacts and other imperfections
3. Detect the edges
4. Filter out the pixels from regions that are not important and return only the "important" edges

![alt text][image1]

For the second step I needed to:
1. Find out good values for the parameters of Hough Line Transform and detect the lines
2. Draw just two lines on the image

![alt text][image2]

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by selecting only lines with slope between 0.5 and 1 for left lane and -0.5 and -1 for right. Then I averaged all the coefficients for beginnings and ends of lines and got the end points of a single line. After that I derived the equation for this line and found the X coordinates for Y at bottom and two-thirds of the image, so I can draw a line between these points.


### 2. Shortcomings

One potential shortcoming is that on certain frames there are no lines detected.
Another shortcoming is that different positions on car, different cameras and even resolutions can cause the detection to be different when some of the parameters are hardcoded.
Other problems can arise on different road conditions - night, no lane markings.
Curving roads are also a problem as we are considering only relatively straight roads.
Plain averaging of the detected coordinates leads to "jumpy" lines.
If there is a car in front of us it would cause problems to our current pipeline - from adding additional lines to obscuring our road view.
When changing lanes there would be three lines to consider and our pipeline allows for only two.


### 3. Possible improve ments

Possible improvements to our pipeline can be:
* Using the stream instead of separate frames to get smoother results (e.g. Kalman filter to smooth out our lines prediction and fix problems with no lines detected)
* Make the pipeline as independent from the camera model and mounting point as possible. This can include camera calibration, using ratios instead of hard-coded values which I tried to do in few cases, but not everywhere.
* Use some more advanced methods for drawing lines including a better interpolation function and allowance for curving lines.
* Think more about the cases when there is a car obscuring the lanes or what happens when we switch lanes.


### 4. Problems and bugs

When no lines are detected this leads to an error in trying to get the mean from the np.array of coordinates. Currently this is solved with an exception. It also can lead to undefined behaviour when trying to draw a line, thus I have clamped the possible pixel coordinates when drawing a line to max of 10000.