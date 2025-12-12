# **Week2 实验报告**
***
## **一、实验任务**
- Linux基础命令
- Python/C++开发环境配置
- ROS1（Noetic）安装与配置
- ROS工作空间与功能包基础
- Turtlesim小乌龟模块
- Launch文件与命名空间入门
- 小乌龟多实例控制

***

## **二、实现过程**

### **1.Linux基础命令**

1. 文件管理：查看目录与文件（ls,ls -l,ls -a,pwd,cd,cd ..,cd ~），创建与删除（touch,mkdir,mkdir -p,rm,rm -r,rm -f），复制与移动（cp,cp -r,mv）
***
权限管理指令：修改权限（chmod u+x 文件名，添加执行权限），修改所有者（chown 用户名 文件名，修改文件所有者）
***
进程管理指令：查看进程（ps,ps -ef,top,pstree），过滤进程（ps -ef | grep 进程名），终止进程（kill 进程PID，kill -9 进程PID, pkill 进程名）
***
安装软件指令（sufo apt update更新软件源列表，sudo apt install 软件名 安装指定软件，sudo apt remove 软件名 卸载软件，sudo apt purge 软件名 彻底卸载软件，sudo apt upgrade升级系统中所有已安装软件）
***
2. 常见目录含义：
/home 用户主目录
/usr 系统软件资源目录
/opt 可选应用程序目录
/etc 系统配置文件目录
***
### **2.Python/C++开发环境配置**
1. Python3与pip环境配置：
```
python3 --version
```
检查Python3版本以后，通过指令安装pip3
```
sudo apt update
sudo apt install python3-pip -y
pip3 install --upgrade pip
```
验证 Python3/pip3 完整性
```
python3 -c "print('Python3 环境正常')"

pip3 install numpy
```
***
2. GCC/G++配置：
安装 build-essential
```
sudo apt install build-essential -y
```
***
3. VSCode Python/C++ 插件安装：
打开VSCode终端
```
code --install-extension ms-python.python

code --install-extension ms-vscode.cpptools
```
***
### **3.ROS1（Noetic）安装与配置**
1. ROS环境搭建
配置Ubuntu软件源与添加ROS官方软件源
```
sudo apt update
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```
添加密钥
```
sudo apt install curl -y
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
```
安装ROS完整版
```
sudo apt update

sudo apt install ros-noetic-desktop-full -y
```
***
2. rosdep初始化

rosdep否则无法运行 rosrun/roslaunch 等命令

安装rosdep依赖
```
sudo apt install python3-rosdep python3-rosinstall python3-rosinstall-generator python3-wstool build-essential -y
```
初始化 rosdep
```
sudo rosdep init
```
更新rosdep
```
rosdep update
```
***
3. ROS环境变量设置

ROS 安装后需配置环境变量，用于识别 roscore、rosrun 等命令

```
source /opt/ros/noetic/setup.bash
```
***
4. ROS 常用命令
启动 ROS 核心节点
```
roscore
```
新开第二个终端，运行单个 ROS 节点,同时需保持roscore终端运行
```
rosrun turtlesim turtlesim_node
```
新开第三个终端，运行控制海龟的节点
```
rosrun turtlesim turtle_teleop_key
```
按键盘方向键（↑↓←→），即可控制海龟移动
***
roslaunch:
新开终端并执行：
```
roslaunch turtlesim turtle_teleop_key.launch
```
***
rostopic

rostopic list 列出所有活跃话题

rostopic info <话题名>	查看话题详情（类型、发布者 / 订阅者）

rostopic echo <话题名>	实时打印话题消息

rostopic pub <话题名> <消息类型> <消息内容>	发布话题消息（控制节点）
***
### **4. ROS 工作空间与功能包基础**
1. catkin_ws 工作空间创建

创建目录结构
```
catkin_ws 工作空间创建

mkdir -p ~/catkin_ws/src

cd ~/catkin_ws/src
catkin_init_workspace

cd ~/catkin_ws
catkin_make

echo "source ~/catkin_ws/devel/setup.bash" >> ~/.bashrc
source ~/.bashrc
```
***
2. 功能包创建与依赖

功能包可包含节点、配置文件、脚本等，创建时需指定依赖包（ROS 自动处理依赖编译）

创建功能包
```
cd ~/catkin_ws/src

catkin_create_pkg learning_ros roscpp rospy std_msgs

```
依赖理解与验证
```
rospack depends learning_ros
```
***
3. ROS 功能包文件结构

src: 存放 C++ 节点源文件

include: 存放 C++ 头文件

scripts: 存放 Python 节点脚本
***
4. 编写 HelloWorld 节点（C++）

创建C++源文件
```
cd ~/catkin_ws/src/learning_ros/src

touch hello_world_cpp.cpp
```
写入对应代码

修改CMakeLists.txt
```
cd ~/catkin_ws/src/learning_ros/CMakeLists.txt
```
修改add_executable和target_link_libraries

