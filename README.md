[![Udacity - Robotics NanoDegree Program](https://s3-us-west-1.amazonaws.com/udacity-robotics/Extra+Images/RoboND_flag.png)](https://www.udacity.com/robotics)

## Project: Perception Pick & Place

<p align="center"> <img src="./images/RoboND-Perception.jpg"> </p>

## [Rubric](https://review.udacity.com/#!/rubrics/1067/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### README

#### Provide a Writeup / README that includes all the rubric points and how to addressed each one. 

### Exercise 1, 2 and 3 pipeline implemented

#### 1. Complete Exercise 1 steps. Pipeline for filtering and RANSAC plane fitting implemented.
In this exercise, for the first time process is converting ROS message into PCL data. The output of PCL data can be seen on the picture below :
<p align="center"> <img src="./images/01 Original PCD.jpg"> </p>

Original image have bigger size, running computation on a full resolution point cloud can be slow and may not yiel any improvement on result obtained using a more sparsely sampled point cloud. So, in many cases, it is advantageous to downsample the data. In particular, you are going to use a VoxelGrid Downsampling Filter to derive a point cloud that has fewer points but should still do a good job of representing the input point cloud as a whole.

In this exercise, LEAF_SIZE that we used is 0.005. The output of downsampled PCD we can see on the picture below :
<p align="center"> <img src="./images/02 downsampled.jpg"> </p>

Now we want remove useless data from our point cloud. Therefore we are implementing The Pass Through Filter as a cropping tool which allows us to crop any given 3D point cloud by specifying an axis with cut-off values along that axis. The region we allow to pass through, is often referred to as region of interest. Applying a Pass Through filter along z axis (the height with respect to the ground) to our tabletop scene in the range 0.3 to 5 gives the following result:
<p align="center"> <img src="./images/03 Pass Throught Filter PCD.jpg"> </p>


#### 2. Complete Exercise 2 steps: Pipeline including clustering for segmentation implemented.  

#### 3. Complete Exercise 3 Steps.  Features extracted and SVM trained.  Object recognition implemented.
Here is an example of how to include an image in your writeup.

![demo-1](https://user-images.githubusercontent.com/20687560/28748231-46b5b912-7467-11e7-8778-3095172b7b19.png)

### Pick and Place Setup

#### 1. For all three tabletop setups (`test*.world`), perform object recognition, then read in respective pick list (`pick_list_*.yaml`). Next construct the messages that would comprise a valid `PickPlace` request output them to `.yaml` format.

And here's another image! 
![demo-2](https://user-images.githubusercontent.com/20687560/28748286-9f65680e-7468-11e7-83dc-f1a32380b89c.png)

Spend some time at the end to discuss your code, what techniques you used, what worked and why, where the implementation might fail and how you might improve it if you were going to pursue this project further.  



