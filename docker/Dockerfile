FROM osrf/ros:humble-desktop

ARG DEBIAN_FRONTEND=noninteractive
ARG UID=1000
ARG DOCKERUSER=
ARG DOCKERUSERCOMMENT=

RUN useradd -d /${DOCKERUSER} -m \
            -u ${UID} -U \
            -s /usr/bin/bash \
            -c "${DOCKERUSERCOMMENT}" ${DOCKERUSER} && \
    echo "${DOCKERUSER} ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers && \
    usermod -a -G video ${DOCKERUSER} && \
    usermod -a -G dialout ${DOCKERUSER} && \
    usermod -a -G plugdev ${DOCKERUSER}

# HOTFIX: RViz black screen
RUN apt-get update && apt-get install -y software-properties-common && add-apt-repository -y ppa:kisak/kisak-mesa && \
    apt update -y && \
    apt upgrade -y

RUN apt-get update && apt-get install -y --no-install-recommends -o Dpkg::Options::="--force-overwrite" \
    apt-utils \
    ros-humble-rmw-cyclonedds-cpp \
    ros-humble-rviz2 \
    ros-humble-rqt-common-plugins \
    ros-humble-teleop-twist-keyboard \
    ros-humble-ros2-controllers \
    ros-humble-ros2-control \
    ros-humble-urdf-tutorial \
    ros-humble-topic-based-ros2-control \
    ros-humble-ament-cmake-nose \
    ros-humble-tf-transformations \
    ros-humble-foxglove-bridge \
    alsa \
    vim \
    python3-pip \
    gdb \
    wget \
    fuse \
    libfuse2 \
    libhidapi-dev

COPY ./docker/cyclonedds.xml /etc/cyclonedds.xml
ENV RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
ENV CYCLONEDDS_URI=/etc/cyclonedds.xml
ENV DOCKERUSER=${DOCKERUSER}

USER ${DOCKERUSER}

COPY ./docker/bashrc /tmp/bashrc
RUN cat /tmp/bashrc >> /${DOCKERUSER}/.bashrc

WORKDIR /${DOCKERUSER}/ros2_ws