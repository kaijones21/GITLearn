# Navigation Description 
The folders shown here are a couple packages within the Heron simulator. The files added and modified in this simulator are in their respective packages subfolders. Files that have a number (e.g. `control2.launch`) are intended to be placed in the packages as *additional* files. The files that have the same name as the files in the packages already are intended to replace the original (e.g. `spawn_heron.launch` should replace the `spawn_heron.launch` already located in the `($find heron_gazebo)/launch` folder). It is suggested to make a copy of the original file (e.g. `cp spawn_heron.launch spawn_heron.launch.backup`) before replacing the file. Below shows more details of each file and their purpose.

## File Tree

- ($find heron_gazebo)/
    - launch/
        - spawn_heron.launch
        - localization_error.launch
    - scripts/
        - filter_rpy_translator (*executable node*)
        - localization_error (*executable node*)
- ($find heron_control)/
    - config/
        - diffdrive.yaml 
        - navsat_config.yaml
        - robot_localization2.yaml
    - launch/
        - control2.launch
        - usv_joydrive.launch
- ($find heron_description)/ 
    - urdf/
        - sensors.xacro (*executable*)

## Dependancies
- Joy_teleop package

## File changes 

### spawn_heron.launch
This file was modified to initiate additional IMU filter nodes, IMU quaternion to RPY conversion nodes, launch the joystick launch file, additional navsat/velocity conversion nodes from Vector3Stamped to TwistStamped messages, launch our EKF nodes, and our localization_error launch file. 

### localization_error.launch 
This file is used to launch multiple error calculation nodes to publish the calculated EKF estimation errors with respect to the `pose_gt` node that the Heron simulator publishes by default. 

- Remapping Inputs
    - `estimate`: EKF estimation topic 
    - `estimate/rpy`: EKF orientation estimate as roll, pitch, yaw 
- Remapping Outputs
    - `estimateError`: topic name that publishes estimation error from ground truth

### filter_rpy_translator
This node converts the EKF's orientation estimation from quaternions in an Odometry message to roll, pitch, yaw in a Vector3Stamped message. 

- Inputs:
    - `estimate`: EKF estimation topic (*Odometry*)
- Outputs:
    - `estimate/rpy`: EKF orientation estimate in rpy (*Vector3Stamped*)

### localization_error
This node calculates the error between the localization estimate and the ground truth topic. 

- Inputs:
    - `pose_gt`: ground truth topic (*Odometry*)
    - `pose_GT/rpy`: ground truth orientation in roll, pitch, yaw (*Vector3Stamped*)
    - `estimate`: EKF estimation (*Odometry*)
    - `estimate/rpy`: EKF orientation estimation in roll, pitch, yaw (*Vector3Stamped*)

- Outputs:
    - `estimateError`: EKF's estimation error (*Odometry*)

### diffdrive.yaml 
Configuration file to configure parameters for the teleop_joy node used to control the Heron with a joystick. 

### navsat_config.yaml 
Modified to not include the UTM transform and publish as parent frame. This is to link the Gazebo world frame as the "map" frame for the EKF nodes. 

### robot_localization2.yaml 
Configuration file to configure all EKF nodes used for each situation described in the paper. This file follows the recommended convention of using one "odom" frame and one "map" frame for each EKF estimate. 

### control2.launch
Used to launch the Navsat transform nodes for the additional GPS and IMU sensors, EKF nodes, the Navsat velocity covariance nodes, and quaternion-to-rpy conversion nodes for the EKFs. 

### usv_joydrive.launch
Launch file to initialize `joy_teleop` node to control the Heron simulation. 

### sensors.xacro
File to define and launch additional sensors for the Heron simulation.