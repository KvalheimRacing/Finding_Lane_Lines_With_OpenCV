# Project Finding Lane Lines

## Introduction

When you drive, you use your eyes to observe your surroundings. The lines on the road show you where the lanes are, and they act as a constant reference for where to steer the vehicle. Naturally, one of the first things we would like to do in developing a self-driving car is to automatically detect lane lines using an algorithm.

<div align="center">
   <br>
  <img src="test_videos_output\solidWhiteRight.gif"><br><br>
</div>

This project is the first among several projects in the [Self Driving Car Engineer](https://www.udacity.com/course/self-driving-car-engineer-nanodegree--nd013) program at Udacity. Here we learn cutting edge technology equipping us with the tools for a career in the field of Self Driving Cars. Udacity calls it a "Nanodegree", but it lasts over 9 months and with all the hours I am putting into teaching myself this, it really becomes a full education for me. So that brings me to this project, which was so much fun I just had to stop myself, I could go on forever, there is always something to do better, and so much good Inspiration!

With that said, below is a brief explanation of the pipeline which you can find [here](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/P1_Lane_Lines.ipynb). 


### Pipeline
1) Convert image to gray-scale
   * Like all the functions under, this comes straight out of openCV
2) Convert from RGB to HSV color space
   * HSV separates luma, (image intensity), from chroma (the color information). This means that it's easier for us to detect dark yellow lines.
3) Apply a low pass filter, Gaussian kernel
   * This helps getting rid of noisy parts of the image which makes the next steps more reliable
4) Run a Canny edge function
   * This step detects edges in the image with the help of the gradient and hysteresis
5) Apply a ROI (region of interest) and mask only what we want
   * See the variable [vertices](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/P1_Lane_Lines.ipynb) for more details
6) Retrieve probabilistic Hough lines and make a lane filling from line segments
   * Separating the lines into left and right lines respectively
   * Here I also make a history of lines, separate and averages all the different lines I get from the Hough function
7) Merge the lane filling with the original image
   * Using an addWheighted() function, see [`α β λ`](writeup_report.md#parameters) below

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

> After some time, I managed to get the middle challenge movie over done with a ok result. It struggles in the middle there because of sudden change in light conditions, so I turned up `hist_frames` to about 30, and it didn't bother that it didn't find lanes in that short period of time (because of the memory). There was one more challenge [video](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_videos_output/challenge18.gif) (not for this project, but for the advanced lane lines project in the end of may), where the algorithm had no chance, I will talk about this in the reflections below.



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

Considering this algorithm only takes account for straight lines, it's ok.


> This bring me to the other rule of thumb, **every problem will have tradeoffs**. The question becomes what are the tradeoffs you are willing to make? This will depend on the case you are solving!

to counter for jumping, I made a history of lines ant took the average
create a buffer of slope and y-intercept values for the last N frames and to overlay a line whose parameters are a rolling mean of these values. This should smooth out the jitter and give the pipeline a "memory" so that if no line segments are detected, it could continue to overlay the last calculated line until another is found.


The algorithm I created is likely overfitted to the data available in this project

could averiging over more hough lines by editing thresholds, but that would result in performance loss from tre algorithm.

While it's cool that the code is able to detect lines, this approach is full of compromises and I would not ever use it in real life! :-)

* With increasing the memory, the algorithm became slow and not really usable.

hard coded after colors

The lane detection region of interest (ROI), must be flexible ( tight turns and bumper to bumper traffic) - use objects and detect the road to use it as ROI

test on heavy traffic on the highway
Driving at night.
Or, it may fail in bad weather, such as rain or snow fog. 
roads without lanes (country roads or urban roads.)
shadows

Using HSV gave me a more accurate value for yellow,, and boosted it some.

not in the middle of the road


Obstructions: car within the ROI may be interpreted as a lane 

Sharp curves and hills 
If the car were to change lanes,  the results would be chaotic during the lane change
Defining a procedure to detect a lane change 

Hard-Coding ROI is a major fault.

Another issue to consider is performance. I got the best lane estimates by lowering the Hough threshold and averaging over more potential line candidates. However, this comes at a performance cost. As it stands, this pipeline is about 1:1 with real time and takes approximately 1/30s to process per frame. This performance estimate comes from the magic %time functionality in Jupyter notebooks.

Rather than fitting a line, fitting a curve with a higher degree polynomial might provide a more accurate set of lane lines for sharp curves.

Attemt to reconstruct 3d image (at lease depth of lanes) by modeling scene constraints (lanes are equally spaced, plane is roughly horizontal, etc.)

Use object recognition to reject lines belonging to recognised objects 




# Conclutions for future work

there are a few things I’d like to improve on.

* Implement GUI for parameter tuning, got inspired by [this](https://medium.com/@maunesh/finding-the-right-parameters-for-your-computer-vision-algorithm-d55643b6f954) article
* or Implement Deep Learning for either parameter tuning, or choosing optimal hough lines.
*

If you enjoyed this post, please hit recommend! Follow me on Medium to know about my future projects\ 
This is great — I’ll have a chance to work on all of this in the advanced lane finding project.
Stay tuned for more self-driving car awesomeness in the future!

Went mental on one of the last challenge videos of udacitys p4

I will work on a more advanced lane finding algorithm later on in May, so I will look into some papers regarding taking it to the nest level, specifically these ones;

> [Robust And Real Time Detection Of Curvy Lanes With Desired Slopes For Driving Assistance And Autonomous Vehicles](https://arxiv.org/ftp/arxiv/papers/1501/1501.03124.pdf)
[Real time Detection of Lane Markers in Urban Streets](http://www.vision.caltech.edu/malaa/publications/aly08realtime.pdf)
[Lane detection and tracking using B-snake](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.106.6644&rep=rep1&type=pdf)

### Acknowledgement
Thanks to Udacity for giving me the oppurtunity to begin a new journey in my life with top-talented professionals and students all over the world learning and researching SDC technologies.

# References & Resources
1. [Udacity course material](https://github.com/KvalheimRacing/CarND/tree/master/demos)
2. [Canny edge detection](https://en.wikipedia.org/wiki/Canny_edge_detector)
3. [Hough transform](https://en.wikipedia.org/wiki/Hough_transform)
4. [HSV](https://en.wikipedia.org/wiki/HSL_and_HSV)
ll of this 
