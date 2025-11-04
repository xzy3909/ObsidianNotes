1. 环境配置
	1. 依赖安装
		- sudo apt-get update
		- sudo apt-get install -y clang cmake g++ git google-mock libgmock-dev libboost-all-dev libcairo2-dev libcurl4-openssl-dev libeigen3-dev libgflags-dev libgoogle-glog-dev liblua5.2-dev libsuitesparse-dev lsb-release ninja-build stow
	2. abseil-cpp安装
		- cd abseil-cpp
		- mkdir build && cd build
		- cmake -G Ninja -DCMAKE_POSITION_INDEPENDENT_CODE=ON -DCMAKE_INSTALL_PREFIX=/usr/local/stow/absl ..
		- ninja
		- sudo ninja install
		- cd /usr/local/stow
		- sudo stow absl
		- cd ~
		- sudo apt-get update
		- sudo apt-get install -y python3-wstool python3-rosdep ninja-build stow
	3. cartographer安装
		- mkdir cartographer
		- cd cartographer_ws
		- mkdir src
		- 将解压后的文件夹里面的cartographer、cartographer_ros、ceres-solver这三个文件夹放入src目录
		- rosdep install --from-paths src --ignore-src --rosdistro=${ROS_DISTRO} -y
		- catkin_make_isolated --install --use-ninja
		- source install_isolated/setup.bash
2. Reference
	https://pan.baidu.com/s/144ItzZyljRe5JkJ72NNvpA 密码: dv8h