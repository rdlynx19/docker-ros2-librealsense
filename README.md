# Dockerfile for ROS2 Humble + Intel Realsense SDK (librealsense)

This template will get you set up using ROS2 with VSCode as your IDE. \
This template is adopted from [Allison Thackston](https://github.com/athackst) \
Instructions to use this template can be found on this [repository](https://github.com/athackst/vscode_ros2_workspace)

## Dockerfile
To install `librealsense` in your docker container, there are multiple options:

### Option 1: Using instructions from [distribution_linux.md](https://github.com/IntelRealSense/librealsense/blob/master/doc/distribution_linux.md)
```
RUN mkdir -p /etc/apt/keyrings && curl -sSf https://librealsense.intel.com/Debian/librealsense.pgp | sudo tee /etc/apt/keyrings/librealsense.pgp > /dev/null
RUN echo "deb [signed-by=/etc/apt/keyrings/librealsense.pgp] https://librealsense.intel.com/Debian/apt-repo `lsb_release -cs` main" | sudo tee /etc/apt/sources.list.d/librealsense.list

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
    build-essential \
    cmake \
    git-all \
    libusb-1.0-0 \
    udev \
    software-properties-common \
 && rm -rf /var/lib/apt/lists/*

# Installing realsense-viewer
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
    librealsense2-dkms \
    librealsense2-utils \ 
 && rm -rf /var/lib/apt/lists/
```

The above option failed for me, because the librealsense packages could not find the kernel version of my system, and were trying to build for a default kernel version. If you encounter a similar problem, you should try using this option.
### Option 2: Configuring linux-headers for the librealsense packages
```
RUN mkdir -p /etc/apt/keyrings && curl -sSf https://librealsense.intel.com/Debian/librealsense.pgp | sudo tee /etc/apt/keyrings/librealsense.pgp > /dev/null
RUN echo "deb [signed-by=/etc/apt/keyrings/librealsense.pgp] https://librealsense.intel.com/Debian/apt-repo `lsb_release -cs` main" | sudo tee /etc/apt/sources.list.d/librealsense.list
    
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
    build-essential \
    cmake \
    git-all \
    libusb-1.0-0 \
    udev \
    software-properties-common \
 && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y wget && \
    KERNEL_VERSION=$(uname -r) && \
    wget http://archive.ubuntu.com/ubuntu/pool/main/l/linux/linux-headers-${KERNEL_VERSION}_all.deb && \
    wget http://archive.ubuntu.com/ubuntu/pool/main/l/linux/linux-headers-${KERNEL_VERSION}-generic_${KERNEL_VERSION}_amd64.deb && \
    dpkg -i linux-headers-${KERNEL_VERSION}*.deb && \
    apt-get install -f -y

# Installing realsense-viewer
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
    librealsense2-dkms \
    librealsense2-utils \ 
 && rm -rf /var/lib/apt/lists/
```
If the above option fails, in case you have a non-generic kernel version, you can upgrade it by running
```
sudo apt dist-upgrade
```
However, if you don't want to modify the kernel version on your system, you can use this option
### Option 3: Using LibUVC-backend installation
```
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
    build-essential \
    cmake \
    git-all \
    libusb-1.0-0 \
    udev \
    software-properties-common \
 && rm -rf /var/lib/apt/lists/*

RUN wget https://github.com/IntelRealSense/librealsense/raw/master/scripts/libuvc_installation.sh
RUN chmod +x libuvc_installation.sh
RUN /bin/bash -c "./libuvc_installation.sh"

```