![编译运行](running.png)
***
5. 发布者 Publisher 与 订阅者 Subscriber 基本流程

发布者： 向指定「话题（Topic）」发布消息的节点（如发布海龟速度指令）

订阅者： 订阅指定「话题」，接收并处理消息的节点

编写 C++ 版 Publisher

创建源文件
```
cd ~/catkin_ws/src/learning_ros/src
touch publisher.cpp
nano publisher.cpp
```
写入代码

编写 C++ 版 Subscriber

创建源文件

```
touch subscriber.cpp
nano subscriber.cpp
```
写入代码

修改CMakeLists.txt，在末尾添加
```
add_executable(publisher src/publisher.cpp)
target_link_libraries(publisher ${catkin_LIBRARIES})

add_executable(subscriber src/subscriber.cpp)
target_link_libraries(subscriber ${catkin_LIBRARIES})
```

***
### **5. Turtlesim 小乌龟模块**

1. turtlesim 环境安装与运行

turtlesim 是 ROS 内置的基础功能包，已默认安装

2. 键盘控制

roscore启动 ROS 核心节点,第二个终端turtlesim 仿真节点
```
rosrun turtlesim turtlesim_node
```
第三个键盘控制小乌龟
```
rosrun turtlesim turtle_teleop_key
```
之后使用方向键↑↓←→即可控制小乌龟
***
3. rostopic 控制小乌龟速度

小乌龟的速度控制话题为 /turtle1/cmd_vel，消息类型是 geometry_msgs/Twist，包含线速度（linear） 和角速度（angular）

通过rostopic list查看 cmd_vel 话题信息,用 rostopic pub 发布速度指令
```
rostopic pub -r 10 /turtle1/cmd_vel geometry_msgs/Twist "linear:
  x: 1.0
  y: 0.0
  z: 0.0
angular:
  x: 0.0
  y: 0.0
  z: 1.0"
```
-r 10代表以 10Hz 频率持续发布，使小乌龟走圆圈
![乌龟画圈](turtle_circle.png)

4. 订阅位置与轨迹信息

打开对应文件夹，并编辑C++文件
```
cd ~/catkin_ws/src/turtle_pose/scripts
touch turtle_pose_sub.cpp
gedit turtle_pose_sub.cpp

```
编写完成后运行
```
cd ~/catkin_ws
catkin_make

rosrun turtle_pose turtle_pose_sub.py
```

5. 编写简单程序画圆

编写画圆节点代码
```
touch turtle_draw_circle.cpp
gedit turtle_draw_circle.cpp
```
编写时关键在于角速度和线速度的参数
```
vel_msg.linear.x = 0.5

vel_msg.linear.y = 0.0

vel_msg.linear.z = 0.0

vel_msg.angular.x = 0.0

vel_msg.angular.y = 0.0

vel_msg.angular.z = 0.5
```
运行
```
rosrun turtle_pose turtle_draw_circle.py
```
***
### **6. Launch 文件与命名空间入门**

1. launch 文件结构

launch 文件是 XML 格式的配置文件，可一键启动多个节点、设置参数、配置命名空间 / 话题重映射、设置环境变量等
***
2. 命名空间

ROS 节点 / 话题 / 参数默认在全局命名空间下
***
3. remap 话题重映射

将一个话题名映射为另一个（如 /turtle1/cmd_vel → /my_turtle/vel）
***
4. 环境变量

由source 与 workspace overlay构成

source：文件可将工作空间的环境变量（如节点路径、包路径）添加到系统环境中，让 ROS 能找到自定义包 / 节点

workspace overlay：多个 ROS 工作空间可按「叠加顺序」生效（后 source 的工作空间优先级更高）

***

### **7. 小乌龟多实例控制**

1. 一个 launch 同时启动多只小乌龟

编写多乌龟 launch 文件
```
cd ~/catkin_ws/src/turtle_pose/launch/multi_turtle.launch
```
编写两只乌龟

***
2. 为每只乌龟配置不同命名空间和启动多乌龟Launch文件

```
source ~/catkin_ws/devel/setup.bash

roslaunch turtle_pose multi_turtle.launch
```
***
3. 用 rostopic pub 控制指定乌龟

查看所有乌龟的话题
```
rostopic list
```
控制第一只乌龟
```
rostopic pub -r 10 /turtle2/cmd_vel geometry_msgs/Twist "linear:
  x: 0.0
  y: 0.0
  z: 0.0
angular:
  x: 0.0
  y: 0.0
  z: 1.0"
```
控制第二只乌龟
```
rostopic pub -r 10 /turtle2/cmd_vel geometry_msgs/Twist "linear:
  x: 1.0
  y: 0.0
  z: 0.0
angular:
  x: 0.0
  y: 0.0
  z: 1.0"
```
![运行](two_turtle.png)
***
4. 用 rqt_graph 查看通信关系图

启动 rqt_graph
```
rqt_graph
```
![rqt_graph](rqt.png)


