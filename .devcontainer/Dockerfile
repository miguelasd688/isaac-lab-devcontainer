# MIT License
# 
# Copyright (c) 2025  Miguel Ayuso Parrilla
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
# 
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
# 
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

ARG DOCKER_IMAGE
ARG IMAGE_VERSION
ARG IMAGE_SUFFIX

FROM $DOCKER_IMAGE:$IMAGE_VERSION$IMAGE_SUFFIX

ENV TZ=ES \
    DEBIAN_FRONTEND=noninteractive
    
RUN apt-get update && apt-get upgrade -y && \
    apt-get install -y python3-pip python-is-python3 ssh neovim \
    git nano cmake build-essential zenity libx11-dev libgl1-mesa-glx libegl1-mesa curl

#   install pytorch and CUDA
RUN pip install torch==2.5.1 torchvision==0.20.1 --index-url https://download.pytorch.org/whl/cu118 && \
    pip install --upgrade pip 

# install isaac lab
RUN git clone https://github.com/isaac-sim/IsaacLab.git
RUN ln -sf /isaac-sim IsaacLab/_isaac_sim
RUN cd IsaacLab && ./isaaclab.sh --install
RUN echo "alias python='/isaac-sim/python.sh'" >> /root/.bashrc && \
    echo "alias pip='/isaac-sim/python.sh -m pip'" >> /root/.bashrc
    

RUN apt update && apt install locales && \
    locale-gen en_US en_US.UTF-8 && \
    update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8 && \
    export LANG=en_US.URF-8 && \
    locale && \
    apt install -y software-properties-common && \
    add-apt-repository universe && \
    apt update && \
    export ROS_APT_SOURCE_VERSION=$(curl -s https://api.github.com/repos/ros-infrastructure/ros-apt-source/releases/latest | grep -F "tag_name" | awk -F\" '{print $4}') && \
    curl -L -o /tmp/ros2-apt-source.deb "https://github.com/ros-infrastructure/ros-apt-source/releases/download/${ROS_APT_SOURCE_VERSION}/ros2-apt-source_${ROS_APT_SOURCE_VERSION}.$(. /etc/os-release && echo ${UBUNTU_CODENAME:-${VERSION_CODENAME}})_all.deb" && \
    dpkg -i /tmp/ros2-apt-source.deb && \
    apt update && apt install -y --allow-downgrades libbrotli1=1.0.9-2build6 ros-dev-tools && \
    apt update && apt upgrade -y 
RUN apt install -y ros-humble-desktop-full

ENV SHELL=/bin/bash

CMD ["/bin/bash"]