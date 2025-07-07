# 🚁 Installing PX4 + Gazebo Environment on Fedora 39 (XFCE)

## 🎯 Goal

Set up a functional environment from scratch to simulate drones using **PX4 Autopilot** and 
**Gazebo** on **Fedora 39 XFCE**, documenting each validated step to ensure correct execution.

---

## 📦 Step 1: Install Dependencies

Install all required system and Python libraries needed to compile and run the environment.

```bash
sudo dnf install git cmake ninja-build gcc-c++ python3-pip \
    python3-jinja2 python3-empy python3-toml python3-numpy \
    python3-yaml python3-pyserial python3-pandas \
    python3-pygments python3-setuptools python3-opencv \
    python3-matplotlib python3-scipy \
    gazebo gazebo-devel```

---

## 📦 Step 2: Install Specific Python Libraries

Specific versions of Python libraries are needed to avoid compatibility issues.

```bash
pip install numpy==1.26.4
pip install pyulog
```

---

## 📦 Step 3: Clone and Build PX4 Autopilot

Clone the official repository

```bash
git clone https://github.com/PX4/PX4-Autopilot.git --recursive
cd ~/PX4-Autopilot
```


## 🚀 Run the Environment (3 Terminals)


### 🖥 Terminal 1: Enter the official PX4 Docker development container

This isolates the build environment and ensures compatibility.

```bash
docker run -it \
  -v $(pwd):/src/PX4-Autopilot:z \
  -w /src/PX4-Autopilot \
  px4io/px4-dev:v1.16.0-rc1-258-g0369abd556 \
  /bin/bash

make px4_sitl_default
```

### 🖥 Terminal 2: Prepare and launch PX4

```bash
cd ~/PX4-Autopilot/build/px4_sitl_default
sudo chown -R personal:personal ~/PX4-Autopilot/build/px4_sitl_default
mkdir -p ~/PX4-Autopilot/build/px4_sitl_default/log
bin/px4 -s etc/init.d-posix/rcS
```

### 🖥 Terminal 3: Launch the Gazebo simulator

```bash
gazebo ~/PX4-Autopilot/Tools/sitl_gazebo/worlds/iris.world
```

---

## 🔄 For Future Runs

### 🖥 Terminal 1: Enter Docker and compile

```bash
cd ~/PX4-Autopilot

docker run -it \
  -v $(pwd):/src/PX4-Autopilot:z \
  -w /src/PX4-Autopilot \
  px4io/px4-dev:v1.16.0-rc1-258-g0369abd556 \
  /bin/bash

make px4_sitl_default
```

### 🖥 Terminal 2: Launch PX4

```bash
cd ~/PX4-Autopilot/build/px4_sitl_default
bin/px4 -s etc/init.d-posix/rcS
```

### 🖥 Terminal 3: Launch Gazebo

```bash
gazebo ~/PX4-Autopilot/Tools/sitl_gazebo/worlds/iris.world
```

---

## ✅ Expected Result

Once all steps are completed, the environment should be up and running, ready to simulate flights with 
the **Iris** drone in **Gazebo**, controlled using the PX4 firmware in **SITL** (Software In The Loop) mode.

---

## 📝 Additional Notes

- It is recommended to use three separate terminals to keep Docker, PX4, and Gazebo processes isolated.
- The `px4io/px4-dev` container helps manage dependencies and simplifies the build process.
- Ensure you have the correct permissions for the `~/PX4-Autopilot` folder, especially after running commands inside Docker.
