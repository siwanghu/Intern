### Turtlebot学习笔记 create by siwanghu v1.0
# ROS安装  
## ubuntu14.04上安装ROS Indigo版本
> **设置安装软件源**  
> sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'  
>  
> **设置key**  
> sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 421C365BD9FF1F717815A3895523BAEEB01FA116  
>  
> **安装**  
> sudo apt-get update  
> sudo apt-get install ros-indigo-desktop-full
>  
> **初始化rosdep**  
> sudo rosdep init  
> rosdep update  
>  
> **初始化ROS开发环境**  
> echo "source /opt/ros/indigo/setup.bash" >> ~/.bashrc  
> source ~/.bashrc  
>  
> **安装ROS工具包**  
> sudo apt-get install python-rosinstall
  
# Turtlebot包安装  
## 安装ROS Indigo对应的Turtlebot包 
> **ubuntu软件安装源更新**  
> sudo apt-get update  
>
> **Turtlebot包安装**  
> sudo apt-get install ros-indigo-turtlebot ros-indigo-turtlebot-apps ros-indigo-turtlebot-interactions ros-indigo-turtlebot-simulator ros-indigo-kobuki-ftdi ros-indigo-rocon-remocon ros-indigo-rocon-qt-library ros-indigo-ar-track-alvar-msgs  
# Turtlebot测试
> **启动ROS**  
> roscore  
>  
> **设备驱动配置**  
> 使用 ls /dev/kobuki命令，如果没有显示对应的设备驱动 **/dev/kobuki** 则使用命令 **rosrun kobuki_ftdi create_udev_rules** 添加，重启Turtlebot底盘即可  
>  
> **键盘控制底盘**  
> roslaunch turtlebot_bringup minimal.launch  
> roslaunch turtlebot_teleop keyboard_teleop.launch  
>  
> **通过发布主题命令控制底盘**  
> rostopic pub -r 10 /cmd_vel_mux/input/navi  geometry_msgs/Twist  '{linear:  {x: 0.1, y: 0.0, z: 0.0}, angular: {x: 0.0,y: 0.0,z: 0.0}}'  
# Kinect配置  
> **安装驱动**  
> sudo apt-get install ros-indigo-openni-* ros-indigo-openni2-* ros-indigo-freenect-*  
> rospack profile  
>  
> **设置环境变量(非常重要,做任何事之前都要查看和配置)**  
> echo $TURTLEBOT_3D_SENSOR  
> 如果输出的不是kinect则需要重新配置  
>  
> **如果你看到一个3D传感器，例如asus_xtion_pro，您将需要设置环境变量的默认值，修改和重新启动终端：**  
> echo "export TURTLEBOT_3D_SENSOR=kinect" >> .bashrc  
# Kinect测试  
> **启动turtlebot**  
> roslaunch turtlebot_bringup minimal.launch  
>  
> **启动Kinect**  
> roslaunch freenect_launch freenect-registered-xyzrgb.launch （Kinect新版本）  
> roslaunch openni_launch openni.launch (Kinect或旧版本)  
> **针对Asus Xtion, Xtion Pro, or Primesense 1.08/1.09 cameras:**  
> roslaunch openni2_launch openni2.launch depth_registration:=true  
>  + 图片 rosrun image_view image_view image:=/camera/rgb/image_color  
> + 深度图 rosrun image_view image_view image:=/camera/depth_registered/image  
>
> **在RVIZ上查看相机**  
> roslaunch turtlebot_bringup 3dsensor.launch  
> roslaunch turtlebot_rviz_launchers view_robot.launch  
# Turtlebot跟随  
> **启动Turtlebot**  
> roslaunch turtlebot_bringup minimal.launch  
>  
> **启动跟随脚本**  
> roslaunch turtlebot_follower follower.launch  
# Turtlebot建图导航  
## 创建地图 
>  
> **启动Turtlebot** *roslaunch turtlebot_bringup minimal.launch*  
>   
> **创建地图** *roslaunch turtlebot_navigation gmapping_demo.launch*  
>  
> **打开rviz** *roslaunch turtlebot_rviz_launchers view_navigation.launch*  
>  
> **手动扫描建图** *roslaunch turtlebot_teleop keyboard_teleop.launch*  
>  
> **保存地图** *rosrun map_server map_saver -f /tmp/my_map*  
## 自主导航  
> **启动Turtlebot** *roslaunch turtlebot_bringup minimal.launch*  
>  
> **启动导航模块** *roslaunch turtlebot_navigation amcl_demo.launch map_file:=/tmp/my_map.yaml*  
>  
> 导航模块有时启动失败可以使用这种方式启动(**非常重要**)  
> export TURTLEBOT_MAP_FILE=～/tmp/my_map.yaml  
> roslaunch turtlebot_navigation amcl_demo.launch  
>  
> **打开rviz** *roslaunch turtlebot_rviz_launchers view_navigation.launch --screen*  
>  
> **当启动之后，TurtleBot并不知道自己在哪个位置,需要给它提供它在地图上的位置：**  
> + 点击rviz中的”2D Pose Estimate”按钮
> + 在地图上标出TurtleBot的近似位置，并指出TurtleBot的朝向（TurtleBot的正运动方向)  
> + 点击rviz上的”2D Nav Goal”按钮  
> + 在地图上标出TurtleBot的导航目标，并且指出其在导航终点的朝向  
# 网络配置  
> 主机也需要和笔记本一样安装ROS和Turtlebot包(**参考上面ROS与Turtlebot包安装过程**)  
>  
> 主机与笔记本需要同步时钟，在主机与笔记本上都需要安装chrony(**非常重要**)  
> sudo apt-get install chrony  
> sudo ntpdate ntp.ubuntu.com  
>  
> 在主机和笔记本电脑上都需要安装ssh-server  
> sudo apt-get install openssh-server  
>  
> 在笔记本电脑端配置  
> echo export ROS_MASTER_URI=http://localhost:11311 >> ~/.bashrc  
> echo export ROS_HOSTNAME=**笔记本电脑IP地址** >> ~/.bashrc  
>  
> 在主机端配置  
> echo export ROS_MASTER_URI=http://**笔记本电脑IP地址**:11311 >> ~/.bashrc  
> echo export ROS_HOSTNAME=**主机IP地址** >> ~/.bashrc  
## 网络测试  
> 主机开启一个终端使用ssh命令登陆笔记本电脑  
> ssh username@ip  
> 运行roscore  
>  
> 主机在开启一个终端使用ssh命令登陆笔记本电脑  
> ssh username@ip  
> 运行roslaunch turtlebot_bringup minimal.launch  
>  
> 在主机开启一个终端  
> 运行roslaunch turtlebot_teleop keyboard_teleop.launch  
> 如果在主机端可以操作Turtlebot,说明网络配置成功  
# ROS常用命令  
> 实时查看机器人坐标位置  
> rosrun tf tf_echo /map /base_link

