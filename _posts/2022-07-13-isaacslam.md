---
title: Using Isaac visual SLAM without a docker
date: 2022-07-13 19:33:00 +/-TTTT
categories: [Robotics, Mobile Robots]
tags: [robotics, ros2, isaac, mobile, slam]     # TAG names should always be lowercase
pin: false
image:
  src: /assets/img/isaacslam.png
  width: 400   # in pixels
  height: 400   # in pixels
  alt: Isaacslam
---

According to the NVIDIA description, [this repository](https://github.com/NVIDIA-ISAAC-ROS/isaac_ros_visual_slam) provides a ROS2 package that performs stereo visual simultaneous localization and mapping (VSLAM) and estimates stereo visual inertial odometry using the Isaac Elbrus GPU-accelerated library. All instructions to use the package are in the README but are focused on docker use. In this tutorial, I will describe an easy way to configure your machine to use the SLAM using Isaac sim and outside a docker.

# Demonstration

# Isaac sim simulation setup
pip install pytest-rerunfailures


Add these line to your ~/.bashrc and reload the terminal. After that cmake will find the nvcc

# cuda 10.2
export CUDA_HOME=/usr/local/cuda
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64
export PATH=$PATH:$CUDA_HOME/bin



mkdir -p your_ws/src && cd your_ws/src

git clone https://github.com/NVIDIA-ISAAC-ROS/isaac_ros_visual_slam
git clone https://github.com/NVIDIA-ISAAC-ROS/isaac_ros_common
git clone https://github.com/NVIDIA-ISAAC-ROS/isaac_ros_image_pipeline

After clone packages, go to saac_ros_common/docker/vpi and install the debian files for VPI.

cd ..

$ source /opt/ros/foxy/setup.bash
$ colcon build --symlink-install
$ . install/setup.bash


FONTES:

https://github.com/NVIDIA-ISAAC-ROS/isaac_ros_visual_slam/issues/18
https://github.com/jetsonhacks/buildLibrealsense2TX/issues/13


SAVE / LOAD MAP:
ros2 action send_goal /visual_slam/save_map isaac_ros_visual_slam_interfaces/action/SaveMap map_url:\ \home\leo\Desktop

ros2 action send_goal /visual_slam/load_map_and_localize isaac_ros_visual_slam_interfaces/action/LoadMapAndLocalize map_url:\ \home\leo\Desktop

# Visual SLAM setup

# Running the SLAM



