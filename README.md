
This repository contains the minimal IsaacLab image to work within a devcontainer in VScode.

*I find very useful to work inside a devcontainer and didn't find any example for Isaac Lab installation, fill free to use and improve it.* 

First tried to do the Dockerfile from the last IsaacLab image provided, but didn't manage to compile it. **This version starts from "nvcr.io/nvidia/isaac-sim:4.5.0 image and install isaac lab from repository.** 

# My Set up

Host OS:

* Ubuntu 24.04.2 LTS
* Docker version 28.2.2
* NVIDIA Container Runtime Hook version 1.17.8

    commit: f202b80a9b9d0db00d9b1d73c0128c8962c55f4d
* VScode version 1.101.1

Hardware: 
    
* CPU: AMD Ryzen™ 7 5800X × 16
* GPU: NVIDIA GeForce RTX™ 3060
* GPU drivers: NVIDIA driver metapackage from nvidia-driver-570

# Performace

For my setup I observed the following behaviours: 

1. Starting the container takes lot of time to download and build all the images (15Gb). Be patient

2. First time isaac-sim GUI openning takes lot of time (~10 mins). If no error arrise, don't worry just be patient. Don't worry about the pop up saying isaac-sim is not responding.

3. Once images are downloaded and built and isaac-sim is cached, start the environment takes just seconds. 

# Prerequisites
ref: https://isaac-sim.github.io/IsaacLab/main/source/deployment/docker.html

1. **install docker**

    ``` bash
    sudo apt-get update
    sudo apt-get install ca-certificates curl
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc

    # Add the repository to Apt sources:
    echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
    $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

    sudo apt-get update

    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    sudo docker run hello-world
    ```

    ``` bash
    sudo groupadd docker

    sudo usermod -aG docker $USER
    ```

2. **Install NVIDIA Container Toolkit for hardware acceleration**
    
    ref: https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html
    [No CUDA installation is needed in host]


    ``` bash
    curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg     && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list |     sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' |     sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list     &&     sudo apt-get update
    sudo apt-get install -y nvidia-container-toolkit
    sudo systemctl restart docker
    sudo nvidia-ctk runtime configure --runtime=docker
    sudo systemctl restart docker
    docker run --rm --runtime=nvidia --gpus all ubuntu nvidia-smi
    ```



# Run the dev container

**Warning:** The image of this DevContainer weights 32Gb, be careful when iterating frustrated builds, this can take your disk space very rapidly.  

1. If necessary, enable xhost for GUI fordwarding.

    At host, run:

    ```bash
    xhost +local:root
    ```

    **You have to do this every new session.**

2. Open the repo root folder in a Dev Container with VScode

3. Test isaac sim works

    ```bash
    ./../isaac-sim/isaac-sim.selector.sh --allow-root
    ```

4. Test isaac lab works

    ``` bash
    python /isaac-sim/IsaacLab/scripts/tutorials/00_sim/log_time.py
    ```


# Usage

**Once evething is installed, you can start playing with your Isaac Sim & Isaac Lab installation.** 

Isaac Sim 

For example you can start creating an asset with:


1. Create and install the asset

    ref: https://isaac-sim.github.io/IsaacLab/main/source/setup/quickstart.html#

    At workspace directory:

    ```bash
    ./isaac-sim/IsaacLab/isaaclab.sh --new source/extrange_robot/

    # install the new asset into the Isaac Lab framework
    python -m pip install -e source/extrange_robot/

    # check your asset is loaded
    python scripts/environments/list_envs.py
    ```

3. Start running a single robot without RL agent

    ```bash
    python scripts/zero_agent.py --task=Template-Extrange-Robot-Direct-v0 --rendering_mode=performance
    ```

4. Execute trainning and play

    ```bash
    python scripts/rsl_rl/train.py \
        --task Template-Extrange-Robot-Direct-v0 \
        --num_envs 2000 \
        --headless \
        --run_name extrange_run \
        --max_iterations 301 \
        --load_run 2025-06-15_00-56-20_extrange_run \
        --resume

    python scripts/rsl_rl/play.py \
        --task Template-Extrange-Robot-Direct-v0 \
        --checkpoint logs/rsl_rl/extrange_robot_ppo/2025-06-15_00-56-20_extrange_run/model_300.pt
    ```

# TODO

- [ ] Update Dockerfile to start from latest isaaclab image
- [ ] Make script o something to extract a clean image for deployment from the dev container.



__________________





# License

MIT License

Copyright (c) 2025  Miguel Ayuso Parrilla

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
    