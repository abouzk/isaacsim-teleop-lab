# Isaac Sim Teleop Lab
**ROS 2 Humble -- NVIDIA Isaac Sim 5.1 -- Mercer University XLab**

A hands-on educational module for learning robot teleoperation in simulation.
This lab progresses from basic ROS 2/Isaac Sim environment validation through gamepad
control, with an optional advanced track introducing haptic device integration.

Developed as an educational guide for students through RPI's [MercerXLab](https://mercerxlab.notion.site/1090f9dca44b80a8a26bf86d582848b1?v=1090f9dca44b8186bb1a000c0b461e14).
The advanced haptic phases of this work continue in
[isaacsim-surgical-teleop](https://github.com/abouzk/isaacsim-surgical-teleop).

---

## Prerequisites

- Ubuntu 22.04
- ROS 2 Humble
- NVIDIA Isaac Sim 5.1 ([Installation Guide](docs/00-environment-setup.md))
- GPU with $\ge$ 8GB VRAM (local), or [Brev cloud environment](docs/00-environment-setup.md#cloud)

---

## Module Structure

### Lab 0 - Environment Setup
Get Isaac Sim and ROS 2 talking to each other.
Install the ROS 2 bridge, verify topics are publishing,
and load your first robot into simulation.

-> [docs/00-environment-setup.md](docs/00-environment-setup.md)

---

### Lab 1 - Keyboard Teleoperation
Drive a differential robot in Isaac Sim using
`teleop_twist_keyboard`. Validates the full
ROS 2 to Isaac Sim communication pipeline before
introducing any hardware.

-> [docs/01-keyboard-teleop.md](docs/01-keyboard-teleop.md)

```bash
ros2 launch isaacsim_teleop_lab keyboard_teleop.launch.py
```

---

### Lab 2 - Gamepad Teleoperation
Map joystick axes to `/cmd_vel` using the `joy` package.
Introduces external USB hardware and axis remapping
without driver complexity.

-> [docs/02-gamepad-teleop.md](docs/02-gamepad-teleop.md)

```bash
ros2 launch isaacsim_teleop_lab gamepad_teleop.launch.py
```

---

### Lab 3 - Arm Fundamentals (Franka)
Move from a differential drive robot to a Franka arm.
Covers end-effector pose targets via the Lula Python
scripting API (`isaacsim.robot_motion.lula`);
the OmniGraph IK nodes are deprecated in Isaac Sim 5.1.

-> [docs/03-arm-fundamentals.md](docs/03-arm-fundamentals.md)

---

### Lab 4 - Haptic Device Integration *(Advanced / Optional)*
Integrate a Novint Falcon for 3DOF spatial teleoperation
via `forcedimension_ros2`. Introduces the
`haptic_teleop_core` ROS 2 package with a custom message
interface and workspace scaling service.

-> [docs/04-haptic-integration.md](docs/04-haptic-integration.md)

---

## Package Structure
