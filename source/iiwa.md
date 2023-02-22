# iiwa install tutorials

配置环境：Ubuntu 22.04 LTS初始环境
本教程旨在帮助您在Ubuntu 22.04 LTS上安装iiwa软件包。教程运行在Ubuntu 22.04 LTS默认环境下，Ubuntu系统的安装可以参照网上的资料，此处不再赘述。

[Ubuntu22.04 安装教程](https://www.sysgeek.cn/install-ubuntu-22-04-lts/)

## 1. ROS安装

ROS安装可以参照 [ROS安装教程](https://docs.ros.org/en/humble/Installation.html)，
推荐按照官方教程安装，可以了解具体都配置了什么。
官方安装具体过程不再赘述，下面给出一个安装脚本，经测试可用：

```sh
wget http://fishros.com/install -O fishros && . fishros
```

这是由博主小鱼写的自动安装脚本，可以自动安装ROS humble版本([Github开源地址](https://github.com/fishros/install))。
输入命令后，输入密码，在选项界面选择1-一键安装ROS。之后会问你需不需要换源，根据实际情况来选择。
如果有能力做终端代理，可以不用换源；也可以自己手动换源（具体参照： [Ubuntu 22.04 换国内源](https://blog.csdn.net/xiangxianghehe/article/details/122856771)），
或者通过这里的脚本换源（未测试）。

再之后会让你选择ROS版本，这里选择1-humble(ROS2)，再选择1-humble(ROS2)桌面版。
之后出现依赖问题，按照提示进行就行。过程比较久，耐心等候，直到看到安装成功提示为止。
要检查ROS2是否成功安装，可以分别打开两个终端，运行代码：

```bash
   ros2 run demo_nodes_cpp talker
   ros2 run demo_nodes_py listener
```

第二个终端中，订阅第一个节点发出的信息，如：

```bash
   [INFO] [listener]: I heard: [Hello, world!]
```

这表明 ROS 2 系统已经成功安装，并且可以正常运行示例程序。如果你看到其他的输出或错误信息，可以参考 ROS 2 的官方文档或社区支持，以解决问题。

## 2. Gazebo安装

注意Gazebo安装必须通过Source安装，
具体过程参照[Gazebo安装教程](https://gazebosim.org/docs/garden/install_ubuntu_src/)编写，
下面给出详细步骤。

### 安装必要工具

通过apt安装：

```bash
    sudo apt-get update
    sudo apt install python3-pip wget lsb-release gnupg curl
```

在执行update过程如果出现Failed to fetch错误，需要给apt-get添加代理或者换源，
具体参照[Ubuntu 22.04 换国内源](https://blog.csdn.net/xiangxianghehe/article/details/122856771)。

> 注意，如果之后的安装出现找不到安装包的错误，可以将源码部分注释取消掉尝试。

通过pip安装vsctool和colcon：

```bash
   pip install vcstool || pip3 install vcstool
   pip install -U colcon-common-extensions || pip3 install -U colcon-common-extensions
```

注意pip中没有报错，完成了安装(Successfully installed ...)。

```bash
   pip show vcstool || pip3 show vcstool | grep Location
   pip show colcon-common-extensions || pip3 show colcon-common-extensions | grep Location
```

确认你的安装位置在 `%HOME/.local` 中后，将该路径添加到系统路径中，命令如下：

```bash
   export PATH=$PATH:$HOME/.local/bin/
```

官网中还介绍了apt安装方式，比pip安装更容易一些，经过尝试也是可以的。
下面安装Git，用于控制vcstool版本：

```bash
   sudo apt-get install git
```

### 获取Gazebo源码

接着，创建一个工作空间，用于存放Gazebo的源码：

```bash
   mkdir -p ~/workspace/src
   cd ~/workspace/src
```

> 当然你也可以随意命名，这里只是引用了原教程的命名方式。

接下来通过yaml文件下载gazebo-garden的源码：

```bash
   wget https://raw.githubusercontent.com/gazebo-tooling/gazebodistro/master/collection-garden.yaml
   vcs import < collection-garden.yaml
```

> 从这步开始涉及的软件包下载过程在没有代理的情况下，下载会比较缓慢甚至断开连接，请酌情使用代理。

### 安装依赖

在编译工作空间之前，需要安装相关的依赖。通过packages.osrfoundation.org的源安装依赖：

```bash
   sudo wget https://packages.osrfoundation.org/gazebo.gpg -O /usr/share/keyrings/pkgs-osrf-archive-keyring.gpg
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/pkgs-osrf-archive-keyring.gpg] http://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/gazebo-stable.list > /dev/null
   sudo apt-get update
```

下面通过apt安装依赖：

```bash
   sudo apt -y install \
  $(sort -u $(find . -iname 'packages-'`lsb_release -cs`'.apt' -o -iname 'packages.apt' | grep -v '/\.git/') | sed '/gz\|sdf/d' | tr '\n' ' ')
```

### 编译Gazebo

依赖已经全部装好，接下来就可以编译Gazebo了：

```bash
   cd ~/workspace
   colcon graph
```

命令行会输出ROS 2 packages的构建依赖图。在此之前，我们安装仿真iiwa需要的包ros_gz。

```bash
   cd src/
   # 设置gazebo版本号
   export GZ_VERSION=garden
   # 下载源码
   git clone https://github.com/gazebosim/ros_gz.git -b ros2
   git clone -b ahcorde/rename/ign_to_gz https://github.com/ros-controls/gz_ros2_control.git
```

接下来，我们安装依赖：

```bash
   cd ~/workspace
```

如果rosdep还没有初始化，执行如下命令：

```bash
   sudo rosdep init
   rosdep update
```

再执行：

```bash安装位置
   rosdep install -r --from-paths src -i -y --rosdistro rolling

```

看到命令行提示依赖全部安装成功且没有出错的时候，执行编译命令：

```bash
   colcon build --merge-install
```

注意此处如果终端卡死或者系统卡死，要为上述命令添加线程数量限制，如：

```bash
   colcon build --merge-install --parallel-workers 2
```

如果还会卡死，试着改成单线程：

```bash
   colcon build --merge-install --executor sequential
```

观察到编译成功的提示后，gazebo就安装成功了。如果看到各种报错信息，请在issue中提出。
将gazebo的环境变量添加到.bashrc中：

```bash
   echo "source ~/workspace/install/setup.bash" >> ~/.bashrc
   source ~/.bashrc
```

运行仿真：

```bash
   ros2 launch iiwa_bringup iiwa_bringup.launch.py use_sim:=true
```

## 3. Moveit2安装

Moveit2 中添加了伺服的功能，还附带了避障，同时还兼容Moveit本身的规划，用起来很方便。

关于servo的介绍可以参考这里[Moveit2 Servo](https://moveit.picknik.ai/humble/doc/examples/realtime_servo/realtime_servo_tutorial.html)。

### 安装依赖

接下来安装Moveit2，具体安装过程参考：[Moveit2安装](https://moveit.ros.org/install-moveit2/source/)。初始化安装器：

```bash
   sudo apt update
   sudo apt dist-upgrade
   rosdep update
```

通过apt和pip安装依赖：

```bash
   sudo apt install -y \
   build-essential \
   cmake \
   git \
   libbullet-dev \
   python3-colcon-common-extensions \
   python3-flake8 \
   python3-pip \
   python3-pytest-cov \
   python3-rosdep \
   python3-setuptools \
   python3-vcstool \
   wget && \
   # install some pip packages needed for testing
   python3 -m pip install -U \
   argcomplete \
   flake8-blind-except \
   flake8-builtins \
   flake8-class-newline \
   flake8-comprehensions \
   flake8-deprecated \
   flake8-docstrings \
   flake8-import-order \
   flake8-quotes \
   pytest-repeat \
   pytest-rerunfailures \
   pytest
```

看到安装成功提示后，进行接下来的步骤。卸载之前通过apt安装的moveit：

```bash
   sudo apt remove ros-$ROS_DISTRO-moveit*
```

### 编译工作空间

创造一个工作空间，用于存放Moveit2的源码：

```bash
   export COLCON_WS=~/ws_moveit2/
   mkdir -p $COLCON_WS/src
   cd $COLCON_WS/src
```

下载源码：
**Foxy, Galactic, Humble - stable**

```bash
   git clone https://github.com/ros-planning/moveit2.git -b $ROS_DISTRO
   for repo in moveit2/moveit2.repos $(f="moveit2/moveit2_$ROS_DISTRO.repos"; test -r $f && echo $f); do vcs import < "$repo"; done
   rosdep install -r --from-paths . --ignore-src --rosdistro $ROS_DISTRO -y
```

**Middleware**

```bash
   sudo apt install ros-$ROS_DISTRO-rmw-cyclonedds-cpp
   export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
```

如果之后也要继续使用这个Middleware，可以将其添加到.bashrc中：

```bash
   echo "export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp" >> ~/.bashrc
   source ~/.bashrc
```

编译工作空间：

```bash
   cd $COLCON_WS
   colcon build --event-handlers desktop_notification- status- --cmake-args -DCMAKE_BUILD_TYPE=Release
```

看到编译成功后，将moveit2添加到.bashrc中：

```bash
   echo "source ws_moveit2/install/setup.bash" >> ~/.bashrc
   source ~/.bashrc
```

这样就安装好Moveit2了。

## 4. iiwafir2编译

在校园网环境下下载源码：

```bash
   mkdir -p ~/iiwa_ws/src
   cd ~/iiwa_ws/src
   git clone https://git.robotics-hitsz.com/iiwa/iiwafri2.git
```

如果下载不下来，可以复制git链接去网页上下载压缩包。接下来编译：

```bash
   cd ~/iiwa_ws
   colcon build
```

看到编译成功的信息后，我们就完成了全部的安装过程。

## 5. 测试

### 启动Moveit2

最好把 moveit 和 Gazebo 都放到单独的工作空间里，然后把这两个的source写到 .bashrc中

这里如果是仿真，就要把use_sim_time改成true， 如果是实际可以不带这个参数，默认是false。（实机我还没有测试过）

```bash
   ros2 launch iiwa_moveit_config iiwa_moveit.launch.py use_sim_time:=true
```

### 启动Moveit2 servo

运行程序：

```bash
   ros2 launch iiwa_moveit_config iiwa_servo.launch.py
```

实际使用的时候要把iiwa_moveit_config/config/iiwa_l_servo.yaml和iiwa_moveit_config/config/iiwa_l_servo.yaml这两个文件中的use_gazebo改成false。

### 测试Servo

运行程序：

```bash
   ros2 run iiwa_servo_test iiwa_servo_test
```

然后往 `/left/servo_node/test` 和 `/right/servo_node/test` 这两个话题里发消息就可以了。

### 自定义运行学逆解

可以修改 `iiwa_moveit_plugin` 中src的 `chiniksolver_vel_mimic_svd.cpp` 这个文件的 `CartToJnt` 这个函数。

然后在 `iiwa_moveit_config` 中 `config/kinematics.yaml` 文件里的 `kinematics_solver` 改成 `iiwa_kinematics_plugin/IIwaKinematicsPlugin` 就可以。

如果想换回原来默认的，改成 `kdl_kinematics_plugin/KDLKinematicsPlugin` 。

_KUKA Programming password:_ `kuka`

_KUKA Advanced password:_ `argus`

## 6. 实时系统安装

### RealTime内核安装

reference: https://docs.ros.org/en/humble/Tutorials/Miscellaneous/Building-Realtime-rt_preempt-kernel-for-ROS-2.html

> If there is a Error like

```bash
   make[2]: *** [debian/rules:6: build] Errore 2
   dpkg-buildpackage: Errore: debian/rules build subprocess returned exit status 2
   make[1]: *** [scripts/Makefile.package:77: deb-pkg] Errore 2
   make: *** [Makefile:1464: deb-pkg] Errore 2
```

reference: https://askubuntu.com/questions/1327749/error-in-compiling-installing-realtime-kernel-on-ubuntu-20-04-2-lts

### Set permission of scheduler and memory lock

reference: https://docs.ros.org/en/humble/Tutorials/Demos/Real-Time-Programming.html

### Nvidia error in RealTime kernel

Follow this insturction

https://github.com/ApolloAuto/apollo/blob/master/docs/01_Installation%20Instructions/how_to_install_apollo_kernel_cn.md

## 7. KUKA Configuration

```
Right iiwa
   IP : 192.168.168.20

   R Port : 30200

Left iiwa
::
   L IP : 192.168.168.10

   L Port : 30201
```
