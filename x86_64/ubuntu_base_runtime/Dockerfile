# SPDX-FileCopyrightText: Copyright (c) 2022-2023 NVIDIA CORPORATION & AFFILIATES. All rights reserved.
# SPDX-License-Identifier: MIT
#
# Permission is hereby granted, free of charge, to any person obtaining a
# copy of this software and associated documentation files (the "Software"),
# to deal in the Software without restriction, including without limitation
# the rights to use, copy, modify, merge, publish, distribute, sublicense,
# and/or sell copies of the Software, and to permit persons to whom the
# Software is furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL
# THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
# FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER
# DEALINGS IN THE SOFTWARE.


ARG CUDA_VERSION

FROM nvcr.io/nvidia/cuda:${CUDA_VERSION}-runtime-ubuntu20.04

ARG TENSORRT_VERSION
ARG CUDNN_VERSION


# Add open GL libraries
RUN apt-get update && \
        DEBIAN_FRONTEND=noninteractive  apt-get install -y --no-install-recommends \
        pkg-config \
        libglvnd-dev \
        libgl1-mesa-dev \
        libegl1-mesa-dev \
        libgles2-mesa-dev \
        libegl-mesa0 && \
        rm -rf /var/lib/apt/lists/* && \
        apt autoremove

RUN apt-get update && \
        DEBIAN_FRONTEND=noninteractive  apt-get install -y  --no-install-recommends \
        wget \
        libyaml-cpp-dev \
        gnutls-bin && \
        rm -rf /var/lib/apt/lists/* && \
        apt autoremove

#
# IP requirement for removal
#  Remove  gstreamer1.0-libav \

RUN apt-get update && \
	DEBIAN_FRONTEND=noninteractive 	apt-get install -y --no-install-recommends \
	gstreamer1.0-tools \
	gstreamer1.0-plugins-good \
	gstreamer1.0-plugins-bad \
	gstreamer1.0-plugins-ugly \
	gstreamer1.0-alsa \
	libssl1.1 \
	libgstreamer1.0-0 \
	libgstrtspserver-1.0-0 \
    libjansson4 \
	librabbitmq4 \
	libuuid1 \
	libcurl4 \
	libjson-glib-1.0-0 \
	gstreamer1.0-rtsp \
	rsyslog \
	git && \
	rm -rf /var/lib/apt/lists/* && \
	apt autoremove

ADD nv-tensorrt-local-repo-ubuntu2004-8.5.2-cuda-11.8_1.0-1_amd64.deb /tmp

# Add TRT repo
RUN mkdir -p /tmp/temp_trt && \
       cd /tmp/temp_trt && \
       mv /tmp/nv-tensorrt-local-repo-ubuntu2004-8.5.2-cuda-11.8_1.0-1_amd64.deb  /tmp/temp_trt/ && \
       ls -l /tmp/temp_trt/ && \
       dpkg -i /tmp/temp_trt/nv-tensorrt-local-repo-ubuntu2004-8.5.2-cuda-11.8_1.0-1_amd64.deb && \
       cp /var/nv-tensorrt-local-repo-ubuntu2004-8.5.2-cuda-11.8/nv-tensorrt-local-9A139056-keyring.gpg /usr/share/keyrings/ && \
       apt-get update && \
       DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
       libnvinfer8=8.5.2-1+cuda11.8 \
       libnvinfer-plugin8=8.5.2-1+cuda11.8 \
       libnvparsers8=8.5.2-1+cuda11.8 \
       libnvonnxparsers8=8.5.2-1+cuda11.8 \
       libnvinfer-bin=8.5.2-1+cuda11.8 \
       libcudnn8=8.7.0.84-1+cuda11.8 \
       libcudnn8-dev=8.7.0.84-1+cuda11.8 && \
       cd /tmp && \
       rm -rf temp_trt && \
       rm -rf /var/lib/apt/lists/*

RUN apt-get update && \
	DEBIAN_FRONTEND=noninteractive 	apt-get install -y --no-install-recommends \
	make \
    build-essential \
    libssl-dev \ 
    libopencv-videoio4.2 libopencv-imgcodecs4.2 libopencv-imgproc4.2 libopencv-core4.2 && \
	rm -rf /var/lib/apt/lists/* && \
	apt autoremove

WORKDIR /tmp
RUN git clone -b v1.1.0 https://github.com/redis/hiredis.git
WORKDIR hiredis
RUN make LIBRARY_PATH=lib USE_SSL=1 -j16 
RUN make LIBRARY_PATH=lib USE_SSL=1 install 
WORKDIR ..
RUN rm -rf hiredis

# Add TRT repo using a hosted file method.
# install TensorRT repo from a hosted file on a server
# add a URL to  server where  TRT is hosted
# Commented out the  Add version before uncommenting this version.
# RUN mkdir -p /tmp/temp_trt && \
#       cd /tmp/temp_trt && \
#       wget  https://<add url>/nv-tensorrt-local-repo-ubuntu2004-8.5.2-cuda-11.8_1.0-1_amd64.deb && \
#       dpkg -i /tmp/temp_trt/nv-tensorrt-local-repo-ubuntu2004-8.5.2-cuda-11.8_1.0-1_amd64.deb && \
#       cp /var/nv-tensorrt-local-repo-ubuntu2004-8.5.2-cuda-11.8/nv-tensorrt-local-9A139056-keyring.gpg /usr/share/keyrings/ && \
#       apt-get update && \
#       DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
#       libnvinfer8=8.5.2-1+cuda11.8 \
#       libnvinfer-plugin8=8.5.2-1+cuda11.8 \
#       libnvparsers8=8.5.2-1+cuda11.8 \
#       libnvonnxparsers8=8.5.2-1+cuda11.8 \
#       libnvinfer-bin=8.5.2-1+cuda11.8 \
#       libnvinfer-dev=8.5.2-1+cuda11.8 \
#       libnvinfer-plugin-dev=8.5.2-1+cuda11.8 \
#       libnvparsers-dev=8.5.2-1+cuda11.8 \
#       libnvonnxparsers-dev=8.5.2-1+cuda11.8 \
#       libnvinfer-samples=8.5.2-1+cuda11.8 \
#       python3-libnvinfer=8.5.2-1+cuda11.8 \
#       python3-libnvinfer-dev=8.5.2-1+cuda11.8 \
#       uff-converter-tf=8.5.2-1+cuda11.8 \
#       graphsurgeon-tf=8.5.2-1+cuda11.8 \
#       libcudnn8=8.7.0.84-1+cuda11.8 \
#       libcudnn8-dev=8.7.0.84-1+cuda11.8 && \
#       cd /tmp && \
#       rm -rf temp_trt && \
#       rm -rf /var/lib/apt/lists/*

# ENV NVIDIA_DRIVER_CAPABILITIES $NVIDIA_DRIVER_CAPABILITIES,video
ENV NVIDIA_DRIVER_CAPABILITIES $NVIDIA_DRIVER_CAPABILITIES,video,compute,graphics,utility

WORKDIR /opt/nvidia/deepstream/deepstream

# User additional install script (moved to the correct location with the license file)
ADD user_additional_install_runtime.sh /opt/

RUN ln -s /usr/lib/x86_64-linux-gnu/libnvcuvid.so.1 /usr/lib/x86_64-linux-gnu/libnvcuvid.so && \
    ln -s /usr/lib/x86_64-linux-gnu/libnvidia-encode.so.1 /usr/lib/x86_64-linux-gnu/libnvidia-encode.so 

RUN ldconfig

ENV LD_LIBRARY_PATH="/usr/local/lib:/usr/lib/x86_64-linux-gnu:/opt/nvidia/deepstream/deepstream/lib:${LD_LIBRARY_PATH}"
