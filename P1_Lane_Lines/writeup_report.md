# Finding Lane Lines Project

## Introduction

What a time to be alive! The year is 2017, Donald Trump is president of the United States of America and autonomous vehicles are all the rage. Still at its infancy, the winning solution to dominate the mass production of autonomous vehicles are ongoing. 
The two main factions currently are the robotics approach and the end-to-end neural networks approach. Like the four seasons, the AI winter has come and gone. It's Spring and this is the story of one man's attempt to explore the pros and cons of the end-to-end neural networks faction in a controlled environment. The hope is to draw some conclusions that will help the greater community advance as a whole.

## How I did it

<div align="center">
   <br>
  <img src="./images/sim_image.png"><br><br>
</div>

The goals of this project are:

1. Create a pipeline in OpenCV to process video frames.
2. Use the Hough transform to identify lane lines on the road.
3. Create this written report to discuss the methodology and summarize the results.

1. Get started
   * Init needed files
2. Draw conclutions for future work

 Convert frame to grayscale
2) Create masks for yellow and white pixels
3) Apply a Gaussian smoothing

Convert RGB to grayscale
2. Apply a slight Gaussian blur
3. Perform Canny edge detection
4. Define a region of interest and mask away the undesired portions of the image
5. Retrieve Hough lines 
6. Apply lines to the original image

Use a gaussian kernel to filter the image
This helps in getting rid of noisy parts of the image which makes the next steps more reliable
Perform canny edge detection
This step basically detects the edges in the image with the help of the image gradient and hysteris (see [here](https://en.wikipedia.org/wiki/Canny_edge_detector) for more details)
Use hough transformation to find lines from the edges
Transforms each point to a line in hough space where the intersection of these lines shows the presence of a line in image space (see [here](https://en.wikipedia.org/wiki/Hough_transform))

Convert image to grayscale
Apply Gaussian blur
Apply Canny edge detection
Apply polygon mask to eliminate non relevant noise
Apply probabilistic Hough transform to detect line segments
Separate the line segments into Left and Right buckets, and fit a single line to each bucket of points

Test Image             |  Blurred
:-------------------------:|:-------------------------:
![](https://github.com/CYHSM/carnd/blob/master/CarND-LaneLines-P1/test_images/pipeline/original.jpg?raw=true)  |  ![](https://github.com/CYHSM/carnd/blob/master/CarND-LaneLines-P1/test_images/pipeline/blur.jpg?raw=true)



[below](WRITEUP.md#drawing-lines)

![Demo](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_videos_output/solidWhiteRight.gif)

![Demo2](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_videos_output/solidYellowLeft.gif)

### Video White
[![Video White](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/test_videos_output/solidWhiteRight.gif)](https://youtu.be/bajgiQuEc38)


(see [Jupyter Notebook](https://github.com/CYHSM/carnd/blob/master/CarND-LaneLines-P1/P1.ipynb) for more details)

# Hyperparameters

* Learning rate: 1e-1 (very aggressive!)
* Batch size: 128 (tried 64, 128, 256, 1024)
* Adam optimizer

- `kernel_size`, Gaussian blur in 2D with square kernel. Must be an odd positive integer.
- `lkernel_size`, Laplacian to enhance edges. Kernel size must be an odd positive integer.
- `canny_lower_threshold`, Canny edge detection, lower hysteresis bound.
- `canny_upper_threshold`, Canny edge detection, upper hysteresis bound.
- `rho`, distance resolution of the Hough accumulator in pixels.
- `theta`, angular resolution of the Hough accumulator in radians.
- `threshold`, Hough accumulator voting threshold for peak values.
- `min_line_length`, minimum line length to be considered by cv2.HoughLinesP().
- `max_line_gap`, maximum line length to be considered by cv2.HoughLinesP()

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

Implement GUI for parameter tuning in this [article](https://medium.com/@maunesh/finding-the-right-parameters-for-your-computer-vision-algorithm-d55643b6f954)

If you enjoyed this post, please hit recommend! Follow me on Medium to know about my future projects.

Acknowledgement
Thanks to Udacity staff for giving me the oppurtunity to begin a new journey in my life with top-talented professionals and students all over the world learning and researching for SDC technologies.


# References
1. [Comma.ai steering model](https://github.com/commaai/research/blob/master/train_steering_model.py)
