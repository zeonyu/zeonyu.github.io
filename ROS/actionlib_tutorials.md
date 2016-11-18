# actionlib 教程

该教程默认已经具有一定的ros基础，仅仅记录关键步骤，没有详细的细节，可以用来梳理思路，如果想要详细教程，请转到[官网](http://wiki.ros.org/actionlib)

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

## fibonacci 数列的例子

首先创建一个pkg用来做例子（先转到workspace下）：```catkin_create_pkg learning_actionlib actionlib message_generation roscpp rospy std_msgs actionlib_msgs```

1. 添加action文件：创建 learning_actionlib/action/Fibonacci.action，添加如下：

```
#goal definition
int32 order
---
#result definition
int32[] sequence
---
#feedback
int32[] sequence
```

2. 修改配置文件

CMakeLists.txt中修改：

```
find_package(catkin REQUIRED COMPONENTS actionlib_msgs)

add_action_files(
  DIRECTORY action
  FILES Fibonacci.action
)

generate_messages(
  DEPENDENCIES actionlib_msgs std_msgs  # Or other packages containing msgs
)

catkin_package(
  CATKIN_DEPENDS actionlib_msgs
)
```

然后编译就可以了。

解释一下action做了哪些事情。

action其实定义的是一个比较高层的语义，在make的过程中会生成相应的msg和头文件。1.其中头文件会生成7个，和msg对应；2.其中msg会生成包括：```FibonacciActionGoal.msg```和```FibonacciGoal.msg```这样类似的三对，分别是goal、feedback、result，和一个总的```FibonacciAction.msg```。如果详细关注一下，就会发现他们三者的逻辑其实是：

```
                            FibonacciAction.msg
    ________________________________|________________________________        
    |                               |                                |
 FibonacciActionGoal.msg  FibonacciActionFeedback.msg  FibonacciActionFeedback.msg
     |                              |                                |
 FibonacciGoal.msg        FibonacciFeedback.msg           FibonacciFeedback.msg
```

## server 和 client 都做了些什么