# rtabmap

配置方法：

1. freenect2驱动
2. kinect2_bridge

启动命令：

```
roslaunch kinect2_bridge kinect2_bridge.launch 
rosrun tf stic_transform_publisher 0 0 0 -1.5707963267948966 0 -1.5707963267948966 camera_link kinect2_link 100
rosrun tf stic_transform_publisher  0 0 0  0 0 0  map camera_link 100
rosrun tf stic_transform_publisher  0 0 0  0 0 0  kinect2_link kinect2_rgb_optical_frame 100
roslaunch rtabmap_ros rgbd_mapping.launch rta
bmap_args:="--delete_db_on_start" rgb_topic:=/kinect2/qhd/image_color_rect depth_registered_topic:=/kinect2/qhd/image_depth_rect camera_info_topic:=/kinect2/qhd/camera_info

```