# ROS工作原理  
> ##  **ROS节点**  
> 使用命令**rosnode**查看Turtlebot运行启动时运行的ROS结点  
>  
> ROS 的一个基本目标是使机器人的很多节点node（可执行文件，几乎相对独立的小程序）能够同时运行。为此，这些节点必须能够彼此通信。ROS 中实现通信的关键部分就是ROS节点管理器。要启动节点管理器，使用如下命令：**roscore**。节点管理器应该在使用ROS的全部时间内持续运行。一个合理的工作流程是在一个终端启动roscore，然后打开其他终端运行其他程序。  
>  
> **/rosout**  
> rosout节点是一个特殊的节点，通过 roscore 自动启动。其作用有点类似于控制台程序中使用的标准输出（即 std::cout）。/rosout也是话题，所有的节点发布都向话题/rosout 发布消息，该话题由同名的 /rosout 节点订阅。这个话题的作用是用来生成各个节点的文本日志消息。在某些地方/rosout 既指节点又指话题。但 ROS 并不会这种重复的名字而混淆，因为 ROS 会根据上下文来推测我们讨论的是/rosout节点,还是/rosout话题。  
>  
> **/app_manager**  
> 对于robot是一个公共接口结点,只需调用app_manager的接口就可以操控robot。  
>  
> **/master**  
> master提供topic的命名和注册service功能，也提供跟踪sub/pub功能。这个节点的角色就是让A节点能够定位到B节点。然后两个节点AB之间进行点对点的通信。
也提供参数服务器功能。  
> ## **ROS主题**  
> 使用**rostopic**查看Turtlebox启动时运行的ROS主题  
>  
> 主题是由ROS网络对消息进行路由和消息管理的数据总线。每一条消息都要发布到相应的主题。当一个节点发送数据时，我们就说该节点正在向主题发布消息。节点可以通过订阅某个主题，接收来自其他节点的消息。一个节点可以订阅一个主题，而并不需要该节点同时发布该主题。这就保证了消息的发布者和订阅者之间相互解耦，完全无需知晓对方的存在。主题的名称必须是独一无二的，否则在同名主题之间的消息路由就会发生错误。  
>  
> **/cmd_vel_mux/active**  
> 记录每一刻的主动输入，如果没有人指挥机器人，就会闲置的话题  
>  
> **/cmd_vel_mux/input/XXX**  
> 多路复用器输入主题。可以在配置文件中描述任意数量的输入主题  
> ## **ROS消息**  
> **rostopic info 节点名**可以查看主题的消息类型  
>  
> 节点通过消息完成彼此的沟通。消息包含一个节点发送到其他节点的数据信息。ROS 中包含很多种标准类型的消息，同时你也可以基于标准消息开发自定义类型的消息  
>  
> ## **ROS服务**  
> 可以使用**rosservice**命令查看当前的ROS服务  
>  
> 在发布主题时，正在发送的数据能够以多对多的方式交互。但当你需要从某个节点获得一个请求或应答时，就不能通过主题来实现了。在这种情况下，服务能够允许我们直接与某个节点进行交互。此外，服务必须有一个唯一的名称。当一个节点提供某个服务时，所有的节点都可以通过使用ROS 客户端库所编写的代码与它通信。  
>  
> ## **ROS节点管理器**  
> 节点管理器是ROS名称服务,能够帮助节点找到彼此。节点通过与节点管理器通信来报告他们的注册信息。值得注意的是,当这些节点和节点管理器通信时,它们可以接受其他注册节点的信息,并能保持通信正常。当这些注册信息改变时,节点管理器也会回调这些节点。所以,没有节点管理器,节点将不能相互找到,也不能进行消息交换或者调用服务。  
>  
> ## **参数服务器**  
> 是节点管理器的一部分,其允许数据通过关键词来存储。可以在结点运行时配置结点或改变节点的工作模式  
>  
> ## **消息记录包**  
> 使用**rosbag record -a**记录所有消息，消息记录包存放在当前文件夹下  
> 使用**rosbag play 包名**回放消息  
>
> 消息记录包是一种用于保存和回放 ROS 消息数据的格式。消息记录包是检索传感器数据的重要机制。它可以获取并记录各种传感器数据，我们可以通过消息记录包来反复获取实验数据  
>  
> ![图片](ros.JPG)





