# Finding Lane Lines


What a time to be alive! The year is 2017, Tesla is soon to release **Model 3**, revolutionize transport, and autonomous vehicles are the big focus of every major car manufacturer. The race for a winning solution to dominate the mass production of autonomous vehicles are on. 
The two main factions currently are the robotics approach and the end-to-end neural networks approach. 

Like the four seasons, the AI winter has come and gone. It's Spring and this is the story of one man's attempt to explore the pros and cons of the end-to-end neural networks faction in a controlled environment. The hope is to draw some conclusions that will help the greater community advance as a whole.

<div align="center">
   <br>
  <img src="test_videos_output\solidYellowLeft.gif"><br><br>
</div>

> This project features code for finding the driver lane in pictures or video stream. Check out the **[writeup_report](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/writeup_report.md)** file for a detailed report on this project.
The project requirements made by Udacity can be found *[here](https://review.udacity.com/#!/rubrics/322/view)*

##
## The goals of this project were:

1. Get to know OpenCV (I really took the time to read up on the functions used).
2. Use Hough transform to successfully identify the road lane.
3. Create a good written report and discuss the methodology as well as results.


## The following are included:

* [P1_Lane_Lines.ipynb](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/P1_Lane_Lines.ipynb) containing the only code (`Python 3.5`) needed for the algorithm.
* [Test images](https://github.com/KvalheimRacing/CarND/tree/master/P1_Lane_Lines/test_images) for testing the algorithm.
* [Output images](https://github.com/KvalheimRacing/CarND/tree/master/P1_Lane_Lines/test_images_output) from the algorithm.
* [Test_videos](https://github.com/KvalheimRacing/CarND/tree/master/P1_Lane_Lines/test_videos_output) for testing the algorithm.
* [Output videos](https://github.com/KvalheimRacing/CarND/tree/master/P1_Lane_Lines/test_videos_output) from the algorithm.
* [Examples](https://github.com/KvalheimRacing/CarND/tree/master/P1_Lane_Lines/examples) from Udacity, setting the standard.
* A **[report](https://github.com/KvalheimRacing/CarND/blob/master/P1_Lane_Lines/writeup_report.md)** summarizing the results and the project.
