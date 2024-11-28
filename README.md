# Baseline-framework-and-tutorial-for-an-autonomous-drone
Autonomous navigation for a drone (quadrotor) - a comprehensive framework integrating vision inertial odometry, trajectory planning and tracking control.
(Before the following toturials please refer to the hardware_list.xlsx to see the specific hardware firstly)

## Algorithms and Papers
The whole framework includes Kinodynamic path planning and B-spline-based trajectory optimization. This toturial builds on several papers (algorithms):
- [__KinoJGM: A framework for efficient and accurate quadrotor trajectory generation and tracking in dynamic environments__](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=9812352&casa_token=NtGx-UFm0XIAAAAA:tsefk1JEYnOmA4zo6nku67lRrie3bVqvYzDVn4SF3vMjR4bEvxSipUJUl8nve70Po51UH95ZNA&tag=1), Wang, Yanran, James O'Keeffe, Qiuchen Qian, and David Boyle, IEEE International Conference on Robotics and Automation (__ICRA__), 2022.
- [__Ego-planner: An esdf-free gradient-based local planner for quadrotors__](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=9309347&casa_token=T80lv299Sz8AAAAA:ytNanEEBCMYHoH3iJdWG5m8dgDFWIFdbsPHzkiEsSPHfu4wWVXAappiRoxgmDXvKXl2AP3LhEQ), Zhou, Xin, Zhepei Wang, Hongkai Ye, Chao Xu, and Fei Gao, IEEE Robotics and Automation Letters (__R-AL__), 2020.

## Flight Controller, Specifically Pixhawk 4 (mini), Setup
- **Firmware Version Info**
  - Use the firmware located at `/firmware/px4_fmu-v5_default.px4`, which is compiled from the official version v1.11.0 of PX4. Please note that Firmware v1.13 is not suitable for this project. The older firmware version has not been tested.

- **IMU Frequency Configuration**
  - Add the following commands to `/etc/extras.txt` in the root directory:
    ```plaintext
    mavlink stream -d /dev/ttyACM0 -s ATTITUDE_QUATERNION -r 200
    mavlink stream -d /dev/ttyACM0 -s HIGHRES_IMU -r 200
    ```
    by running the following command in terminal

      ```bash
      sudo sh -c 'echo "mavlink stream -d /dev/ttyACM0 -s ATTITUDE_QUATERNION -r 200\nmavlink stream -d /dev/ttyACM0 -s HIGHRES_IMU -r 200" >> /etc/extras.txt'
      ```
