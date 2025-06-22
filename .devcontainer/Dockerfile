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

RUN apt-get update && apt-get upgrade -y && \
    apt-get install -y python3-pip python-is-python3 ssh neovim \
    git nano cmake build-essential zenity libx11-dev libgl1-mesa-glx libegl1-mesa

#   install pytorch and CUDA
RUN pip install torch==2.5.1 torchvision==0.20.1 --index-url https://download.pytorch.org/whl/cu118 && \
    pip install --upgrade pip 

# install isaac lab
RUN git clone https://github.com/isaac-sim/IsaacLab.git
RUN ln -sf /isaac-sim IsaacLab/_isaac_sim
RUN cd IsaacLab && ./isaaclab.sh --install
RUN echo "alias python='/isaac-sim/python.sh'" >> /root/.bashrc && \
    echo "alias pip='/isaac-sim/python.sh -m pip'" >> /root/.bashrc
    
ENV SHELL /bin/bash

CMD ["/bin/bash"]