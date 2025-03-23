FROM ros:foxy-ros-base

# Set the working directory
WORKDIR /home

# Set environment variables for non-interactive installation
ENV DEBIAN_FRONTEND=noninteractive
ENV PATH="/opt/conda/bin:$PATH"

# Install system dependencies and colcon tools
RUN apt-get update && apt-get install -y \
    lsb-release \
    gnupg \
    build-essential \
    curl \
    wget \
    python3 \
    python3-pip \
    bzip2 \
    python3-colcon-common-extensions \
    libyaml-cpp-dev \
    && curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | apt-key add - \
    && echo "deb http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" \
    > /etc/apt/sources.list.d/ros2-latest.list \
    && apt-get update && apt-get install -y ros-foxy-desktop \
    python3-colcon-common-extensions \
    python3-rosdep \
    python3-vcstool \
    && rm -rf /var/lib/apt/lists/*

# Install ROS2 dependencies
RUN apt-get update && apt-get install -y \
    ros-foxy-navigation2 \
    ros-foxy-nav2-bringup \
    && rm -rf /var/lib/apt/lists/*

# Install Miniconda
RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda.sh \
    && bash miniconda.sh -b -p /opt/conda \
    && rm miniconda.sh \
    && conda init \
    && conda create -n trg-env python=3.8 -y

# Activate Conda for future RUNs
SHELL ["conda", "run", "-n", "trg-env", "/bin/bash", "-c"]

# Install pybind and Python dependencies
RUN pip install --upgrade pip empy setuptools wheel \
    scikit-build-core ninja cmake build numpy open3d

# Install git
RUN apt-get update && \
    apt-get install -y git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

# Inject conda activation into .bashrc
RUN echo 'source /opt/conda/etc/profile.d/conda.sh && conda activate trg-env' >> /root/.bashrc
ENTRYPOINT ["/entrypoint.sh"]