- **Airframe Selection**
  - Install [QGroundControl](http://qgroundcontrol.com/) on your Laptop, launch it, connect it with your Pixhawk via USB-C, select the appropriate airframe based on the right wheelbase (from QGroungControl). Then, go to QGroundControl settings, search for following parameters and set the them to the following values:
    - `SER_TEL1_BAUD`: 921600
    - `CBRK_IO_SAFETY`: 22027
    - `SYS_USE_IO`: 0

There is no need to configure `SYS_USE_IO` for Pixhawk 6C. It does not exist and is not needed as mentioned [here](https://discuss.px4.io/t/pixhawk-6c-and-spektrum-receiver-not-communicating-after-updating-to-v1-14/37841/2). 
- **Motor Rotation Checking**
  - Check that propellers are NOT installed!!!

- **First Flight in Self-Stabilizing Mode**
  - Ensure that you seek assistance from someone familiar with the self-stabilizing mode during the first flight.

## Installation Ubuntu20.04 & 18.04
- Ubuntu 20.04 is recommended but 18.04 works with slight changes of code as well
- Please following the links: https://phoenixnap.com/kb/install-ubuntu-20-04

## Environmental Configuration of the on-board Computer
- **ROS Installation**
  - Follow the official installation steps for ROS Noetic on Ubuntu 20.04: [http://wiki.ros.org/noetic/Installation/Ubuntu](http://wiki.ros.org/noetic/Installation/Ubuntu)
    - For Ubuntu 18.04, refer to: [http://wiki.ros.org/melodic/Installation/Ubuntu](http://wiki.ros.org/melodic/Installation/Ubuntu)
  - Test ROS installation by opening three terminals and running the following commands:
    - `roscore`
    - `rosrun turtlesim turtlesim_node`
    - `rosrun turtlesim turtle_teleop_key`
- **Depth Camera (Realsense D435i) Driver Installation**
  - Follow the instructions provided [here](https://github.com/IntelRealSense/librealsense/blob/master/doc/distribution_linux.md) to install the required packages for the Realsense D435i camera.
  - Test the successful installation by running the command: `realsense-viewer`
  - Note that both the USB port and USB cable should support USB 3.0.
- **Mavros Installation**
  - Run `sudo apt-get install ros-noetic-mavros` to install Mavros.
  - Navigate to the Mavros directory: `cd /opt/ros/noetic/lib/mavros`.
  - Run `sudo ./install_geographiclib_datasets.sh` to install the required geographiclib datasets.
- **Installation of Ceres, Glog, and DDynamic-Reconfigure**
  - Unzip the `3rd_party.zip` file.
  - Open the terminal and navigate to the `glog` directory.
  - Run `chmod +x ./autogen.sh` and `chmod +x ./configure`
  - Run `./autogen.sh && ./configure && make && sudo make install`.
  - Install the required dependencies: `sudo apt-get install liblapack-dev libsuitesparse-dev libcxsparse3.1.2 libgflags-dev libgoogle-glog-dev libgtest-dev`.
  - Open the terminal and navigate to the `ceres` directory.
  - Create a `build` directory: `mkdir build`.
  - Navigate to the `build` directory: `cd build`.
  - Run `cmake ..` to generate the build files.
  - Run `sudo make -j4` to compile Ceres.
  - Run `sudo make install` to install Ceres.
  - Install `ros-noetic-ddynamic-reconfigure`: `sudo apt-get install ros-noetic-ddynamic-reconfigure`.
- **Download the Trajectory Planner Code and Compile**
  - Clone the Ego-Planner repository: `https://github.com/Alex-yanranwang/Sysal-Autonomous-Drone.git`.
  - Navigate to the repository directory: `cd Sysal-autonomous-drone`.
  - Run `catkin_make` to compile the code.
  - Source the workspace setup file: `source devel/setup.bash`.
  - Launch the simulation: `roslaunch ego_planner single_run_in_sim.launch`.
  - Press the `G` key on the keyboard in Rviz, then click the left mouse button to select the target point for the drone.

## Installation on the tools of network communication and configration
- **VSCode Installation**
  - Install the `.deb` file using the following command: `sudo dpkg -i ***.deb`.
- **Terminator Installation**
  - Install Terminator using the command: `sudo apt install terminator`.
- **SSH Configuration**
  - Install OpenSSH server: `sudo apt install openssh-server`.
  - On your PC/notebook, ping the IP address `192.168.**.**`.
  - Open the hosts file: `sudo gedit /etc/hosts`.
  - Add the following line at the end: `192.168.**.** sysal-autonomous-drone`.
  - Ping the hostname: `ping sysal-autonomous-drone`.
  - Connect to the remote host via SSH: `ssh sysal-autonomous-drone@sysal-autonomous-drone` (`ssh username@alias`).
- **ROS Multiple Machines Configuration** 
  - Purpose:
    - Decrease the computation pressure on on-board computers.
    - Enable swarm aerial robotics by distributing computation across on-board computers and ground stations.
  - Follow the steps provided in the ROS tutorial for multiple machines: [http://wiki.ros.org/ROS/Tutorials/MultipleMachines](http://wiki.ros.org/ROS/Tutorials/MultipleMachines).
- **NoMachine Installation**
  - Download the `.deb` file on both the on-board computers and your ground station (e.g., laptop).
  - Install the downloaded `.deb` file using the following command: `sudo dpkg --i ***.deb`.

## Preception Configuration: Vision Inersial Odometry (VIO, i.e., VINS) Settings
- **Check Connection**
  - Run `ls /dev/tty*` to confirm the serial port connection of the flight controller (usually `/dev/ttyACM0`).
  - Give serial port permissions using `sudo chmod 777 /dev/ttyACM0`.
  - Launch the mavros node: `roslaunch mavros px4.launch`.
  - Check the IMU frequency transmitted by the flight controller with `rostopic hz /mavros/imu/data_raw` (should be around 200Hz).
- **Check Realsense Driver**
  - Launch the Realsense camera driver: `roslaunch realsense2_camera rs_camera.launch`.
  - Open the remote desktop and launch `rqt_image_view`.
  - Check if `/camera/infra1/image_rect_raw`, `/camera/infra2/image_rect_raw`, and `/camera/depth/image_rect_raw` topics are displaying images correctly.
- **VINS Parameter Configuration**
  - Check the `realflight_modules/VINS_Fusion/config/` folder.
  - Obtain `fx`, `fy`, `cx`, and `cy` values from `rostopic echo /camera/infra1/camera_info` and fill in `left.yaml` and `right.yaml` in the same folder.
  - Create a `vins_output` folder in the home directory.
  - Modify the `sysal-drone.yaml` file. Adjust the fourth column of `data` in `body_T_cam0` and `body_T_cam1` to match the actual extrinsic parameters of your drone's camera relative to the flight controller, in meters.
- **Accurate self-calibration of VINS external parameters**
  - Run `sh shfiles/rspx4.sh`.
  - Listen to the topic `/vins_fusion/imu_propagate` using `rostopic echo`.
  - Slowly move the robot in the field while picking it up. Avoid changing lighting conditions or using flickering light sources. Add clutter to increase feature points for VINS matching.
  - Replace the content in `vins_output/extrinsic_parameter.txt` with the values of `body_T_cam0` and `body_T_cam1` from `sysal-drone.yaml`.
  - Repeat the above steps until the odometer data deviation of VINS converges to a satisfactory value (usually within 0.3 meters).
- **Mapping test**
  - Run `sh shfiles/rspx4.sh`.
  - Launch the ego planner: `roslaunch ego_planner single_run_in_exp.launch`.
  - Open the remote desktop and launch `roslaunch ego_planner rviz.launch`.

## Trajectory Planning and Tracking Control Configuration: Ego-Planner Setting
- **Navigate to the route `src/planner/plan_manage/launch/single_run_in_exp.launch`**
  - `map_size`: Modify this parameter when your map is large. Note that the target point should not exceed half the map size.
  - `fx/fy/cx/cy`: Actual internal parameters of your depth camera.
  - `max_vel/max_acc`: Maximum speed and acceleration. It is recommended to start with 0.5 for testing. The maximum speed should not exceed 2.5, and the acceleration should not exceed 6.
  - `flight_type`: Use 1 for RViz point selection mode, and 2 for waypoints tracking mode.
- **Navigate to the route `src/planner/plan_manage/launch/advanced_param_exp.xml`**
  - `resolution`: Represents the resolution of the grid points in the raster map, in meters. Smaller values result in a finer map but require more memory. The minimum should not be lower than 0.1.
  - `obstacles_inflation`: Represents the expansion size of the obstacle, in meters. It is recommended to set it at least 1.5 times the radius of the aircraft (including the propeller), but not more than 4 times the `resolution`. If the wheelbase of the aircraft is larger, please increase the `resolution`.
- **Navigate to the route `src/realflight_modules/px4ctrl/config/ctrl_param_fpv.yaml`**
  - `mass`: Actual weight of the drone.
  - `hover_percent`: The hovering throttle of the drone. It can be viewed through px4log. For details, please refer to the [document](https://www.bookstack.cn/read/px4-user-guide/zh-log-flight_review.md). If your drone is the same as the course, keep this at 0.3. If the power configuration, weight, or wheelbase is changed, please adjust this parameter. Otherwise, automatic takeoff may fail or there may be significant overshoot.
  - `gain/Kp,Kv`: P and I values of the PID controller. Generally, no major changes are required. If overshoot occurs, please adjust them appropriately. If the drone responds slowly, please adjust them accordingly.
  - `rc_reverse`: No changes are required if using AT9S. If the flight direction of the aircraft is opposite to the direction of the joystick, modify this parameter. Change the value corresponding to the opposite channel to `true`. Please confirm the throttle before taking off:
    - Launch `roslaunch mavros px4.launch`.
    - Run `rostopic echo /mavros/rc/in`.
    - Turn on the remote control and gradually increase the throttle from the lowest to the highest.
    - Observe which item in the echo message changes slowly (this item represents the throttle channel value) and check if it changes from small to large.
    - If it changes from small to large, no modification is needed for the throttle's `rc_reverse`. Otherwise, change it to `true`.
    - The same steps apply to other channels.

## Flight Experiments
- **Automatic takeoff**
  - Run `sh shfiles/rspx4.sh`.
  - Listen to the topic `/vins_fusion/imu_propagate` using `rostopic echo`.
  - Slowly move the robot in a small range and put it back in place, ensuring VINS has a small deviation.
  - Set channel 5 of the RC to the inside and channel 6 to the lower side. Keep the throttle centered.
  - Launch the PX4 controller: `roslaunch px4ctrl run_ctrl.launch`.
  - Run `sh shfiles/takeoff.sh`. Adjust the `hover_percent` parameter if the drone cannot take off or if it flies over 1 meter before descending.
  - Control the position of the drone using the controls similar to DJI.
  - To land, set the throttle to the lowest level. After the drone is on the ground, set channel 
- **Real Flight experiments**
  - Automatic takeoff (see the steps above)
  - Launch trajectory planning: `roslaunch ego-planner single_run_in_exp.launch`
  - Record the flight data in preception and planning processes: `sh shfiles/record.sh`
  - Enter remote desktop (base on the above installation of ROS Multiple Machine), `roslaunch ego_planner rviz.launch`
  - Press the G key and the left mouse button to click the target point to make the drone fly

## Potential Issues & Answers during Practical Impementation
- **Can I use 265+435 to run VINS without issues?**
  - Yes, but the speed estimation of the direct output odometry from 265 may be problematic, which can lead to unstable control. You need to fuse 265 and IMU using EKF.

- **Can I replace the component mentioned in the hardware list?**
  - Please refer to the extra video, which explains most of the possible replacements.

  - If you want to replace the long wheelbase frame, you need to change the power system and propellers accordingly. PID parameters also need to be adjusted. Please consult relevant resources for further information.

- **Can I use the built-in IMU of D435i to run VINS?**
  - No, because the IMU of the 435 has significant noise.

- **Are there any changes in the provided v1.11.0 firmware? Is it necessary to use this firmware?**
  - There are no changes; it was directly downloaded from the PX4 official source. Currently, this version is the only one that has been tested with the code provided in this package. Testing on v1.13 has failed, resulting in frequent crashes of VINS. Other flight controllers or firmware versions have not been tested. If needed, you can conduct your own tests.

- **What should I do if the motors don't spin during the QGC motor test?**
  - Check if the ESCs support DShot; if not, please refer to the calibration methods for PWM ESCs.
  - If you are using the V5+ flight controller or any other flight controller that separates analog and digital outputs (characterized by output ports labeled A1-A4 and M1-M4), if you want to use DShot protocol, connect the ESCs to the A ports.
  - If you are using the Holybro Pixhawk4 complete set, connect the flight controller and power distribution board using the FMU PWM OUTPUT, not the I/O PWM OUTPUT.

- **I get an error message in red after running VINS. What should I do?**
  - Most likely, the error is due to a formatting issue in the modified configuration. Check the error message and make the corresponding changes in the configuration file.

- **After running VINS, I see the error "VINS_RESULT_PATH not opened." What should I do?**
  - Create a folder named "vins_output" in your home directory (if your username is not "sysal-drone," modify the "vins_out_path" in the config file to the absolute path of the folder you created).

- **What is the flight range of this aircraft?**
  - The range it can fly depends on the communication quality of your Wi-Fi. Typically, Wi-Fi can communicate up to a maximum of 100 meters. Additionally, since the grid map is directly loaded into memory, setting the map size too large can easily fill up the memory and cause other programs to run slowly. It is generally not recommended to exceed 50 meters by 50 meters.

- **Why do you need to block the structured light of the D435?**
  - The purpose of structured light is to provide more accurate depth maps for the camera. However, fixed pattern noise appears on the stereo image, which is detrimental to VIO (Visual-Inertial Odometry) operation. Therefore, it needs to be turned off.

- **What should I do if VINS drifts?**
  - Check if there are strong reflective objects (tiles, glass, etc.) in the environment.
  - Move the drone slowly and try to avoid moving objects in the scene.
  - Measure the initial extrinsic parameters as accurately as possible.
  - Avoid running RVIZ on a remote desktop while VINS is running (it consumes a lot of CPU resources). If necessary, consider setting up ROS multi-machine and running it on a laptop.
  - Check if there are flickering light sources in the environment (not visible to the naked eye, but can be checked in the monocular image of Realsense).

- **After modifying the etc/extra.txt file on the SD card according to the tutorial, the IMU frequency did not change to 200Hz. What should I do?**
  - The possible reason is that your firmware does not support such modifications. You can try executing the following commands after starting mavros:
    ```plaintext
    rosrun mavros mavcmd long 511 105 5000 0 0 0 0 0 & sleep 1
    rosrun mavros mavcmd long 511 31 5000 0 0 0 0 0 & sleep 1
    ```
- **How to install on Jetson Nano Orin Nano?**
- First of all, OpenCV requires 12GB Swap size. So, increase your swap size. You can easily find commands to increase Swap size using chatgp. These commands are placed here for reference:
  ```
  sudo swapoff -a
  wait
  
  sudo fallocate -l 12G /mnt/12GB.swap
  wait
  
  sudo chmod 600 /mnt/12GB.swap
  wait
  
  sudo swapon /mnt/12GB.swap
  wait
  
  sudo swapon --show
  wait
  
  echo '/mnt/12GB.swap swap swap defaults 0 0' | sudo tee -a /etc/fstab
  wait
  
  free -h
  wait
  ```
- Use Nvidia recommended link to install OpenCV 4.6.0 [here](https://github.com/AastaNV/JEP/blob/master/script/install_opencv4.6.0_Jetson.sh).
- The essential software environment is same as VINS-Fusion. Besides, it requires OpenCV cuda version.(Only test it on OpenCV 4.5.3 and 4.6.0 with CUDA/CUDNN), and it have to rebuild the cv_bridge after install the OpenCV4 so that the OutOfMemoryError will not happend. 
For opencv, add the path of the **OpenCVConfig.cmake** file and **cv_bridgeConfig.cmake** file to your following packages VINS-Fusion/camera_models/CMakeLists.txt (change Line 16), VINS-Fusion/loop_fusion/CMakeLists.txt (Line 19) and VINS-Fusion/vins_estimator/CMakeLists.txt (Line 9 and 19) by adding following lines right before `find_package(OpenCV 4 REQUIRED)` as:
```
set(OpenCV_DIR /home/mannan/Downloads/workspace/opencv-4.6.0/release) #/home/mannan/Downloads is where your [OpenCV 4.6.0 sh](https://github.com/AastaNV/JEP/blob/master/script/install_opencv4.6.0_Jetson.sh) file was placed. You need to modify it according to your computer name
set(cv_bridge_DIR /opt/ros/noetic/share/cv_bridge/cmake)
set(cv_bridge_DIR /home/mannan/Sysal-Autonomous-Drone/devel/share/cv_bridge/cmake)
```
**Note:** For [OpenCV 4.6.0](https://github.com/AastaNV/JEP/blob/master/script/install_opencv4.6.0_Jetson.sh), built from source the defaulc `cv_bridge` insalled does not work. You must clone that package from the main repository using 
```
git clone --branch noetic https://github.com/ros-perception/vision_opencv.git
```
and build it in your respository. Otherwise, the default path setting `set(cv_bridge_DIR /opt/ros/noetic/share/cv_bridge/cmake)` will throw an error `OutOfMemoryError`. 


