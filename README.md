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
In this exercise, for the first time process is converting ROS message into PCL data. The function that I was used is ros_to_pcl functions.

The output of PCL data can be seen on the picture below :
<p align="center"> <img src="./images/01 Original PCD.jpg"> </p>

I try to saved the PCD data in images folder on this project with named Original_pcl.pcd. This file is very larged because this is a full resolution of point cloud data. The size of file is about 29.1 MB. Doing computation using this PCD would be slower and difficult to get fast time processing. Therefore, the first time preprocessing is downsampled the voxel data. Downsample the data depend with the size of LEAF_SIZE. Value of LEAFSIZE must be consider betweed information and compression size. We need the minimum value of LEAFSIZE that would be great compression without lose information. In this exercise, LEAF_SIZE that we used is 0.005. It is difficult to determined the optimal value, but using LEAF_SIZE 0.005 is good enough, half of file size and minimum loss of information. PCD file size become 18.1 MB.The output of downsampled PCD we can see on the picture below :
<p align="center"> <img src="./images/02 downsampled.jpg"> </p>

After we downsample the voxel data to reduce computing process, another method is The Pass Through Filter. The Pass Through Filter would be crop the Voxel data on target of interest only. Therefore dimension of voxel would be small and contain only information that we need. Applying a Pass Through filter along z axis (the height with respect to the ground) to our tabletop scene in the range 0.3 to 5 gives the result as image below :
<p align="center"> <img src="./images/03 Pass Throught Filter PCD.jpg"> </p>
PCD data only contain table and object list only.

Next in our perception pipeline, we need to remove the table itself from the scene. To do this, we implementing RANSAC (Random Sample Consensus).

<p align="center"> <img src="./images/04 Extracted Inlier.jpg"> </p>

From extracted_inlier, we can get extracted outlier as image below :
<p align="center"> <img src="./images/05 Extracted Outlier.jpg"> </p>

#### 2. Complete Exercise 2 steps: Pipeline including clustering for segmentation implemented.  

After We finished doing segmentation, now is the for doing clustering pipeline. In our exercise, we implement clustering using Euclidean Clustering or DBSCAN algorithm. This algorithm is a nice alternative to k-means when you don' t know how many clusters to expect in your data, but you do know something about how the points should be clustered in terms of density (distance between points in a cluster). 

#### 3. Complete Exercise 3 Steps.  Features extracted and SVM trained.  Object recognition implemented.
After we are doing clustering, we need to get SVM trained that we can get from sensor_stick capture_feature.py
Parameter that we used for generated SVM trained model is :
Histogram length = 32
Number of Trainee = 20
Image Type = HSV
SVM Classifier = linear

##### World 1 (3 Object)
Training output :
<p align="center"> <img src="./images/08 Train SVM.jpg"> </p>
Confused matrix for this trainee:
<p align="center"> <img src="./images/06 Confused Matrix.jpg"> </p>
And normalized confused matrix is:
<p align="center"> <img src="./images/07 Normalized Confused Matrix.jpg"> </p>

##### World 2 (5 Object)
Training output :
<p align="center"> <img src="./images/11 Train SVM.jpg"> </p>
Confused matrix for this trainee:
<p align="center"> <img src="./images/09 Confused Matrix.jpg"> </p>
And normalized confused matrix is:
<p align="center"> <img src="./images/10 Normalized Confused Matrix.jpg"> </p>

##### World 1 (8 Object)
Training output :
<p align="center"> <img src="./images/15 Train SVM.jpg"> </p>
Confused matrix for this trainee:
<p align="center"> <img src="./images/13 Confused Matrix.jpg"> </p>
And normalized confused matrix is:
<p align="center"> <img src="./images/14 Normalized Confused Matrix.jpg"> </p>

### Pick and Place Setup

#### 1. For all three tabletop setups (`test*.world`), perform object recognition, then read in respective pick list (`pick_list_*.yaml`). Next construct the messages that would comprise a valid `PickPlace` request output them to `.yaml` format.

##### World 1 (3 Object)
This configuration on pick_place_project.launch we define

rosparam command="load" file="$(find pr2_robot)/config/pick_list_1.yaml"

which looks like :
object_list:
  - name: biscuits
    group: green
  - name: soap
    group: green
  - name: soap2
    group: red

The recognition output we can see in below picture :
<p align="center"> <img src="./images/00 World 1.jpg"> </p>

Target recognition output is 100% (3/3) objects in test1.world
YAML File: [Link!](https://github.com/oktantod/RoboND-Perception-Project/blob/master/output_1.yaml)

##### World 2 (5 Object)
This configuration on pick_place_project.launch we define

rosparam command="load" file="$(find pr2_robot)/config/pick_list_2.yaml"

which looks like :
object_list:
  - name: biscuits
    group: green
  - name: soap
    group: green
  - name: book
    group: red
  - name: soap2
    group: red
  - name: glue
    group: red

The recognition output we can see in below picture :
<p align="center"> <img src="./images/12 World 2.jpg"> </p>

Target recognition output is 100% (5/5) objects in test2.world
YAML File: [Link!](https://github.com/oktantod/RoboND-Perception-Project/blob/master/output_2.yaml)

##### World 3 (8 Object)
This configuration on pick_place_project.launch we define

rosparam command="load" file="$(find pr2_robot)/config/pick_list_3.yaml"

which looks like :
object_list:
  - name: sticky_notes
    group: red
  - name: book
    group: red
  - name: snacks
    group: green
  - name: biscuits
    group: green
  - name: eraser
    group: red
  - name: soap2
    group: green
  - name: soap
    group: green
  - name: glue
    group: red

The recognition output we can see in below picture :
<p align="center"> <img src="./images/16 World 3.jpg"> </p>

Target recognition output is 100% (8/8) objects in test3.world
YAML File: [Link!](https://github.com/oktantod/RoboND-Perception-Project/blob/master/output_3.yaml)
