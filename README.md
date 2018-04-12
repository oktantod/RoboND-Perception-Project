[![Udacity - Robotics NanoDegree Program](https://s3-us-west-1.amazonaws.com/udacity-robotics/Extra+Images/RoboND_flag.png)](https://www.udacity.com/robotics)

## Project: Perception Pick & Place

<p align="center"> <img src="./images/RoboND-Perception.jpg"> </p>

## [Rubric](https://review.udacity.com/#!/rubrics/1067/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### README

#### Provide a Writeup / README that includes all the rubric points and how to addressed each one. 

The procces of this exercise we can see in the image below :
<p align="center"> <img src="./images/Pipeline Process.jpg"> </p>

### Exercise 1, 2 and 3 pipeline implemented

#### 1. Complete Exercise 1 steps. Pipeline for filtering and RANSAC plane fitting implemented.
In this exercise, for the first time process is converting ROS message into PCL data. The function that I was used is ros_to_pcl functions.

The output of PCL data can be seen on the picture below :
<p align="center"> <img src="./images/01 Original PCD.jpg"> </p>

I try to saved the PCD data in images folder on this project with named Original_pcl.pcd. This file is very larged because this is a full resolution of point cloud data. The size of file is about 29.1 MB. Doing computation using this PCD would be slower and difficult to get fast time processing. Therefore, the first time preprocessing is downsampled the voxel data. Downsample the data depend with the size of LEAF_SIZE. Value of LEAFSIZE must be consider betweed information and compression size. We need the minimum value of LEAFSIZE that would be great compression without lose information. In this exercise, LEAF_SIZE that we used is 0.005. It is difficult to determined the optimal value, but using LEAF_SIZE 0.005 is good enough, half of file size and minimum loss of information. PCD file size become 18.1 MB.The output of downsampled PCD we can see on the picture below :
<p align="center"> <img src="./images/02 downsampled.jpg"> </p>

After we downsample the voxel data to reduce computing process, another method is The Pass Through Filter. The Pass Through Filter would be crop the Voxel data on target of interest only. Therefore dimension of voxel would be small and contain only information that we need. Applying a Pass Through filter along z axis (the height with respect to the ground) to our tabletop scene in the range 0.3 to 5 gives the result as image below :
PCD data only contain table and object list only.
<p align="center"> <img src="./images/03 Pass Throught Filter PCD.jpg"> </p>

Next in our perception pipeline, we need to remove the table itself from the scene. To do this, we implementing RANSAC (Random Sample Consensus). Because the target that would be segmented is the table, than we must define that type of model is plane (Sample Consesus Plane). After we define the model, than we must define maxdistance of the threshold. We dont wont the object except the table would be threshold. In this experiment, we define the value of max_distance is 0.015.

After we doing RANSAC Plan Fitting process, than we must doing extraction from RANSAC Plane Fitting output. When extracted process the argument negative is False, the PCD would only contain the table and object above the table would be removed. This process is Extracted inlier, and the output is an image below.

<p align="center"> <img src="./images/04 Extracted Inlier.jpg"> </p>

When extracted process the argument negative is True, the te PCD would contain the object and the table would be removed. This proccess is called Extracted Outlier, and the output is an image below:

<p align="center"> <img src="./images/05 Extracted Outlier.jpg"> </p>

#### 2. Complete Exercise 2 steps: Pipeline including clustering for segmentation implemented.  

After We finished doing segmentation, now is the for doing clustering pipeline. In our exercise, we implement clustering using Euclidean Clustering or DBSCAN algorithm. This algorithm can do nicely without knowing the number of cluster that must be defined. Therefore, it would be effective when applied for world1 with 3 object, world2 with 5 object, and world3 with 8 object. 

#### 3. Complete Exercise 3 Steps.  Features extracted and SVM trained.  Object recognition implemented.
After we are doing clustering, we need to get SVM trained that we can get from sensor_stick capture_feature.py

Object-list that we want to captured is :
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

It contain 8 object and each object would be captured randomly as much as 50 position. Therefore feature in training set would be contain 400 feature. Extracted feature is using HSV. To define the number of position and HSV, we can edit capture_feature.py.

##### capture_features.py File: [Link!](https://github.com/oktantod/RoboND-Perception-Project/blob/master/pr2_robot/scripts/capture_features.py)

Process computing histogram is doing in feature.py file. The length of histogram feature can be defined in bins value. On this exercise, length of histogram that used is 64. Please see feature.py on link below.

##### feature.py File: [Link!](https://github.com/oktantod/RoboND-Perception-Project/blob/master/pr2_robot/scripts/features.py)

Support Vector Machine (SVM) kernel that we are used is RBF. Another kernel that may used is linear or poly. The best performance for our exercise is RBF, therefore we used it.
##### train_svm.py File: [Link!](https://github.com/oktantod/RoboND-Perception-Project/blob/master/pr2_robot/scripts/train_svm.py)

The output of confusion matrix we can see as image below :
<p align="center"> <img src="./images/Confusion Matrix.jpg"> </p>

As we can see from confusion matrix, from 50 test, each object have misclassification maximum is 3. This feature generation and svm model is good enought to determined the object. The output model would be used for target recognition in world_1, world_2 and world_3. The accuracy of SVM you can see in the image bellow :
<p align="center"> <img src="./images/Accuracy SVM Trainee.jpg"> </p>


### Pick and Place Setup

#### 1. For all three tabletop setups (`test*.world`), perform object recognition, then read in respective pick list (`pick_list_*.yaml`). Next construct the messages that would comprise a valid `PickPlace` request output them to `.yaml` format.

In this exercise, we would test object recognition using model.sav that had been generated by the 8 object list earlier. Object recognition would be applied on World 1, World 2 and World 3, then write in yaml output file.

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

# Potentially improvement :
## 1. Filtering.
#### VoxelGrid Downsampling Filter, the number of LEAF_SIZE must be find which value that have smaller size but the accuracy still better
#### Pass Through Filtering, axis_min and axis_max is set manually to define region of interest
#### Ransac Plane Fitting, subset of point from the original cloud does inliers depends on the setting of max_distance. This value is set manually

## 2. Object Recognitions
#### In this exercise, model.sav generated from 8 object only. Taking 50 feature train each object would be enought. But, if the object have number very larged, it would be better if we train each object with higher number of train to get better accuracy.
