# Use ROS indigo Perception as the base image

FROM ros:indigo-perception

# Set environment variables

ENV CATKIN_WS=/root/catkin_ws

ENV USERNAME=rosuser

# Install dependencies for building ROS packages and USB passthrough

RUN apt-get update && apt-get install -y \
    git \
    python-catkin-tools \
    ros-indigo-usb-cam \
    ros-indigo-dynamixel-controllers \
    ros-indigo-urdf \
    ros-indigo-xacro \
    ros-indigo-dynamixel-msgs \
    udev \
    sudo

# Create a non-root user and add them to the dialout group

RUN useradd -m $USERNAME && echo "$USERNAME:$USERNAME" | chpasswd && usermod --shell /bin/bash $USERNAME && \
    usermod -aG sudo,dialout $USERNAME

# Install ROS package dependencies with rosdep

RUN apt-get install -y python-rosdep && \
    if [ ! -f /etc/ros/rosdep/sources.list.d/20-default.list ]; then \
    rosdep init; \
    fi && \
    rosdep update

# Create the catkin workspace and clone the reflex-ros-pkg repository

RUN mkdir -p $CATKIN_WS/src && \
    cd $CATKIN_WS/src && \
    git clone https://github.com/why-does-ie-still-exist/reflex-ros-pkg.git --branch broken-arclab-hand

# Resolve dependencies using rosdep

RUN cd $CATKIN_WS && rosdep install --from-paths src --ignore-src -r -y

# Build the workspace

RUN /bin/bash -c "source /opt/ros/indigo/setup.bash && \
    cd $CATKIN_WS && catkin_make"

# Set the default command to source the workspace and run as the created user

CMD ["/bin/bash", "-c", "source /opt/ros/indigo/setup.bash && source $CATKIN_WS/devel/setup.bash && bash"]

# Set up udev rules for USB passthrough (specific to the FTDI device)

RUN echo 'SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6015", MODE="0666"' > /etc/udev/rules.d/99-usb.rules

# Add this section right before switching to the non-root user

# Create a startup script to reload udev rules

RUN printf '#!/bin/bash\nudevadm control --reload-rules && udevadm trigger\nexec "$@"\n' > /root/start.sh && \
    chmod +x /root/start.sh

# Set the default entrypoint to use the startup script

ENTRYPOINT ["/root/start.sh"]
