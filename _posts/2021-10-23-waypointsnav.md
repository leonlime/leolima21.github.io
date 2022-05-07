---
title: Waipoints navigation + Commented code
date: 2021-10-23 14:07:00 +/-TTTT
categories: [Robotics, Mobile Robots]
tags: [robotics, ros, gazebo, navigation]     # TAG names should always be lowercase
image:
  src: /assets/img/waypoints.png
  width: 400   # in pixels
  height: 400   # in pixels
  alt: waypointsnav
---

This content is ideal for those who are starting in ROS, and for this reason, besides the demo video I am also sharing the code I created, with comments to simplify the understanding. This code is about navigation between points on the map, first adding them to a list and then using Turtlebot's movebase to make the robot move through them, always hoping to reach one point before going to another. From this code, more complex navigation algorithms can be developed.

## Video
<iframe width="560" height="315" src="https://www.youtube.com/embed/V8V8sOzXaZE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Commented Code
```python
#!/usr/bin/env python3
import rospy
from geometry_msgs.msg import PoseStamped
from geometry_msgs.msg import Twist, Vector3
from move_base_msgs.msg import MoveBaseActionGoal, MoveBaseAction, MoveBaseGoal, MoveBaseActionResult

class Turtlebot:
  def __init__(self):
    # list that will receive the waypoints:
    self.waypoints = []
    # starting node waypoints nav:
    rospy.init_node('waypointsnav')
    # instance of the object that will publish in the topical move base:
    self.move_base_pub = rospy.Publisher("/move_base_simple/goal", PoseStamped, queue_size=1)

  # method for adding new points to the list::
  def add_waypoint(self, list):
    self.waypoints.append(list)

  # publish the position on the move_base, taking into account the map coordinate (0,0):
  def goal_move_base(self, pose_x, pose_y, pose_z, pose_w):
    msg_move_to_goal = PoseStamped()
    msg_move_to_goal.pose.position.x = pose_x 
    msg_move_to_goal.pose.position.y = pose_y
    msg_move_to_goal.pose.orientation.z = pose_z
    msg_move_to_goal.pose.orientation.w = pose_w
    msg_move_to_goal.header.frame_id = 'map'

    rospy.sleep(1)

    self.move_base_pub.publish(msg_move_to_goal)
    
  # navigate between the points, waiting to arrive at one point before going to the other:
  def nav_into_points(self):
    for i in range(len(self.waypoints)):
      self.goal_move_base(self.waypoints[i][0], self.waypoints[i][1], self.waypoints[i][2], self.waypoints[i][3])
      rospy.wait_for_message("/move_base/result", MoveBaseActionResult, timeout=None)

if __name__ == '__main__':
  robot = Turtlebot()  
  
  robot.add_waypoint([-6.1,-1.0,-0.7,0.7])
  robot.add_waypoint([1.1,3.4,-0.7,0.7])
  robot.add_waypoint([6.2,-0.9,-0.7,0.7])
  
  robot.nav_into_points()
  
```

## How to execute:
To execute this code, just clone [this repository](https://github.com/leonlime/turtlenav) to your workspace and follow the commands below:

```roslaunch turtlenav turtlenav_gazebo.launch```

```rosrun turtlenav waypoints_nav.py```
