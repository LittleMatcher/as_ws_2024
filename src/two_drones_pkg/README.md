# Autonomous Systems-Lab2

## 1. Project repository

```bash
https://github.com/LittleMatcher/as_ws_2024
```
## 2. Flow
Step 1 is to create a workspace
```bash
$ mkdir -p ~/as_ws/src #ROS three steps, the address here can be modified
$ cd ~/as_ws/
$ catkin init
```
Step 2 Clone the project
```bash
$ git clone https://github.com/LittleMatcher/as_ws_2024.git
```
Step 3 Compile
```bash
$ catkin build
$ source devel/setup.bash
```
Step 4 Run
```bash
$ roslaunch <project> <launch file>
$ roslaunch two_drones_pkg two_drones.launch static:=True #static的作用取决于launch文件
```

## 3. Main Code
frames_publisher_node.cpp
```cpp
	// 2. Populate the two transforms for the AVs, using the variable "time"
    //    computed above. Specifically:
    //     - AV1World should have origin in [cos(time), sin(time), 0.0] and
    //       rotation such that:
    //        i) its y axis stays tangent to the trajectory and
    //       ii) the z vector stays parallel to that of the world frame
    //
    //     - AV2World shoud have origin in [sin(time), 0.0, cos(2*time)], the
    //       rotation is irrelevant to our purpose.
    //    NOTE: AV1World's orientation is crucial for the rest fo the assignment,
    //          make sure you get it right
    //
    //    HINTS:
    //    - check out the ROS tf Tutorials: http://wiki.ros.org/tf/Tutorials,
    //      http://wiki.ros.org/tf/Tutorials/Adding%20a%20frame%20%28C%2B%2B%29#The_Code
    //    - consider the setRPY method on a tf::quaternion for AV1
    AV1World.setOrigin(tf::Vector3(cos(time), sin(time), 0.0));// 设置旋转，使得物体始终朝向运动方向
    tf::Quaternion q;
    q.setRPY(0, 0, time+M_PI/2); // 因为在圆上，切线方向比位置的角度多 90 度
    AV1World.setRotation(q);
    AV2World.setOrigin(tf::Vector3(sin(time), 0.0, cos(2*time)));
```

```cpp

 	// 3. Publish the transforms, namely:
    //     - AV1World with frame_id "world", child_frame_id "av1"
    //     - AV2World with frame_id "world", child_frame_id "av2"
    //    HINTS:
    //         1. you need to define a tf::TransformBroadcaster as a member of the
    //            node class (line 11) and use its sendTrasform method below
    //         2. the frame names are crucial for the rest of the assignment,
    //            make sure they are as specified, "av1", "av2" and "world"    static tf::TransformBroadcaster br;
    br.sendTransform(tf::StampedTransform(AV1World, ros::Time::now(), "world", "av1"));// 发送变换
    br.sendTransform(tf::StampedTransform(AV2World, ros::Time::now(), "world", "av2"));
```

> Ref [https://wiki.ros.org/tf/Tutorials/Adding%20a%20frame%20%28C%2B%2B%29#](https://wiki.ros.org/tf/Tutorials/Adding%20a%20frame%20%28C++%29#)

plots_publisher_node.cpp

```cpp
 		 // ~~~~~~~~~~~~~~~~~~~~~~  BEGIN OF EDIT SECTION  ~~~~~~~~~~~~~~~~~~~~~~~~~

         /* The transform object needs to be populated with the most recent
          * transform from ref_frame to dest_frame as provided by tf.

          * Relevant variables in this scope:
          *   - ref_frame, the frame of reference relative to which the trajectory
          *                needs to be plotted (given)
          *   - dest_frame, the frame of reference of the object whose trajectory
          *                 needs to be plotted (given)
          *   - parent->tf_listener, a tf::TransformListener object (given)
          *   - transform, the tf::transform object that needs to be populated
          *
          * HINT: use "lookupTransform", see
          *  http://wiki.ros.org/tf/Tutorials/Writing%20a%20tf%20listener%20%28C%2B%2B%29#The_Code
	        *
	        * NOTE: only using "lookupTransform", you might see a lot of errors in your terminal!
          *       Unlike other topic types - which are typically received on a callback basis -
          *       \tf can be polled at any time and therefore "lookupTransform" might fail 
          *       due to timing issues. To fix it, consider the method "waitForTransform" with 
          *       a small wait time.    
          */

          parent->tf_listener.lookupTransform(ref_frame, dest_frame, ros::Time(0), transform);  
```

> Ref [https://wiki.ros.org/tf/Tutorials/Writing%20a%20tf%20listener%20%28C%2B%2B%29#](https://wiki.ros.org/tf/Tutorials/Writing%20a%20tf%20listener%20%28C++%29#)
## 4. Result
Deliverable 1 - Nodes, topics, launch files (this is just for yourself - you do not actually need to delivere this)
### 4.1 Deliverable 1 - Nodes, topics, launch files (this is just for yourself - you do not actually need to delivere this)
### 4.1.1 Nodes List
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/77b88192daab4c3b9aee7e681c330381.png)
### 4.1.2 
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/52031c93ee8c417fb1c7f9b1753d223f.png)
### 4.1.3
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/6195a425f6f042c29fa3f356277a4793.png)

### 4.2 Deliverable 2 - Publishing transforms (30 pts)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/98a119161bd147c891eed3c7b14169c0.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/b5a3ff67580d4b0480baac2c9ea41eeb.gif#pic_center)
### 4.2 Deliverable 3 - Looking up a transform (30 pts)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/09bfe3ae865f4dc5a980b633497e1e09.gif#pic_center)



