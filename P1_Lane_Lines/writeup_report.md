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

1. Get started
   * Init needed files
2. Draw conclutions for future work

# Hyperparameters

* Learning rate: 1e-1 (very aggressive!)
* Batch size: 128 (tried 64, 128, 256, 1024)
* Adam optimizer

> This bring me to the other rule of thumb, **every problem will have tradeoffs**. The question becomes what are the tradeoffs you are willing to make? This will depend on the business case you are solving!

# References
1. [Comma.ai steering model](https://github.com/commaai/research/blob/master/train_steering_model.py)
