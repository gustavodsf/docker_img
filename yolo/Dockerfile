#Download base image ubuntu 20.04
FROM ubuntu:20.04

# LABEL about the custom image
LABEL maintainer="gustavo.figueiredo@g.glob"
LABEL version="0.1"
LABEL description="Custom docker to run yolo-v4"

# Disable Prompt During Packages Installation
ARG DEBIAN_FRONTEND=noninteractive

# Update Ubuntu Software repository
RUN apt update

# Install Yolo Required Libraries
RUN apt install make build-essential git -y

# Imstall GCC
RUN apt install g++ -y

# Install Opencv
RUN apt install libopencv-dev python3-opencv -y 

# Install Open MP
RUN apt install libomp-dev    -y

RUN mkdir -p /home/my_yolo_test/ \
    cd /home/my_yolo_test/ \
    git clone https://github.com/AlexeyAB/darknet

COPY Makefile /home/my_yolo_test/darknet/

RUN cd /home/my_yolo_test/darknet \
    make

