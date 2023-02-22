## Setup environment

1. build & install RealTime kernel

   reference : https://docs.ros.org/en/humble/Tutorials/Miscellaneous/Building-Realtime-rt_preempt-kernel-for-ROS-2.html

   > If there is a Error like

   ```
   make[2]: *** [debian/rules:6: build] Errore 2
   dpkg-buildpackage: Errore: debian/rules build subprocess returned exit status 2
   make[1]: *** [scripts/Makefile.package:77: deb-pkg] Errore 2
   make: *** [Makefile:1464: deb-pkg] Errore 2
   ```

   > reference https://askubuntu.com/questions/1327749/error-in-compiling-installing-realtime-kernel-on-ubuntu-20-04-2-lts

2. set permission of scheduler and memory lock

   reference : https://docs.ros.org/en/humble/Tutorials/Demos/Real-Time-Programming.html

3. Nvidia error in RealTime kernel

    follow this insturction
    https://github.com/ApolloAuto/apollo/blob/master/docs/01_Installation%20Instructions/how_to_install_apollo_kernel_cn.md

## KUKA Configuration

### Right iiwa

    IP : 192.168.168.20

    R Port : 30200

### Left iiwa

    L IP : 192.168.168.10

    L Port : 30201

## Simulation

需要通过 source 手动安装 Gazebo 最新版
### 1. Install Gazebo
Following as [Gazebo Official Install](https://gazebosim.org/docs/garden/install_ubuntu_src)

### 2. Install Gazebo Ros Plugin
Clone the [gz_ros2_control package](https://github.com/ros-controls/gz_ros2_control)

Check out to the `ahcorde/rename/ign_to_gz` branch.

This is because the version update of the Gazebo....

### 3. Source the Gazebo ws then export the Gazebo model environment variables

添加下面这段在 .bashrc 中，这样Gazebo才能找到机器人的描述文件

`export GZ_SIM_RESOURCE_PATH=/home/xx/xx_ws/install/iiwa_description/share/`

! Please replace the "xx" to your real path.

### 4. Launch the Gazebo Robot & Enjoy

`ros2 launch iiwa_bringup iiwa_bringup.launch.py use_sim:=true`

## Moveit2

Moveit2 中添加了伺服的功能，还附带了避障，同时还兼容Moveit本身的规划，用起来很方便。

关于servo的介绍可以参考这里[Moveit2 Servo](https://moveit.picknik.ai/humble/doc/examples/realtime_servo/realtime_servo_tutorial.html)

### 1. 安装 Moveit2

[Moveit2 官方 Tutorial](https://moveit.ros.org/install-moveit2/source/)

这里需要注意，可能在编译的时候需要指定CPU使用数量，不然很容易造成内存过载。

例如： `colcon build --event-handlers desktop_notification- status- --cmake-args -DCMAKE_BUILD_TYPE=Release --parallel-workers 2`

### 1. 启动moveit2

最好把 moveit 和 Gazebo 都放到单独的工作空间里，然后把这两个的source写到 .bashrc中

这里如果是仿真，就要把use_sim_time改成true， 如果是实际可以不带这个参数，默认是false。（实机我还没有测试过）

`ros2 launch iiwa_moveit_config iiwa_moveit.launch.py use_sim_time:=true`

### 2. 启动moveit2 servo

`ros2 launch iiwa_moveit_config iiwa_servo.launch.py`

实际使用的时候要把`iiwa_moveit_config/config/iiwa_l_servo.yaml`和`iiwa_moveit_config/config/iiwa_l_servo.yaml`这两个文件中的`use_gazebo`改成false


### 3. 测试Servo

`ros2 run iiwa_servo_test iiwa_servo_test`

然后往`/left/servo_node/test` 和 `/right/servo_node/test` 这两个话题里发消息就可以了


---
### 自定义运行学逆解

可以修改 iiwa_moveit_plugin 中 src的 chiniksolver_vel_mimic_svd.cpp 这个文件的 CartToJnt 这个函数。

然后在 iiwa_moveit_config 中 config/kinematics.yaml文件里的kinematics_solver 改成 iiwa_kinematics_plugin/IIwaKinematicsPlugin 就可以。

如果想换回原来默认的，改成 kdl_kinematics_plugin/KDLKinematicsPlugin

---

_KUKA Programming password:_ `kuka`

_KUKA Advanced password:_ `argus`
