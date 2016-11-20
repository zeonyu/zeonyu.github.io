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
    |                               |                                |
 FibonacciGoal.msg        FibonacciFeedback.msg           FibonacciFeedback.msg
```

## server 和 client 都做了些什么

### server 端做了什么

首先，我们先从功能性上进行讲解。server服务器，最直观的功能就是提供某个功能的模块，然后等待client的请求，当有一个client的请求进来的时候，就调用server中相关的处理代码进行功能实现。因此server中最重要的两个概念，一个是spin，一个是callback。spin直观理解就是让服务以一直处于等待接受client请求的状态，而callback则是当收到一个client请求，调用的函数。调用```rxgraph```可以查看节点状态。

下面就用fibonacci的例子来进行讲解。

（略）


### client 端做了什么

首先，还是先从功能上进行讲解。既然已经有了server，那么就可以从client端直接发送目标，让server执行。具体操作的方式是，申明一个client：```actionlib::SimpleActionClient<learning_actionlib::FibonacciAction> ac("fibonacci", true);```，之后设置好goal之后，就可以发送：```ac.sendGoal(goal);```。至此，关键步骤就算结束了，之后在进行是否成功的检查就可以。调用```rxgraph```可以查看节点状态。

下面就用fibonacci的例子来进行讲解。

（略）
