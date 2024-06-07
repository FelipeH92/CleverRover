#############################################
# Created from template athackst.humble.Dockerfile
#############################################

FROM ubuntu:22.04 as cleverrover-humble

# Avoid warnings by switching to noninteractive
ENV DEBIAN_FRONTEND=noninteractive

# Install language
RUN apt-get update && apt-get install -y \
  locales \
  && locale-gen en_US.UTF-8 \
  && update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8 \
  && rm -rf /var/lib/apt/lists/*
ENV LANG en_US.UTF-8

# Install timezone
RUN ln -fs /usr/share/zoneinfo/UTC /etc/localtime \
  && export DEBIAN_FRONTEND=noninteractive \
  && apt-get update \
  && apt-get install -y tzdata \
  && dpkg-reconfigure --frontend noninteractive tzdata \
  && rm -rf /var/lib/apt/lists/*

# Install common programs
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    gnupg2 \
    lsb-release \
    software-properties-common \
    sudo \
    wget \
    && rm -rf /var/lib/apt/lists/*

ENV ROS_DISTRO=humble
ENV AMENT_PREFIX_PATH=/opt/ros/humble
ENV COLCON_PREFIX_PATH=/opt/ros/humble
ENV LD_LIBRARY_PATH=/opt/ros/humble/lib
ENV PATH=/opt/ros/humble/bin:$PATH
ENV PYTHONPATH=/opt/ros/humble/lib/python3.10/site-packages
ENV ROS_PYTHON_VERSION=3
ENV ROS_VERSION=2

# Install ROS2
RUN sudo add-apt-repository universe \
  && curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null \
  && apt-get update && apt-get install -y --no-install-recommends \
    python3-argcomplete \
    ros-${ROS_DISTRO}-ros-base \
    ros-${ROS_DISTRO}-desktop \
  && rm -rf /var/lib/apt/lists/*

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
    ros-${ROS_DISTRO}-joint-state-publisher-gui \
    ros-${ROS_DISTRO}-ros-gz \
    ros-${ROS_DISTRO}-ros-testing \
    ros-${ROS_DISTRO}-sophus \
    ros-${ROS_DISTRO}-gazebo-ros \
    ros-${ROS_DISTRO}-gazebo-plugins \
    ament-cmake \
    ament-lint \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Install common programs
RUN apt-get update && apt-get install -y \
    bash-completion \
    build-essential \
    cmake \
    curl \
    evince \
    gdb \
    git \
    gnupg2 \
    htop \
    iftop \
    iproute2 \
    iputils-ping \
    lsb-release \
    nano \
    net-tools \
    openssh-client \
    psmisc \
    python3-argcomplete \
    python3-pip \
    ros-dev-tools \
    ros-$ROS_DISTRO-ament-* \
    software-properties-common \
    sqlite3 \
    sudo \
    traceroute \
    terminator \
    vim \
    wireless-tools \
    wget \
    xterm \
    && rm -rf /var/lib/apt/lists/*

RUN rosdep init || echo "rosdep already initialized"

ARG USERNAME=cleverrover-humble
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Create a non-root user
RUN groupadd --gid $USER_GID $USERNAME \
  && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
  # Add sudo support for the non-root user
  && apt-get update \
  && apt-get install -y sudo \
  && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME\
  && chmod 0440 /etc/sudoers.d/$USERNAME \
  && rm -rf /var/lib/apt/lists/*

# Set up autocompletion for user
RUN apt-get update && apt-get install -y git-core bash-completion \
  && echo "if [ -f /opt/ros/${ROS_DISTRO}/setup.bash ]; then source /opt/ros/${ROS_DISTRO}/setup.bash; fi" >> /home/$USERNAME/.bashrc \
  && echo "if [ -f /usr/share/colcon_argcomplete/hook/colcon-argcomplete.bash ]; then source /usr/share/colcon_argcomplete/hook/colcon-argcomplete.bash; fi" >> /home/$USERNAME/.bashrc \
  && rm -rf /var/lib/apt/lists/* 

# Install gazebo ignition fortress
RUN wget https://packages.osrfoundation.org/gazebo.gpg -O /usr/share/keyrings/pkgs-osrf-archive-keyring.gpg \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/pkgs-osrf-archive-keyring.gpg] http://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/gazebo-stable.list > /dev/null \
  && apt-get update && apt-get install -y -q \
    ignition-fortress \
    libignition-plugin-dev \
    libignition-cmake2-dev \
    libignition-gazebo6-dev \
  && rm -rf /var/lib/apt/lists/*

################
# Expose the nvidia driver to allow opengl 
# Dependencies for glvnd and X11.
################
RUN apt-get update \
 && apt-get install -y -qq --no-install-recommends \
  libegl1 \
  libglvnd0 \
  libglx0 \
  libgl1 \
  libxext6 \
  libx11-6

# Env vars for the nvidia-container-runtime.
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES graphics,utility,compute
ENV QT_X11_NO_MITSHM 1

################
# ClerverRover packages
################

RUN apt-get update && apt-get install -y \
    ros-${ROS_DISTRO}-rtabmap-ros \
    ros-${ROS_DISTRO}-turtlebot4-simulator \
    ros-${ROS_DISTRO}-moveit \
    ros-${ROS_DISTRO}-moveit-setup-assistant \
    ros-${ROS_DISTRO}-navigation2 \
    ros-${ROS_DISTRO}-nav2-bringup \
    && rm -rf /var/lib/apt/lists/*

# Install PlotJuggler
RUN apt-get update && apt-get install -y \
ros-humble-plotjuggler-ros \
&& rm -rf /var/lib/apt/lists/*

# Install sqlittle
RUN pip3 install db-sqlite3

# Install any additional Python dependencies
RUN pip3 install numpy pandas

# Standard init procedure

ENV DEBIAN_FRONTEND=

RUN echo "source /opt/ros/$ROS_DISTRO/setup.bash" >> /root/.bashrc

RUN usermod -aG video $USERNAME

RUN apt-get update && apt-get install -y --no-install-recommends apt-utils && \
    apt-get -y autoremove && \
    apt-get -y clean

# Accept the WORKSPACE build argument
ARG WORKSPACE

# Set the working directory to the WORKSPACE argument value
WORKDIR $WORKSPACE

# Source ROS on container startup
RUN echo "source /opt/ros/humble/setup.bash" >> /root/.bashrc

COPY entrypoint.sh /setup/entrypoint.sh

ENTRYPOINT ["/bin/bash", "/setup/entrypoint.sh"]

CMD ["bash"]
