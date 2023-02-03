.. index::
   single: Gazebo
==========
Gazebo tutorials
==========
Gazebo is an open source 3D Dynamic simulator used for robotics development. It offers a wide of sensors and interfaces as well as multiple physics engines. In this video, I will introduce you to Gazebo and explain about its importance for robotics and I will show you how to install it and launch it on your system.

Terminal code for installation:
    - 1- OSRF repository installation ▶ sudo sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu `lsb_release -cs` main" ＞ /etc/apt/sources.list.d/gazebo-latest.list'
    NOTE: If the  "＞" symbol in the above code line caused a problem, please replace it by a small greater than symbol using your keyboard. (I am not allowed to include the small greater than symbol in my YouTube video description).
    - 2- OSRF repository key setup ▶ wget https://packages.osrfoundation.org/ga... -O - | sudo apt-key add -
    - 3- Gazebo package installation ▶
        sudo apt-get update
        sudo apt-get install gazebo9
        sudo apt-get install libgazebo9-dev

Share This Video ▶ https://youtu.be/O0729K-7VEY

Useful links:
Gazebo answers ▶ https://answers.gazebosim.org/questions/
