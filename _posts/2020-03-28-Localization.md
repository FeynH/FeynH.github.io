---
layout:     post   				    # 使用的布局（不需要改）
title:     Localization using GPS, IMU and robot_localization # 标题 
subtitle:    #LPF                    #副标题
date:       2020-03-28 				# 时间
author:     Feyn 						# 作者
header-img: img/post-bg-2015.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - navigation
---
## 引言   
robot_localization是状态估计节点的集合，每个节点都是针对在3D空间中移动的机器人的非线性状态估计器的实现。 它包含两个状态估计节点ekf_localization_node和ukf_localization_node。 此外，robot_localization提供navsat_transform_node，它有助于集成GPS数据。在此，我仅使用imu+gps来实现机器人的室外导航。   
## 软硬件要求
* robot_localization : sudo apt-get install ros-kinetic-robot-localization   
* IMU : 九轴IMU,含地磁计
* GPS : u-blox M8 GNSS
## 过程
定位过程如下图所示。  

![rosgraph](https://tva4.sinaimg.cn/mw690/0060BUgEly1gd9h2clo0qj30um0lf0wz.jpg)  
  
其主要思想就是ekf_localization_node和navsat_transform_node形成一个共生的反馈回路。ekf_localization订阅navsat_transform发出的/odometry/gps消息，融合IMU信息，来生成/odometry/filtered消息。navsat_transform订阅ekf_localization发出的/odometry/filtered消息，融合gps原始数据(/gps/fix)和IMU数据，来生成/odometry/gps消息。  
  
tf树如下所示。ekf_localization node 广播odom->base_link的转换，odom坐标系在地球坐标系中的位置由启动navsat_transform_node时指定的基准参数指定。
     
![frames](https://tvax2.sinaimg.cn/mw690/0060BUgEly1gd9hzwlk37j30qh0czq4a.jpg)
