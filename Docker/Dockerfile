FROM osrf/ros:melodic-desktop-full

ENV DEBIAN_FRONTEND noninteractive
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8
ENV ROS_DISTRO melodic

###################################### user #####################################

ENV SHELL=/bin/bash \
    USER=arg \
    UID=1000 \
    LANG=en_US.UTF-8 \
    LANGUAGE=en_US.UTF-8

ENV HOME=/home/${USER}

RUN adduser --disabled-password \
    --gecos "Default user" \
    --uid ${UID} \
    ${USER} 

RUN echo "root:root" | chpasswd
RUN echo "${USER}:111111" | chpasswd

###################################### basic tools #####################################

# Fix the time synchronization problem
RUN echo "Acquire::Check-Valid-Until \"false\";\nAcquire::Check-Date \"false\";" | cat > /etc/apt/apt.conf.d/10no--check-valid-until

Run curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -

Run apt-get -o Acquire::ForceIPv4=true update && apt-get -o Acquire::ForceIPv4=true install -yq --no-install-recommends \
    lsb-release \
    wget \
    sudo \
    software-properties-common \
    git \
    cmake \
    make \
    vim \
    build-essential \
    libblkid-dev \
    e2fslibs-dev \
    libboost-all-dev \
    libaudit-dev \
    net-tools \
    libturbojpeg-dev \
    iputils-ping \
    python-catkin-tools 

    # for proman
Run apt-get -o Acquire::ForceIPv4=true update && apt-get -o Acquire::ForceIPv4=true install -yq --no-install-recommends \
    python-gobject \
    python-gtk2

    # for python3 tf
Run apt-get -o Acquire::ForceIPv4=true update && apt-get -o Acquire::ForceIPv4=true install -yq --no-install-recommends \
    python3-setuptools \
    python3-pip \
    python3-empy

###################################### ROS ######################################

Run apt-get update && apt-get install -y \
    ros-$ROS_DISTRO-rosbridge-server \
    ros-$ROS_DISTRO-smach \
    ros-$ROS_DISTRO-smach-msgs \
    ros-$ROS_DISTRO-smach-ros \
    ros-$ROS_DISTRO-executive-smach \
    ros-$ROS_DISTRO-smach-viewer



###################################### PIP3 ######################################

Run pip3 install rospkg

##################################### setting #####################################

RUN chown -R ${USER}:${USER} ${HOME}/
RUN echo "root ALL=(ALL) ALL" > /etc/sudoers


# setup entrypoint
COPY ./ros_entrypoint.sh /
ENTRYPOINT ["/ros_entrypoint.sh"]

WORKDIR ${HOME}

######################################## realsense ########################################

RUN apt-get update && apt-get install -y \
    libsuitesparse-dev\
    software-properties-common \
    libusb-1.0-0-dev \
    libx11-dev \
    xorg-dev \
    libgl1-mesa-dev \
    libglu1-mesa-dev \
    && rm -rf /var/lib/apt/lists/*

# set the version of the realsense library
ENV LIBREALSENSE_VERSION 2.50.0
RUN cd ${HOME} && git clone https://github.com/IntelRealSense/librealsense.git \
    && cd librealsense \
    && git checkout v${LIBREALSENSE_VERSION} \
    && mkdir build \
    && cd build \
    && cmake .. \
    && make install
