# Lab 0: Environment Setup

## What You'll Have at the End

Isaac Sim running, ROS 2 running, and the two talking to each
other confirmed. Every lab after this assumes this connection
exists.

---

## Part 1: Install Isaac Sim

Follow NVIDIA's official installation guide for your platform:
https://docs.isaacsim.omniverse.nvidia.com/5.1.0/installation

Check your machine against the requirements before installing:
https://docs.isaacsim.omniverse.nvidia.com/5.1.0/installation/requirements.html

The requirements page includes a link to the Isaac Sim
Compatibility Checker app. **Run it before installing anything**.
The two most common failure points on personal machines are
GPU VRAM and driver version. 

**On Union's ESports gaming PCs:** Isaac Sim is already installed on some of these machines.
They are also available for use and already surpass the recommended specs.
Skip to Part 2.

**On a personal Windows machine:** Install Isaac Sim natively
on Windows, then continue to Part 2 to set up WSL2 for ROS 2.

**No local GPU:** This option is recommended for users who don't have access to Use the NVIDIA Brev cloud deployment:
https://docs.isaacsim.omniverse.nvidia.com/5.1.0/installation/install_advanced_cloud_setup_brev.html

---

## Part 2: Set Up ROS 2

**On XLab PCs and Linux machines:** Follow the Mercer XLab
ROS 2 guide if you have not already: [link]

**On Windows:** ROS 2 does not run natively on Windows for
this use case. You need WSL2 (Windows Subsystem for Linux)
running Ubuntu 22.04, with ROS 2 Humble installed inside it.

Set up WSL2:
```powershell
# Run in PowerShell as Administrator
wsl --set-default-version 2
wsl --install -d Ubuntu-22.04
```

Restart when prompted, then open the Ubuntu 22.04 app and
follow the Mercer XLab ROS 2 guide inside it: [link]

---

## Part 3: Connect Isaac Sim to ROS 2

This is the part that is not obvious from either install guide.
Isaac Sim and ROS 2 are separate applications that communicate
over DDS, a pub-sub network protocol. You need to tell Isaac Sim
to load its ROS 2 bridge and confirm topics are flowing.

**A note on Python versions:** You may see warnings about Python
3.10 vs 3.11 mismatches between ROS 2 and Isaac Sim. Ignore them.
They run in completely separate environments and talk over the
network, not through Python. Do not try to change either version.

### Enable the Bridge

Open a fresh terminal. Do not source your ROS 2 installation
in this terminal. Run:

```bash
export isaac_sim_package_path=$HOME/isaacsim
export ROS_DISTRO=humble
export RMW_IMPLEMENTATION=rmw_fastrtps_cpp
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$isaac_sim_package_path/exts/isaacsim.ros2.bridge/humble/lib

~/isaacsim/isaac-sim.sh
```

> Set the LD_LIBRARY_PATH export only once per terminal.
> Running it a second time in the same terminal appends the
> path twice and causes conflicts. Open a new terminal if
> something seems wrong.

**On Windows:** Enable the bridge through the Extension Manager.
In Isaac Sim go to Window, then Extensions, search for ROS 2
Bridge, and enable it. Then set up port forwarding so Isaac Sim
on Windows can reach ROS 2 in WSL2:

```powershell
# Get your WSL2 IP first by running `hostname -I` inside WSL2
# Get your Windows IP from ipconfig /all

$Windows_IP = "YOUR_WINDOWS_IP"
$WSL2_IP = "YOUR_WSL2_IP"

netsh interface portproxy add v4tov4 listenport=7400 listenaddress=$Windows_IP connectport=7400 connectaddress=$WSL2_IP
netsh interface portproxy add v4tov4 listenport=7410 listenaddress=$Windows_IP connectport=7410 connectaddress=$WSL2_IP
netsh interface portproxy add v4tov4 listenport=9387 listenaddress=$Windows_IP connectport=9387 connectaddress=$WSL2_IP
```

> WSL2 gets a new IP on every reboot. Re-run these after
> restarting your machine.

### Build the Isaac Sim ROS Workspace

This workspace contains packages the labs depend on. Run inside
WSL2 or your Linux terminal:

```bash
sudo apt install python3-rosdep python3-colcon-common-extensions build-essential

git clone https://github.com/isaac-sim/IsaacSim-ros_workspaces.git
cd IsaacSim-ros_workspaces

source /opt/ros/humble/setup.bash
cd humble_ws
git submodule update --init --recursive
rosdep install -i --from-path src --rosdistro humble -y
colcon build
```

Add these to your `~/.bashrc` so you do not have to re-source
every session:

```bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
echo "source ~/IsaacSim-ros_workspaces/humble_ws/install/local_setup.bash" >> ~/.bashrc
```

---

## Part 4: Verify Everything Works

With Isaac Sim open and in Play mode, open a new terminal and run:

```bash
source /opt/ros/humble/setup.bash
ros2 topic list
```

You should see `/clock` in the output. If you do not, hit the
Play button in the Isaac Sim viewport first. Topics only publish
while the simulation is running.

Confirm the clock is live:

```bash
ros2 topic echo /clock
```

Timestamps incrementing means the bridge is working. You are
ready for Lab 1.

---

## Troubleshooting

**No topics after ros2 topic list**
Isaac Sim is not in Play mode. Hit Play in the viewport.

**ROS 2 bridge failed to load**
You have ROS 2 sourced in the same terminal as Isaac Sim. Open
a clean terminal, run only the export block, then launch.

**LD_LIBRARY_PATH conflict**
You ran the export block twice in the same terminal. Open a
new terminal.

**Port forwarding not working on Windows**
WSL2 IP changes on reboot. Get the current IPs again and rerun
the netsh commands.

**Isaac Sim black screen or slow to open**
Update your NVIDIA driver. First launch always takes 5-10
minutes while shaders compile — do not close it.

---

## Next

[Lab 1 - Keyboard Teleoperation](01-keyboard-teleop.md)
