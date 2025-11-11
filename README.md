# Gazebo an Ardupilotplugin Setup
Gazebo and ArduPilot setup, including some model and world configurations.

Ubuntu 22.04
Gazebo Sim Harmonic, version 8.10.0
ArduPilot-4.6.0
NOTE: The ArduPilot plugin does not depend on ROS. Therefore, in this setup, we will skip ROS installation for now. In the future, it may be necessary.

All the documents using this setup are listed below:
https://gazebosim.org/docs/harmonic/install_ubuntu/ gazebo harmonic installation
https://ardupilot.org/dev/docs/sitl-with-gazebo.html ardupilot+gazebo plugin
https://discuss.ardupilot.org/uploads/default/original/2X/d/d7083377f747deab79798e7a9cef58d433ff5f66.pdf multiple drones

## Gazebo Harmonic Installation
First install some necessary tools:
```bash
sudo apt-get update
sudo apt-get install curl lsb-release gnupg
```

Then install Gazebo Harmonic:
```bash
sudo curl https://packages.osrfoundation.org/gazebo.gpg --output /usr/share/keyrings/pkgs-osrf-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/pkgs-osrf-archive-keyring.gpg] https://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/gazebo-stable.list > /dev/null
sudo apt-get update
sudo apt-get install gz-harmonic
```
## Using Ardupilot SITL with Gazebo¶

Firstly, check your Gazebo. This command should open a world with various shapes.
```bash
gz sim -v4 -r shapes.sdf
```
While Gazebo is commonly used with ROS / ROS2, the ArduPilot Gazebo plugin does not depend on ROS.
### Install the ArduPilot Gazebo Plugin¶
1.) Install additional dependencies
```bash
sudo apt update
sudo apt install libgz-sim8-dev rapidjson-dev
sudo apt install libopencv-dev libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev gstreamer1.0-plugins-bad gstreamer1.0-libav gstreamer1.0-gl
```
2.)Create a workspace folder and clone the repository
```bash
mkdir -p gz_ws/src && cd gz_ws/src
git clone https://github.com/ArduPilot/ardupilot_gazebo
```
3.)Build the plugin
Set GZ_VERSION environment variable according to installed gazebo version 
```bash
export GZ_VERSION=harmonic
cd ardupilot_gazebo
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=RelWithDebInfo
make -j4
```
### Configure the Gazebo environment¶
Do not forget to run these two commands:
```bash
echo 'export GZ_SIM_SYSTEM_PLUGIN_PATH=$HOME/gz_ws/src/ardupilot_gazebo/build:${GZ_SIM_SYSTEM_PLUGIN_PATH}' >> ~/.bashrc
echo 'export GZ_SIM_RESOURCE_PATH=$HOME/gz_ws/src/ardupilot_gazebo/models:$HOME/gz_ws/src/ardupilot_gazebo/worlds:${GZ_SIM_RESOURCE_PATH}' >> ~/.bashrc```
```

### Using Gazebo with ArduPilot¶
In the first terminal, start a Gazebo example world:
```bash
gz sim -v4 -r iris_runway.sdf
```
In the second terminal, run SITL:
```bash
sim_vehicle.py -v ArduCopter -f gazebo-iris --model JSON --map --console
```
In the SITL terminal, you can test your connection (please try these commands one by one):
```bash
GUIDED
GUIDED
GUIDED
arm throttle
takeoff 10
```

If, at the end of the SITL terminal, you can see “heartbeat received” and do not see any “JSON message not received” errors, you can use the ArduPilot plugin and Gazebo properly. After this step, we can edit our worlds and models and start using them.









