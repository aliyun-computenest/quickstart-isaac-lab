# Isaac Lab standalone deployment documentation

## Overview

Built on top of Isaac Sim, Isaac Lab provides a unified and flexible learning framework for robotics using the latest simulation technology. It is designed to be modular and extensible, and aims to simplify common workflows in robotics research (such as reinforcement learning, demonstration learning, and motion planning). Although it includes some pre-built environments, sensors, and tasks, its main goal is to provide an open source, unified, and easy-to-use interface for developing and testing custom environments and robot learning algorithms.


## Billing Description

Issac Lab's expenses on computing nests mainly relate:

-Selected vCPU, GPU, and memory specifications
-System disk type and capacity
-Public network bandwidth

Billing methods include:

-Pay-As-You-Go (hours)
-Package year and package month

The estimated cost can be seen in real time when the instance is created.

## Service Architecture

![img.png](images-en/img.png)

## Permissions required for RAM accounts

The Isaac Lab service needs to access and create resources such as ECS and VPC. If you use a RAM user to create a service instance, you need to add the corresponding resource permissions to the account of the RAM user before creating the service instance. For details about how to add RAM permissions, see [Authorize RAM users](https://help.aliyun.com/document_detail/121945.html). The required permissions are shown in the following table.


| Permission policy name | Comment |
| -------------------------------- | --- |
| AliyunECSFullAccess | Permissions to manage ECS instances |
| AliyunVPCFullAccess | Permissions to manage a VPC |
| AliyunROSFullAccess | Manage permissions for Resource Orchestration Service (ROS) |
| AliyunComputeNestUserFullAccess | Manage user-side permissions for the compute nest service (ComputeNest) |


## Deployment process

### Deployment steps
#### Step 1: Select Configuration
1. Select the service template, and select the stand-alone version here.

![img_32.png](images-en/img_32.png)
2. The name of the service instance can be customized or the default can be used.
2. Region, it is recommended to select nearby to obtain better network delay
3. Payment type, select Pay-As-You-Go or Package
4. For the instance type, you need to select a GPU instance. It is officially recommended to use the ecs.gn6i-c40g1.10xlarge instance specification. For details, please refer to the Aliyun Deployment Document on the official website of Isaac sim [Alibaba Cloud Deployment](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/install_advanced_cloud_setup_alibaba.html)

![img_1.png](images-en/img_1.png)
5. Instance password, which is used to set the password of the ECS instance.
6. In the available zone configuration, you can select the corresponding available zone under the region, select the existing or new VPC network

![img_2.png](images-en/img_2.png)

#### Step 2: Create a Service Instance
1. After selecting the configuration required by the service, click Create Now to create a service instance.

![img_3.png](images-en/img_3.png)
2. After the service instance is created and initiated, the Service Instance List page is displayed, where you can view the deployment progress of the service instance.

![img_4.png](images-en/img_4.png)

#### Step 3: View Instance Details
1. When the service instance status changes to "Deployed", click the service instance name to enter the corresponding service instance details page.

![img_5.png](images-en/img_5.png)
2. Service Instance Details In the "Use Now" module, you can see the IP address of the server. For specific usage methods, see the service deployment and usage instructions above.

![img_6.png](images-en/img_6.png)

## Use the tutorial
The Isaac Lab service instance has a complete built-in Isaac Sim application and supports two training modes: either independently using Isaac Sim for simulation training or intensive learning training based on the Isaac Lab framework.

The corresponding ECS instance is installed with the Ubuntu graphical interface and can be used directly in the ECS console through VNC.
### Access an ECS instance through VNC
Currently, there are two ways to access Ubuntu instances. We recommend that you use VncServer VncRealViewer to access Ubuntu instances. You can easily adjust the resolution and use it more smoothly.
#### Access VncServer VncRealViewer (recommended)
1. On the Service Instance Details page, find the corresponding ECS instance and click Remote Connection to log on.

![img_28.png](images-en/img_28.png)

2. Log on to the ECS instance and run the following command:
shell
Switch to the root account
sudo su root
# Set the VNC service password. Note that the maximum password length is 8 digits
/opt/TurboVNC/bin/vncpasswd
# Start the VNC Server service, the service will listen 5901 port, the installation group has been preset to open
/opt/TurboVNC/bin/vncserver :1 -geometry 1920x1080 -depth 24 -xstartup ~/.vnc/xstartup
'''
3. Download [VncRealViewer Client](https://www.realvnc.com/en/connect/download/viewer/) and enter <Server Public Network IP>:5901 to connect to the server.

![img_29.png](images-en/img_29.png)

![img_30.png](images-en/img_30.png)

4. In the/home/isaac-sim/isaacsim path, run the./isaac-sim.sh -- allow-root command to open the Isaac Sim interface.

![img_31.png](images-en/img_31.png)

#### Ecs console comes with native VNC login method
1. On the Service Instance Details page, find the corresponding ECS instance in Resources, and click Go to ECS Console.

![img_17.png](images-en/img_17.png)

2. Click the remote connection in the upper right corner and select the VNC login method to enter the graphical interface of the Ubuntu system.

![img_18.png](images-en/img_18.png)

3. Enter the logon password corresponding to the isaac-sim account. The password is the same as the ECS instance password. You can go to the service instance overview page to view the password.

![img_19.png](images-en/img_19.png)

### How to use Isaac Sim
After logging on to the ECS instance in the above way, open the Terminal and you can see the isaacsim and isaacsim_assets directories under the isaac-sim account.
-The isaacsim directory is the installation directory of Isaac Sim, which contains Isaac Sim-related startup and training scripts.
-The isaacsim_assets directory is Isaac Sim's resource directory, which has been downloaded in advance for later training.

![img_21.png](images-en/img_21.png)

#### Example 1: Scene synthesis dataset generation without GUI
This example demonstrates the process of generating a synthetic dataset using the omni.replicator extension. The resulting data will be stored offline (on disk), making it available for the training of the deep neural network. The examples can be run in Isaac Sim's Python standalone environment and use Isaac Sim and Replicator to create offline synthetic datasets for training ML models. It is recommended to copy the official sample code to the user directory for modification and use.
shell
cd /home/isaac-sim
mkdir -p isaacsim_test
cd /home/isaac-sim/isaacsim_test
mkdir -p scene_based_sdg
cp -rf /home/isaac-sim/isaacsim/standalone_examples/replicator/scene_based_sdg/* /home/isaac-sim/isaacsim_test/scene_based_sdg/
## Render composition (-- config specifies the configuration file path, where headless = true is set; --/persistent/isaac/asset_root/default specifies the 3D asset storage path)
/home/isaac-sim/isaacsim/python.sh ./scene_based_sdg/scene_based_sdg.py --config="/home/isaac-sim/isaacsim_test/scene_based_sdg/config/config_coco_writer.yaml" --/persistent/isaac/asset_root/default="/home/isaac-sim/isaacsim_assets/Assets/Isaac/4.5"
'''
The build results are stored in "./isaacsim_test/_out_coco" and visualized as follows:
![img_23.png](images-en/img_23.png)![img_22.png](images-en/img_22.png)

#### Example 2: Using Isaac Sim as a GUI
Execute the following command in the termial to enter the GUI interface of Isaac Sim.
shell
cd /home/isaac-sim/isaacsim
./isaac-sim.sh
'''
It should be noted here that Isaac Sim will be slower to start, and a waiting window will pop up. There is no need to operate, just wait for a period of time.

![img_24.png](images-en/img_24.png)

The following are the steps in the [Getting Started](https://docs.isaacsim.omniverse.nvidia.com/4.5.0/introduction/quickstart_isaacsim.html) to create a cube.

![img_25.png](images-en/img_25.png)

### How to use the Isaac Lab
The Isaac Lab service installation directory is located in/home/isaac-sim/IsaacLab, which contains the installation directory and startup scripts for Isaac Lab.

#### Example 1: Train the intelligences using GUI-free mode
In this case, we will use Stable-Baselines3 reinforcement learning (RL) framework to solve the agent task of Cartpole balance control. Stable-Baselines3 is a PyTorch-based reinforcement learning library that provides a variety of stable and easy-to-use RL algorithms, such as PPO, SAC, DQN, etc. The training goal is to let the intelligent body learn how to control the left and right movement of the car and keep the swing rod upright. We recommend that you copy the sample code to your personal directory for modification and debugging.
shell
cd /home/isaac-sim
mkdir -p isaaclab_test
cd /home/isaac-sim/isaaclab_test
mkdir -p sb3
cp -rf /home/isaac-sim/IsaacLab/scripts/reinforcement_learning/sb3/* /home/isaac-sim/isaaclab_test/sb3/
## Render composition (-- config specifies the configuration file path, where headless = true is set; --/persistent/isaac/asset_root/default specifies the 3D asset storage path)
/home/isaac-sim/IsaacLab/isaaclab.sh -p ./sb3/train.py --task Isaac-Cartpole-v0 --num_envs 64 --headless --video
'''
The training results are saved to./logs/sb3/Isaac-Cartpole-v0; the visualization results are as follows
![img_26.png](images-en/img_26.png)

#### Example 2: Generating Basic Objects in a Scene Using GUI Mode
Run the following command in the Terminal to enter the GUI interface of Isaac Lab.
shell
cd /home/isaac-sim/IsaacLab
./isaaclab.sh -p scripts/tutorials/00_sim/spawn_prims.py
'''
![img_27.png](images-en/img_27.png)