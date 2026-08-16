# Running ROS on Windows using WSL 2 (Ubuntu)

This guide documents the setup process, installation steps, and troubleshooting tips for running **ROS (Robot Operating System)** on Windows using **Windows Subsystem for Linux (WSL 2)** with **Ubuntu**.

---

## 📌 Prerequisites
* Windows 10 (Build 19041+) or Windows 11.
* Virtualization enabled in BIOS/UEFI.
* WSL 2 updated to the latest version.

---

## 🚀 Setup & Installation Guide

### 1. Install WSL & Ubuntu
Open **PowerShell** or **Command Prompt** as Administrator and run:
```powershell
wsl --install -d Ubuntu

```

Restart your computer if prompted. After rebooting, complete the initial Ubuntu setup by creating a **username** and **password**.

---

### 2. Update System Packages

Inside your Ubuntu terminal, update the repository lists and upgrade existing packages:

```bash
sudo apt update && sudo apt upgrade -y

```

---

### 3. Install ROS

Add the ROS repositories, configure the keys, and install ROS packages:

```bash
# Add ROS repository and keys (Example for ROS 2 Humble / Ubuntu 22.04)
sudo apt install software-properties-common curl -y
sudo add-apt-repository universe
sudo curl -sSL [https://raw.githubusercontent.com/ros/rosdistro/master/ros.key](https://raw.githubusercontent.com/ros/rosdistro/master/ros.key) -o /usr/share/keyrings/ros-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] [http://packages.ros.org/ros2/ubuntu](http://packages.ros.org/ros2/ubuntu) $(. /etc/os-release && echo$UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null

# Install Desktop version (includes RViz, demos, and tutorials)
sudo apt update
sudo apt install ros-humble-desktop -y

```

*(Note: Replace `ros-humble-desktop` with your specific ROS version if using a different release).*

---

### 4. Configure Environment Sourcing

To avoid sourcing ROS manually every time a new terminal is opened, add the setup script to `~/.bashrc`:

```bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
source ~/.bashrc

```

---

### 5. Verification

Test the installation by running the demo publisher and subscriber:

* **Terminal 1 (Talker):**
```bash
ros2 run demo_nodes_cpp talker

```


* **Terminal 2 (Listener):**
```bash
ros2 run demo_nodes_py listener

```



---

## ⚠️ Common Issues & Troubleshooting

### 1. GUI Applications Not Launching (RViz / Gazebo)

* **Cause:** Display variable missing or outdated WSL version.
* **Fix (Windows 11):** Windows 11 includes **WSLg** by default. Update WSL in PowerShell:
```powershell
wsl --update

```


* **Fix (Windows 10):** Set the `DISPLAY` environment variable manually in `~/.bashrc`:
```bash
export DISPLAY=:0

```


And use an X-Server (such as VcXsrv or Xming) on Windows.

### 2. `ros2: command not found` Error

* **Cause:** The ROS environment script was not sourced in the current terminal session.
* **Fix:** Run `source ~/.bashrc` or verify that the correct path is appended to `~/.bashrc`.

### 3. Microcontroller / Hardware Not Detected (USB/COM Ports)

* **Cause:** WSL 2 does not attach USB devices by default.
* **Fix:** Use **usbipd-win** on Windows to bridge hardware devices (Arduino, sensors, LiDAR) into WSL:
```powershell
# List devices
usbipd list
# Bind and attach the device to WSL
usbipd bind --busid <BUSID>
usbipd attach --wsl --busid <BUSID>

```



### 4. GPU Acceleration & OpenGL Issues in Simulation

* **Cause:** Software rendering issues inside WSL.
* **Fix:** Enable hardware acceleration by passing:
```bash
export LIBGL_ALWAYS_SOFTWARE=1

```


Or ensure the latest GPU drivers with WSL-DirectX support are installed on the Windows host.

---

## 🛠️ Recommended Development Tools

* **VS Code WSL Extension:** Run `code .` directly inside Ubuntu to open the workspace in Visual Studio Code.
* **Colcon:** Build tool for ROS packages (`sudo apt install python3-colcon-common-extensions`).
