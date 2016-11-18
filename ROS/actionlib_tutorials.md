# actionlib 教程

## 通信与配置

一个server服务器端，一个client客户端，双方通信依靠action定义的三个数据量：Goal（c2s)，FeedBack（s2c），Result（s2c）

在./action文件夹创建的action文件的定义如下：

```
# Define the goal
uint32 dishwasher_id  # Specify which dishwasher we want to use
---
# Define the result
uint32 total_dishes_cleaned
---
# Define a feedback message
float32 percent_complete
```

catkin下，在CMakeListstxt中的catkin_package()前加入：

```
find_package(catkin REQUIRED genmsg actionlib_msgs actionlib)
add_action_files(DIRECTORY action FILES DoDishes.action)
generate_messages(DEPENDENCIES actionlib_msgs)
```

在package.xml加入：

```
<build_depend>actionlib</build_depend>
<build_depend>actionlib_msgs</build_depend>
<run_depend>actionlib</run_depend>
<run_depend>actionlib_msgs</run_depend>
```

