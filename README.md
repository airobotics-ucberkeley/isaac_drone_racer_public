![Isaac Drone Racer](media/motion_trace1.jpg)

# Isaac Drone Racer

[![IsaacSim](https://img.shields.io/badge/IsaacSim-4.5.0-silver.svg)](https://docs.isaacsim.omniverse.nvidia.com/latest/index.html)
[![Python](https://img.shields.io/badge/python-3.10-blue.svg)](https://docs.python.org/3/whatsnew/3.10.html)
[![License](https://img.shields.io/badge/license-BSD--3-yellow.svg)](https://opensource.org/licenses/BSD-3-Clause)

**Isaac Drone Racer** is an open-source simulation framework for autonomous drone racing, developed on top of [IsaacLab](https://github.com/isaac-sim/IsaacLab). It is designed for training reinforcement learning policies in realistic racing environments, with a focus on accurate physics and modular design.

Maintained by [Ai Robotics @ Berkeley](https://github.com/airobotics-ucberkeley).

Autonomous drone racing is an active area of research. This project builds on insights from that body of work, combining them with massively parallel simulation to train racing policies within minutes — offering a fast and flexible platform for experimentation and benchmarking.

(You can watch the original related video here: [https://youtu.be/wLTYtpEUEEk](https://youtu.be/wLTYtpEUEEk))

## Features

Key highlights of the Isaac Drone Racer project:

1. **Accurate Physics Modeling** — Simulates rotor dynamics, aerodynamic drag, and power consumption to closely match real-world quadrotor behavior.
2. **Low-Level Flight Controller** — Built-in attitude and rate controllers.
3. **Manager-Based Design** — Modular architecture using IsaacLab's [manager based architecture](https://isaac-sim.github.io/IsaacLab/main/source/refs/reference_architecture/index.html#manager-based).
4. **Onboard Sensor Suite** — Includes simulated fisheye camera, IMU and collision detection.
5. **Track Generator** — Dynamically generate custom race tracks with multiple A2RL configurations.
6. **ROS 2 Bridge** — Stream camera, IMU, and state data to ROS 2 topics for integration with autonomy stacks.
7. **Multiple RL Frameworks** — Train with PPO (skrl), RSL-RL, or DreamerV3.
8. **Logger and Plotter** — Integrated tools for monitoring and visualizing flight behavior.

## Gym Environments

| ID | Track | Notes |
|----|-------|-------|
| `Isaac-Drone-Racer-v0` / `Play-v0` | 7-gate base track | Core training environment |
| `Isaac-Drone-Racer-A2RL-v1` / `Play-v1` | 10-gate A2RL | Absolute coordinates |
| `Isaac-Drone-Racer-A2RL-v2` / `Play-v2` | 5-gate origin-centered | Simpler for training |
| `Isaac-Drone-Racer-A2RL-cam-v3` / `Play-v3` | 5-gate camera variant | Camera observations |
| `Isaac-Drone-Racer-A2RL-loop-v4` / `Play-v4` | 8-gate loop | Looping track |
| `Isaac-Drone-Racer-A2RL-full-v5` / `Play-v5` | 11-gate full | Camera enabled in PLAY |

## Requirements

This framework has been tested on x64 Linux (Ubuntu 22.04).

### Prerequisites
- Workstation capable of running Isaac Sim (see [link](https://github.com/isaac-sim/IsaacSim?tab=readme-ov-file#prerequisites-and-environment-setup))
- Python 3.10
- [uv](https://docs.astral.sh/uv/) package manager
- [Git](https://git-scm.com/downloads) & [Git LFS](https://git-lfs.com)

## Setup

### Quick Start (recommended)

```bash
git clone git@github.com:airobotics-ucberkeley/isaac_drone_racer_public.git
cd isaac_drone_racer_public
./setup.sh
source env_isaaclab/bin/activate
```

`setup.sh` creates a self-contained venv with Isaac Sim 4.5.0, PyTorch 2.7 (CUDA 12.8), IsaacLab, and all project dependencies.

### Manual Installation

1. Follow the [Isaac Lab pip installation instructions](https://isaac-sim.github.io/IsaacLab/main/source/setup/installation/pip_installation.html):

```bash
git clone git@github.com:isaac-sim/IsaacLab.git
cd IsaacLab
git checkout v2.1.0
```

2. Clone this repo:
```bash
git clone git@github.com:airobotics-ucberkeley/isaac_drone_racer_public.git
```

3. Install in editable mode:
```bash
cd isaac_drone_racer_public
pip3 install -e .
```

## Usage

### Training a Policy (PPO via skrl)

```bash
python3 scripts/rl/train.py --task Isaac-Drone-Racer-v0 --headless --num_envs 4096
```

Train on an A2RL track:
```bash
python3 scripts/rl/train.py --task Isaac-Drone-Racer-A2RL-v1 --headless --num_envs 4096
```

> [!NOTE]
> You can pass additional CLI arguments supported by the [AppLauncher](https://isaac-sim.github.io/IsaacLab/main/source/tutorials/00_sim/launch_app.html). IsaacLab supports the [Hydra Configuration System](https://isaac-sim.github.io/IsaacLab/main/source/features/hydra.html) for adjusting task parameters from CLI.
> ```bash
> python3 scripts/rl/train.py --task Isaac-Drone-Racer-v0 --headless --num_envs 4096 env.actions.control_action.use_motor_model=False
> ```

### Training with RSL-RL

```bash
python3 scripts/rl/rsl_rl/train.py --task Isaac-Drone-Racer-v0 --headless --num_envs 4096
```

### Playing Back a Trained Policy

```bash
python3 scripts/rl/play.py --task Isaac-Drone-Racer-Play-v0 --num_envs 1
```

### ROS 2 Bridge with Camera Streaming

Stream camera and state data from Isaac Sim to ROS 2:
```bash
python3 scripts/rl/play_ros_bridge.py --task Isaac-Drone-Racer-A2RL-full-Play-v5 --num_envs 1
```

Published topics:
- `/camera/image_raw` — RGB camera stream
- `/drone/state/pose`, `/drone/state/twist` — drone state
- `/drone/imu/data` — IMU data
- `/drone/state` — custom `autonomy_msgs/DroneState`

## Next Steps

- [ ] **Data-driven aerodynamic model pipeline** — integrate tools for data-driven system identification, calibration and include the learned aerodynamic forces into the simulation environment.
- [ ] **Power consumption model** — incorporate a detailed power model that accounts for battery discharge based on current draw.
- [ ] **Policy learning using onboard sensors** — explore and implement methods to transition away from full-state observations by instead using only onboard sensor data (e.g camera + IMU).

## Troubleshooting

- Activate the venv before running any scripts:
```bash
source env_isaaclab/bin/activate
```
- First launch of Isaac Sim may take up to 10 minutes to load. This is normal.

## License

This project is licensed under the BSD 3-Clause License — see the [LICENSE](LICENSE) file for details.

## Contributors

- [@redspry](https://github.com/redspry)
- [@timothyypark](https://github.com/timothyypark)
- [@biojeffliu](https://github.com/biojeffliu)
- [@GulatiRohan1215](https://github.com/GulatiRohan1215)
- [@FieldDiTian](https://github.com/FieldDiTian)

Project Link: [https://github.com/airobotics-ucberkeley/isaac_drone_racer_public](https://github.com/airobotics-ucberkeley/isaac_drone_racer_public)

If you encounter any difficulties, feel free to reach out through the Issues section. If you find any bugs or have improvements to suggest, don't hesitate to make a pull request.
