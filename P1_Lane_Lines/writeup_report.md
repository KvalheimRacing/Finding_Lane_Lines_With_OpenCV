# Project Finding Lane Lines

## Introduction

When you drive, you use your eyes to observe your surroundings. The lines on the road show you where the lanes are, and they act as a constant reference for where to steer the vehicle. Naturally, one of the first things we would like to do in developing a self-driving car is to automatically detect lane lines using an algorithm.

<div align="center">
   <br>
  <img src="test_videos_output\solidWhiteRight.gif"><br><br>
</div>

This project is the first among several projects in the [Self Driving Car Engineer](https://www.udacity.com/course/self-driving-car-engineer-nanodegree--nd013) program at Udacity. Here we learn cutting edge technology equipping us with the tools for a career in the field of Self Driving Cars. Udacity calls it a "Nanodegree", but it lasts over 9 months and with all the hours I am putting into this, it really becomes a full education for me. So that brings me to this project, which was so much fun I just had to stop myself, I could go on forever, there is always something to do better, and so much good Inspiration!

With that said, below is a brief explanation of the pipeline which you can find [here](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/P1_Lane_Lines.ipynb). 


### Pipeline
1) Convert image to gray-scale with [`cv2.cvtColor(img, cv2.COLOR_RGB2GRAY)`](http://docs.opencv.org/2.4.8/modules/imgproc/doc/miscellaneous_transformations.html#cvtcolor)
   * Like all the functions under, this comes straight out of openCV
2) Convert from RGB to HSV color space with [`cv2.cvtColor(img, cv2.COLOR_RGB2HSV)`](http://docs.opencv.org/2.4.8/modules/imgproc/doc/miscellaneous_transformations.html#cvtcolor)
   * HSV separates luma, (image intensity), from chroma (the color information). This means that it can be easier for us to detect dark yellow lines.
3) Apply a low pass filter, Gaussian kernel -> [`cv2.GaussianBlur()`](http://docs.opencv.org/2.4.8/modules/imgproc/doc/filtering.html#gaussianblur)
   * This helps getting rid of noisy parts of the image which makes the next steps more reliable
4) Run a Canny edge function -> [`cv2.Canny()`](http://docs.opencv.org/2.4/modules/imgproc/doc/feature_detection.html?#canny)
   * This step detects edges in the image with the help of the gradient and hysteresis
5) Apply a ROI (region of interest) and mask only what we want, by using [`cv2.fillPoly()`](http://docs.opencv.org/2.4.8/modules/core/doc/drawing_functions.html#fillpoly) and [`cv2.bitwise_and()`](http://docs.opencv.org/2.4.8/modules/core/doc/operations_on_arrays.html#bitwise-and) 
   * See the variable [vertices](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/P1_Lane_Lines.ipynb) for more details
6) Retrieve probabilistic Hough lines and make a lane filling from line segments, with [`cv2.HoughLinesP()`](http://docs.opencv.org/2.4/modules/imgproc/doc/feature_detection.html?#houghlinesp) ++
   * Separating the lines into left and right lines respectively
   * Here I also make a history of lines, separate and averages all the different lines I get from the Hough function
7) Merge the lane filling with the original image
   * Using an [`cv2.addWeighted()`](http://docs.opencv.org/2.4.8/modules/core/doc/operations_on_arrays.html#addweighted) function, see [`α β λ`](writeup_report.md#parameters) below

***

Input Image             |  1.Gray
:-------------------------:|:-------------------------:
![input](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_images/solidYellowLeft.jpg)|![gray](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_images_output/Processed_gray_img_solidYellowLeft.jpg)
3.Gaussian Blur                   |  4.Edges from Canny
![blur](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_images_output/Processed_blur_img_solidYellowLeft.jpg)|![canny](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_images_output/Processed_edge_img_solidYellowLeft.jpg)
5.Masked only ROI                  |  6.Hough Lines
![masked](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_images_output/Processed_maskd_img_solidYellowLeft.jpg)|![hough](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_images_output/Processed_hough_img_solidYellowLeft.jpg)
6.Lane made by Hough Lines                    | 7.Result
![lane](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_images_output/Processed_lane_img_solidYellowLeft.jpg)|![finiced](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_images_output/Processed_solidYellowLeft.jpg)


> You can find the rest of the pictures [here](https://github.com/KvalheimRacing/CarND/tree/master/P1_Lane_Lines/test_images_output)
The final test of the algorithm was on the videos below. I spent some time tuning the parameters, especially how many lanes which gets stored in `hist_frames`. Found out that the ideal value was around 10-20. I also tried a lot of parameters on the Canny function and the Hough transform from other solutions, but with no luck of further improving the result shown here.


### Solid Yellow
[![solidYellowLeft](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_videos_output/solidYellowLeft.gif)](https://www.youtube.com/watch?v=ZocZa1NPjHo&feature=youtu.be)

### Challenge
[![challenge](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_videos_output/challenge14.gif)](https://www.youtube.com/watch?v=mrqkV6bfFDA&feature=youtu.be)

### Challenge
[![challenge 2](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_videos_output/challenge16.gif)](https://www.youtube.com/watch?v=rM0yoBgP2O0&feature=youtu.be)

> After some time, I managed to get the middle challenge movie over done with a ok result. It struggles in the middle there because of sudden change in light conditions, so I turned up `hist_frames` to about 30, and it didn't bother that it didn't find lanes in that short period of time (because of the memory). There was one more challenge [video](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_videos_output/challenge18.gif) (not for this project, but for the advanced lane lines project in the end of May), where the algorithm had no chance, I will talk about this in the reflections below.



# Parameters

* `α = 0.9`                  Weight factor for initial image
* `β = 0.4`                  Weight factor for new image
* `λ = 0.22`                 Scalar added to each sum (of new and initial image), see weighted_img function
* `kernel_size = 7`          Size of the n x n (2D) matrix used as kernel in Gaussian blur. Must be an odd positive integer.
* `low_threshold = 50`       Value for the canny function, defining the first threshold - lower hysteresis bound.
* `high_threshold = 150`     Value for the canny function, defining the second threshold - upper hysteresis bound.
* `hist_frames = 10`         History of how many frames back to remember lane lines
* `rho = 1`                  Distance resolution in pixels of the Hough grid
* `theta = np.pi/90`         Angular resolution in radians of the Hough grid
* `threshold = 15`           Minimum number of votes (intersections in Hough grid cell)
* `min_line_length = 70`     Minimum number of pixels in a line
* `max_line_gap = 180`       Maximum gap in pixels between connectable line segments
* `min_slope_value = 0.4`    Defining the minimum slope value. Lines under this value is not lane lines.
* `left_line_stack = []`     For keeping a stack of left lines
* `right_line_stack = []`    For keeping a stack of right lines
* `left_line_history = []`   For keeping a history of left lines, if there is no left lines
* `right_line_history = []`  For keeping a history of right lines, if there is no right lines
* `H = img.shape[0]`         Getting the height of the image
* `Hr = H*0.6`               Reducing the height
* `W = img.shape[1]`         Getting the width of the image
* `ly = np.array([20, 100, 100], dtype = "uint8")`  Low lalue for yellow HSV.
* `uy = np.array([30, 255, 255], dtype = "uint8")`  High value for yellow HSV.



# Reflections

It's cool that an algorithm I made detect road lanes, but this approach is full og comprimises and I would not use it before a lot more situations has been accounted for.

That brings us on to the;

## Shortcommings:

* Memory for remembering lane lines to counter jittering is good, but can make the algorithm slow (tuning of `hist_frames` is important).
* I could be averiging over more Hough lines by adjusting the thresholds, but that would result in performance loss from the algorithm (more time to compute).
* The algorithm is only looking for straight lines, would therfore have a problem:
   * In thight/sharps turns (and turns generally).
   * At crossroads, if you are going to the left or right (has no logic for this)
   * In roundabouts
* The algorithm is hard coded after some colour values. This means:
   * Would probably have a hard time at nigt, in fog, rain, but most of all snow! (and ofc in sandstorms on the highway in Dubai;)
   * Collapses where there is no lane markings over a long period of time, and no contrasts dividing the two lanes. This is typically urban roads and country roads.
   * It is porbably overfitted to the data availabe in this project
   * It is sensitive to shadows+sunlight, over a long stretch that would be a problem.
* A problem with the method of only front camera mounted like this is when the sun hits right in the lens. The algorithm would collapse totally
* You would also have problem while changing road lanes (has no logic for this), algorithm would collapse.
* The biggest one yet; ROI is hard coded!
   * Could easily mistake a triangular hill in the horizon for a lane
   * Algorithm will fail if the car is not in the middle of the road
   * Obstructions like cars, which can be in the lane, and have lines which looks simmilar to the ones the algorithm is loking for.

As a cosequence of all these shortcomings, we can see that the algorithm goes bananas on one of the last challenge videos of P4, this is not unexpected and kind of funny ^^, 


## The good things

* Using HSV gave me a more accurate value for yellow, boosted the "visability" some, just what I needed to get the little extra, for handeling the challenge video.
* The memory part was nessesary and good
* The algorithm is simple and short, easy to work with.
* Got the Canny and Hough function working well on the required videos  


> This brings me to a classic rule of thumb, **every problem will have tradeoffs**. The question becomes what are the tradeoffs you are willing to make? This will depend on the case you are solving, and here we have a super simple algorithm, we can't expect it to handle all of these hard situations described over only on a couple of lines of code. Sometimes situations are more complex, like in the picture below:

<div align="center">
   <br>
  <img src="..images\uk.jpg"><br><br>
</div>



# Conclutions for future work

There will always be hard situations, but these are a few things I’d like to improve in the first place:

## Cool stuff:

* Implement GUI for parameter tuning, got inspired by [this](https://medium.com/@maunesh/finding-the-right-parameters-for-your-computer-vision-algorithm-d55643b6f954) article
* or Implement Deep Learning for either parameter tuning, or choosing optimal hough lines.
* Construct a 3d image/depth image by modeling scene constraints. This will help in making a 3D environment around the car, and could be used for vheicle/object detection.
 
## Important stuff:


* Hard-Coding ROI is a major fault, the lane detection ROI, must be flexible. I could use object recognition to detect cars, pedestrians, bocyclist etc, and to detect the road as ROI. Then I could reject lines belonging to other recognised objects (cars, people etc).
* Defining a prosedure to choose between multiple lanes (identify all the lanes (in a lane class?), and from turn signal choose lane).
* Rather than fitting a line, fitting a curve with a higher degree polynomial will provide a more accurate set of lane lines for curved and sharp turns.



Another udacity student, [Kirill Danilyuk](https://github.com/Kidra521/carnd/blob/master/p1_lane_lines_detection/P1.ipynb) has a very good solution implementing classes for lane lines, I think this is a good inspiration for everyone that is considering this approach. So I will definetly be checking out that code with regards to ROI.

***

I will work on a more advanced lane finding algorithm later on in May, so I will look into some papers regarding taking it to the nest level, specifically these ones;

> [Robust And Real Time Detection Of Curvy Lanes With Desired Slopes For Driving Assistance And Autonomous Vehicles](https://arxiv.org/ftp/arxiv/papers/1501/1501.03124.pdf)

> [Real time Detection of Lane Markers in Urban Streets](http://www.vision.caltech.edu/malaa/publications/aly08realtime.pdf)

> [Lane detection and tracking using B-snake](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.106.6644&rep=rep1&type=pdf)

Kepping in mind that the two challenge videos here are from the advanced lane finding project, I feel like having a good starting point when the time comes for that project.
A good starting point, and a lot of awesome stuff to try, this will be so cool!

### Acknowledgement
Thanks to Udacity for giving me the oppurtunity to begin a new journey in my life with top-talented professionals and students all over the world learning and researching on SDC technologies.

# References & Resources
1. [Udacity course material](https://github.com/KvalheimRacing/CarND/tree/master/demos)
2. [Canny edge detection](https://en.wikipedia.org/wiki/Canny_edge_detector)
3. [Hough transform](https://en.wikipedia.org/wiki/Hough_transform)
4. [HSV](https://en.wikipedia.org/wiki/HSL_and_HSV)
5. [Gallen Ballew - for awesome HSV parameters!](https://github.com/galenballew/SDC-Lane-and-Vehicle-Detection-Tracking/blob/master/Part%20I%20-%20Simple%20Lane%20Detection/P1.ipynb)

If you enjoyed this post, please star, like or recommend! :) Follow me on [Medium](https://medium.com/@eirikkvalheim) and [Git](https://github.com/KvalheimRacing) to stay tuned for more self-driving car related awesome stuff! 
