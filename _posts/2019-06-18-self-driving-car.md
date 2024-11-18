---
title:  "Self Driving Car"
categories: post
mathjax: true
---
![self_drivng](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Selfdriving_final/self_driving_car_gif.gif)
## Summary:
- The purpose of this project is to drive the simulation vehicle autonomously. Over the course of the path, the vehicle will meet a few traffic signals. When the traffic signal turns in red, the vehicle should stop in front of it. 

- The required tools/knowledge are
  - Python
  - OpenCV
  - Tensorflow
  - Keras
  - ROS (Robotic Operating System)

## Simulation result #1 (path following):
 [![Watch the video](https://img.youtube.com/vi/YuUtXkjyugA/hqdefault.jpg)](https://youtu.be/YuUtXkjyugA)
## Simulation result #2 (classifiation):
[![Watch the video2](https://img.youtube.com/vi/g31nLPbD8Ps/hqdefault.jpg)](https://youtu.be/g31nLPbD8Ps)

## Submission note:

Path following functionality is not working as expected when the camera is enabled. The issue does not occur when the camera is disabled.

To address potential latency issues, I adjusted the rospy rates for tl_detector to 5Hz and waypoint_updater to 15Hz. Additionally, I implemented a strategy to process only one image every three frames for classification. Unfortunately, these measures did not resolve the problem.

Troubleshooting the issue has been time-consuming. I've also reached out to Udacity to inquire about potential changes to the simulator.

Until a resolution is found, I can demonstrate the following:

Path following without camera: The path following algorithm works as expected when the camera is disabled. (See Simulation Result #1)
Traffic light detection: The classifier accurately detects red stop signals at steady state in manual mode. (See Simulation Result #2)

## Simulation environment:

- Python version:
```python
python version
2.7.12
```
- Keras version 
```python
python -c "import keras; print(keras.__version__)
2.0.8
```

## Procedure 
- At first, make sure the vehicle follows the path without classification (done without camera on)

 ```python
   def get_light_state(self, light):
        """Determines the current color of the traffic light

        Args:
            light (TrafficLight): light to classify

        Returns:
            int: ID of traffic light color (specified in styx_msgs/TrafficLight)

        """
        #print(light.state)
        #return light.state # testing to see if the vehicle stop at the closest stop light when it turns to red
        if(not self.has_image):
            self.prev_light_loc = None # what's this TODO
            #return False
            return False # return red (False:0)
        elif(0 < self.dist_2closest < 80):
            data_collection = 0
            cv_image = self.bridge.imgmsg_to_cv2(self.camera_image, "bgr8")
            if data_collection:
                
                path = os.getcwd()
                image_folder = path +'/sim_data'
                timestamp = datetime.utcnow().strftime('%Y_%m_%d_%H_%M_%S_%f')[:-3]
                image_filename = os.path.join(image_folder, timestamp+'_'+str(light.state))
                #print(image_filename)
                cv2.imwrite('{}.jpg'.format(image_filename),cv_image)
                print('closest_light_location:'+str(light.pose.pose.position.x))
                print('simulation(given)_classification:'+str(light.state))
            print('model classification return to tl_detector:'+str(self.light_classifier.get_classification(cv_image)))
            return self.light_classifier.get_classification(cv_image)
        else:
            return 4 #unknown
 ``` 

  ```python
                  image_filename = os.path.join(image_folder, timestamp+'_'+str(light.state))  
  ```

  ```python
   import os
   import csv
   
   def strip_end(text, suffix):
      if not text.endswith(suffix):
        return text
    return text[:len(text)-len(suffix)]
    
    
  with open("csv_write_test_00.csv","r+") as f:
    w = csv.writer(f, delimiter=',', lineterminator='\n')
    #print(w)
    for path,dirs,files in os.walk(r"C:\Users\SLEE194\Downloads\Bosch-TL-Dataset-master\data\tl-extract-train"):
        #print(path)
        #print(files)
        for filename in files:
            #print(path)
            
            start = filename.find('_')+1
            end = filename.find('.png')
            color = filename[start:end]
            color = strip_end(color,'left')
            color = strip_end(color,'straight')
            w.writerow([filename,color])
  
  ```

However, the classification with traffic light in simulation doesn't work well. 

The options which I had implemented are, 

a. (intermediate method) Use opencv color circle detection to detect only red circle to stop the vehicle at stop light. The rest of color of traffic light, set the traffic light state to unknown. This is a lot lighter algorithm so it may work while camera is turned on for classification. 

```python
#from styx_msgs.msg import TrafficLight
import rospy
import keras
import tensorflow as tf
import numpy as np
from tensorflow.contrib.layers import flatten
from sensor_msgs.msg import Image
#from styx_msgs.msg import TrafficLightArray, TrafficLight
import cv2
from keras.models import load_model
import h5py
from keras import backend as K
#from datetime import datetime
#import os
from datetime import datetime
import os

class TLClassifier(object):
    def __init__(self):
        
        model = None       
        self.idx = 0
        self.light_state = 4 #unkown
    def get_classification(self, image):
        """Determines the color of the traffic light in the image
        Args:
            image (cv::Mat): image containing the traffic light

        Returns:
            int: ID of traffic light color (specified in styx_msgs/TrafficLight)
        """
        while(self.idx < 4):
            print('camera shoot: '+ str(self.idx))
            
            if (self.idx == 0):

                bgr_image = cv2.medianBlur(image,3)
                hsv_image = cv2.cvtColor(bgr_image,cv2.COLOR_BGR2HSV)
                lower_red_hue_range = cv2.inRange(hsv_image, (0, 100, 100), (10, 255, 255))
                upper_red_hue_range = cv2.inRange(hsv_image, (160, 100, 100), (179, 255, 255))

                red_hue_image = cv2.addWeighted(lower_red_hue_range, 1.0, upper_red_hue_range, 1.0, 0.0)
                red_hue_image = cv2.GaussianBlur(red_hue_image, (9, 9), 2, 2)
                
                # Use the Hough transform to detect circles in the combined threshold image
                circles_r = cv2.HoughCircles(red_hue_image, cv2.HOUGH_GRADIENT, 1, red_hue_image.shape[0] / 8.0, 100, 20, 1, 1)

                print('circles_r_detected: ' + str(len(circles_r)))
                      
                if circles_r is None or len(circles_r[0,:]) < 4:
                    self.light_state = 4 # unknown
                else:
                    self.light_state = 0 # red

                    
                    path = os.getcwd()
                    image_folder = path +'/sim_data'
                    timestamp = datetime.utcnow().strftime('%Y_%m_%d_%H_%M_%S_%f')[:-3]
                    image_filename = os.path.join(image_folder, timestamp+'_'+str(self.light_state)+'_'+str(len(circles_r)))
                    #print(image_filename)
                    
                    cv2.imwrite('{}.jpg'.format(image_filename),red_hue_image)
                
                #return t.state
                self.idx += 1
                #prev_state = self.state
                #return self.state
                return self.light_state
                
            elif(self.idx == 1):
                self.idx += 1
                print('no training' + str(self.idx))
                #self.state = prev_state
                return self.light_state
            elif(self.idx == 2):
                self.idx += 1
                print('no training' +str(self.idx))
                #self.state = prev_state
                return self.light_state
            elif(self.idx == 3):
                self.idx = 0
                print('idx reset')
                #self.state = prev_state
                return self.light_state
```

b. Check You Only Look Once (YOLO) Keras implementation with Bosch dataset for classification 

# Self Driving Car capstone
  - Udacity self driving nano degree #9 (final...)

## References:
   - All figures appeared on this readme.md are from Udacity's self driving car nano degree material unless otherwise specified. 
   - ROS official website: [ros.org](https://www.ros.org/)

## How a robot works: 
- Perception: how robot sees the world
- Decision making: command to determine what the next step would be
- Actuation: make a move 

![ROS_overview](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Selfdriving_final/ros.png)


## Nomenclature (Node,topics,messages,package):
- ROS (Robot Operating System) governs the interaction (communication) of each nodes. 

![ROS_Nodes](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Selfdriving_final/nodes.png)

- Topic: 
like CAN (Controlled Area Network) bus to arrange standardized method to communicate information between nodes. 
- Publish: 
A Node sends out information (message) to the recipient. "sending" is called as publish. 
- Subscribe: 
The recipient receives the information (message), "receiving" is called as subscribe. 
![ROS_pub_sub](/selfdriving_final_figure/pub_sub_architecture.png)
- Messages: 
There are quite a bit of predefined messages which represents the prevailing robotic application. For instance, Camera sends out (publish) "image" message. Wheel encoder publishes "rotation" message. 
![ROS_message](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Selfdriving_final/ROS_message.png)
- Compute Graph: 
Visualization message transportation through topic between nodes (similar with [stateflow](https://www.mathworks.com/products/stateflow.html), [structured analysis](https://en.wikipedia.org/wiki/Structured_analysis))
![compute_graph](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Selfdriving_final/compute_graph.png)
- Package: according to [ROSwiki](http://wiki.ros.org/ROS/Concepts), an element of ROS which can build and release. It may contains node(s), ROS dependent library, dataset, configuration files or anything else that is usefully organized together. 

## Project overview:

- The scope of project is to write ROS nodes to implement and integrate the various features (packages) of Carla's autonomous driving system. The packages (**c++ package name**) include, 

  1. traffic light detection (**tl_detector**): transmits the location to stop for red light
  2. way point updater (**waypoint_updater**): update target velocity of each way points based on traffic light and obstacle detection data
  3. vehicle controller (**twist_controller**): controls the steering,throttle,and brakes

![project_overview](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Selfdriving_final/project_overview.png)

## self driving car nodes:

1.**tl_detector** (traffic light detection)
  - incoming topics:  
    a. /base_waypoints: the complete list of way points which the vehicle will follow      
    b. /image_color: camera image transporter  
    c. /current_pose: the vehicle's current location   

  - outgoing topic:    

    a.  /traffic_waypoint: the locations to stop for red traffic light (index of the waypoint for the nearest upcoming red light's stop line)     
  - things to work on:    

    a. tl_detector.py: traffic light detection       

    b. tl_classfier.py: classfies traffic light      

![tl_detector](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Selfdriving_final/tl-detector-ros-graph.png)

2.**waypoint_updater** (update target velocity based on traffic light/obstacle data)

 - incoming topics:  

    a.  /base_waypoints: the complete list of way points which the vehicle will follow. only published once (it makes sense as it won't change all lists throughout driving on the target path) 

    b.  /obstacle_waypoint: don't apply for now.    

    c.  /traffic_waypoint: topic from tl_detector node    

    d.  /current_pose: the vehicle's current location     

 - outgoing topic:    

    a.  /final_waypoints: the list of waypoints (a fixed number of waypo ahead of the car with target velocities  

![tl_detector](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Selfdriving_final/waypoint-updater-ros-graph.png)

3.**twist_controller** (responsible for control the vehicle)    

 - incoming topics:      

   a.  /current_velocity      

   b.  /twist_cmd      

   c.  /vehicle/dbw_enabled      

 - outgoing topics:      

    a.  /vehicle/throttle_cmd    

    b.  /vehicle/steering_cmd    

    c.  /vehicle/brake_cmd    

![twist_controller](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Selfdriving_final/dbw-node-ros-graph.png)

4. **classifier**
- Frame work:
  a. 
     ** revised step1 (image detection using opencv) for a intermediate solution:
     - As fore-mentioned, The Bosch dataset method doesn't detect the traffic signal image correctly using CNN. The reason is, the difference from the trained images to captured simulation image makes result in wrong prediction (classificaiton)
     - How about automatically crop the image at certain range where the vehicle approaches to the nearest traffic signal. However, 
       when camera is on, path following is not working so manually driving and collecting data is painful. Therefore, tried to look for the simplest way.  
     - I used a method to detect a red circle (only) when the vehicle approaches 80m before the nearest traffic signal 
     
     ** future improvement (using YOLO to classify light, custom training data set either [bosch dataset](https://hci.iwr.uni-heidelberg.de/node/6132) or [annotated simulation image](https://github.com/coldKnight/TrafficLight_Detection-TensorFlowAPI)
     
  b. step 2 (integration to tl_classifier node):
     - embed classification algorithm and transmit the signal color as an output. 
     
5. Things to note:
   - path following doesn't work when camera is on. rospy rate is adjusted @ tl_detector as 5hz, but no luck. 
   - in order to mitigate the latency issue when camera is on, I had added a method to take only 1 image out of 3 for classification,         but no luck. 
   - It took so long time to troubleshoot and mitigate the latency. I asked Udacity to look at if simulator has changed at some point.
   - Until the discussion happens with Udacity, what I can demonstrate for the final submission are, 
     - without camera on, path following works. 
     - at steady state, it detects the red stop signal. 
   
Before classification/detection:
![original](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Selfdriving_final/red_light_detection0.png)

After classification/detection:
![opencvdetection](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Selfdriving_final/red_light_detection1.png)
![opencvdetection](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Selfdriving_final/red_light_detection2.png)


6. Tips:
   - rostopic list: look up all topics 
   ![ros_topic_list](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Selfdriving_final/rostopic_list.png)
   - rostopic info /current_pose: check the mesage type on a "/current_pose" topic 
   ![ros_topic_list_info](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Selfdriving_final/rostopic_info.png)
   - rosmsg info geometry_msgs/PoseStamped: check the message contents
   ![ros_msg_list](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Selfdriving_final/rostopic_info_msg.png)
   
7. Issues:
   ![no_ros_msg](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Selfdriving_final/rostopic_echo_base_waypoints.png)
   
   

