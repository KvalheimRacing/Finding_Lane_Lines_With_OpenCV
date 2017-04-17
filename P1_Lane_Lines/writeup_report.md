# Project Finding Lane Lines

## Introduction

What a time to be alive! The year is 2017, Tesla is soon to realease model 3 and autonomous vehicles are all the rage. Still at its infancy, the winning solution to dominate the mass production of autonomous vehicles are ongoing. 
The two main factions currently are the robotics approach and the end-to-end neural networks approach. Like the four seasons, the AI winter has come and gone. It's Spring and this is the story of one man's attempt to explore the pros and cons of the end-to-end neural networks faction in a controlled environment. The hope is to draw some conclusions that will help the greater community advance as a whole.

## How I did it

<div align="center">
   <br>
  <img src="test_images_output\Processed_edge_img_solidYellowLeft.jpg"><br><br>
</div>


### Pipeline
1) Convert image to grayscale
   * Like all the functons under, this comes straight out of openCV
2) Convert from RGB to HSV color space
   * HSV separates luma, (image intensity), from chroma (the color information). This means that it's easier for us to detect the dark yellow lines.
3) Apply a low pass filter, Gaussian kernel
   * This helps getting rid of noisy parts of the image which makes the next steps more reliable
4) Run a Canny edge function
   * This step detects edges in the image with the help of the gradient and hysteris
5) Apply a ROI (region of interest) and mask away the undesired regions.
   * See the variable "vertices" [below](WRITEUP.md#Parameters)
6) Retrieve probabilistic Hough lines and make a lane filling fromline segments
   * Here I also make a history of lines, and averiges all the different lines I get from the hough function
7) Merge the lane filling with the original image
   * Using an addWheighted() function, see [`α` `β` `λ` below](WRITEUP.md#Parameters)



 (see [here](https://en.wikipedia.org/wiki/Canny_edge_detector) for more details)
Use hough transformation to find lines from the edges
Transforms each point to a line in hough space where the intersection of these lines shows the presence of a line in image space (see [here](https://en.wikipedia.org/wiki/Hough_transform))


Separate the line segments into Left and Right buckets, and fit a single line to each bucket of points

Test Image             |  Gray
:-------------------------:|:-------------------------:
![input](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_images/solidYellowLeft.jpg)|![gray](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_images_output/Processed_gray_img_solidYellowLeft.jpg)
Blur                   |  Edges
![blur](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_images_output/Processed_blur_img_solidYellowLeft.jpg)|![canny](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_images_output/Processed_edge_img_solidYellowLeft.jpg)
Masked                  |  Hough Lines
![masked](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_images_output/Processed_maskd_img_solidYellowLeft.jpg)|![hough](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_images_output/Processed_hough_img_solidYellowLeft.jpg)
Lane                    | Result
![lane](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_images_output/Processed_lane_img_solidYellowLeft.jpg)|![finiced](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_images_output/Processed_solidYellowLeft.jpg)

### Solid White
[![solidWhiteRight](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_videos_output/solidWhiteRight.gif)](https://www.youtube.com/watch?v=W81-SgZLCMQ&feature=youtu.be)

### Solid Yellow
[![solidYellowLeft](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_videos_output/solidYellowLeft.gif)](https://www.youtube.com/watch?v=ZocZa1NPjHo&feature=youtu.be)

### Challenge
[![challenge](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_videos_output/challenge14.gif)](https://www.youtube.com/watch?v=mrqkV6bfFDA&feature=youtu.be)

### Challenge
[![challenge 2](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_videos_output/challenge16.gif)](https://www.youtube.com/watch?v=rM0yoBgP2O0&feature=youtu.be)



(see the [Jupyter Notebook](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/P1_Lane_Lines.ipynb) for more details))

# Parameters

* `α = 0.9`                  Weight factor for initial image
* `β = 0.4`                  Weight factor for new image
* `λ = 0.22`                 Scalar added to each sum (of new and initial image), see weighted_img function
* `kernel_size = 7`          Size of the n x n (2D) matrix used as kernel in gaussian blur. Must be an odd positive integer.
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
* `H = img.shape[0]`         Getting the hight of the image
* `Hr = H*0.6`               Reducing the hight
* `W = img.shape[1]`         Getting the width of the image
* `ly = np.array([20, 100, 100], dtype = "uint8")`  Low lalue for yellow HSV.
* `uy = np.array([30, 255, 255], dtype = "uint8")`  Hig value for yellow HSV.



# Reflections

> This bring me to the other rule of thumb, **every problem will have tradeoffs**. The question becomes what are the tradeoffs you are willing to make? This will depend on the case you are solving!

to counter for jumping, I made a history of lines ant took the average
create a buffer of slope and y-intercept values for the last N frames and to overlay a line whose parameters are a rolling mean of these values. This should smooth out the jitter and give the pipeline a "memory" so that if no line segments are detected, it could continue to overlay the last calculated line until another is found.

Convert RGB to [HSV](https://en.wikipedia.org/wiki/HSL_and_HSV) and work with that for getting stronger yellow area in challenge video, then do canny. but that is a hard coded approatch...  

The algorithm I created is likely overfitted to the data available in this project

could averiging over more hough lines by editing thresholds, but that would result in performance loss from tre algorithm.

While it's cool that the code is able to detect lines, this approach is full of compromises and I would not ever use it in real life! :-)


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

a deep learning model?


# conclutions for future work

there are a few things I’d like to improve on.

Implement GUI for parameter tuning in this [article](https://medium.com/@maunesh/finding-the-right-parameters-for-your-computer-vision-algorithm-d55643b6f954)

If you enjoyed this post, please hit recommend! Follow me on Medium to know about my future projects.
This is great — I’ll have a chance to work on this challenge in Project 4.
Stay tuned for more self-driving car awesomeness in the future!

Went mental on one of the last challenge videos of udacitys p4

I will work on a more advanced lane finding algorithm later on in May, so I will look into some papers regarding taking it to the nest level, see the links below

Acknowledgement
Thanks to Udacity staff for giving me the oppurtunity to begin a new journey in my life with top-talented professionals and students all over the world learning and researching for SDC technologies.

- [Robust And Real Time Detection Of Curvy Lanes With Desired Slopes For Driving Assistance And Autonomous Vehicles](https://arxiv.org/ftp/arxiv/papers/1501/1501.03124.pdf)
- [Real time Detection of Lane Markers in Urban Streets](http://www.vision.caltech.edu/malaa/publications/aly08realtime.pdf)
- [Lane detection and tracking using B-snake](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.106.6644&rep=rep1&type=pdf)

# References & Resources
1. [Comma.ai steering model](https://github.com/commaai/research/blob/master/train_steering_model.py)
2. [Canny edge detection](https://en.wikipedia.org/wiki/Canny_edge_detector)
3. [Hough transform](https://en.wikipedia.org/wiki/Hough_transform)
4. [HSV](https://en.wikipedia.org/wiki/HSL_and_HSV)
