FROM ghcr.io/jmarrero/fedora-silverblue:38

ARG ROS_VERSION=humble

ENV LANG=en_US.UTF-8
ENV RPM_ARCH=x86_64
ENV ROS_PYTHON_VERSION=3

RUN rpm-ostree install dnf

RUN sudo dnf install -y \
  cmake \
  cppcheck \
  eigen3-devel \
  gcc-c++ \
  liblsan \
  libXaw-devel \
  libyaml-devel \
  make \
  opencv-devel \
  patch \
  python3-colcon-common-extensions \
  python3-coverage \
  python3-devel \
  python3-empy \
  python3-nose \
  python3-pip \
  python3-pydocstyle \
  python3-pyparsing \
  python3-pytest \
  python3-pytest-cov \
  python3-pytest-mock \
  python3-pytest-runner \
  python3-rosdep \
  python3-setuptools \
  python3-vcstool \
  poco-devel \
  poco-foundation \
  python3-flake8 \
  python3-flake8-import-order \
  redhat-rpm-config \
  uncrustify \
  wget

RUN python3 -m pip install -U --user \
  flake8-blind-except==0.1.1 \
  flake8-builtins \
  flake8-class-newline \
  flake8-comprehensions \
  flake8-deprecated \
  flake8-docstrings \
  flake8-import-order \
  flake8-quotes \
  mypy==0.931

WORKDIR /etc
RUN mkdir -p /etc/ros2/src
WORKDIR /etc/ros2
RUN vcs import --input https://raw.githubusercontent.com/ros2/ros2/${ROS_VERSION}/ros2.repos src

RUN sudo dnf update -y

RUN sudo rosdep init
RUN rosdep update
RUN rosdep install --from-paths src --ignore-src -y --skip-keys "asio cyclonedds fastcdr fastrtps ignition-cmake2 ignition-math6 python3-babeltrace python3-mypy rti-connext-dds-6.0.1 urdfdom_headers"

# TODO: temporary workaround, remove once those packages can be built
RUN touch ./src/ros-visualization/rqt/rqt_gui_cpp/CATKIN_IGNORE && \
touch ./src/ros-visualization/qt_gui_core/qt_gui_app/CATKIN_IGNORE && \
touch ./src/ros-visualization/qt_gui_core/qt_gui_cpp/CATKIN_IGNORE

WORKDIR /etc/ros2/ros2
RUN sed -i '1s/^/#include <stdint.h> /' /etc/ros2/src/ros2/rcpputils/include/rcpputils/filesystem_helper.hpp
RUN sed -i '1s/^/#include <stdint.h> /' /etc/ros2/src/ros-tooling/libstatistics_collector/include/libstatistics_collector/moving_average_statistics/types.hpp
RUN sed -i '1s/^/#include <stdexcept> /' /etc/ros2/src/ros2/rclcpp/rclcpp/include/rclcpp/context.hpp
RUN sed -i '1s/^/#include <stdexcept> /' /etc/ros2/src/ros2/rclcpp/rclcpp/src/rclcpp/logging_mutex.cpp
RUN sed -i '1s/^/#include <stdint.h> /' /etc/ros2/src/ros2/rosbag2/rosbag2_compression/include/rosbag2_compression/compression_options.hpp
# RUN rm -R /ros2/src/ros2/

RUN test -f /usr/bin/ld || ln -s /usr/bin/ld.bfd /usr/bin/ld
RUN colcon build --symlink-install --cmake-args -DTHIRDPARTY_Asio=ON --no-warn-unused-cli

RUN ls
# RUN . /etc/ros2/ros2/install/local_setup.bash
# RUN cat /etc/ros2/ros2/install/local_setup.bash
RUN ls
RUN rm -rf /var/roothome
RUN rm -rf /var/lib/unbound
RUN rm -rf /var/lib/dnf
RUN rm -rf /var/lib/texmf
RUN rm -rf /var/log
RUN chmod +x /etc/ros2/ros2/install/*
RUN ostree container commit
WORKDIR /etc/ros2/src/ros2
RUN ls


# RUN colcon build --packages-select ament_cppcheck
# RUN colcon build --packages-select ament_lint
# RUN colcon build --packages-select ament_package
# RUN colcon build --packages-select fastcdr
# RUN colcon build --packages-select google_benchmark_vendor
# RUN colcon build --packages-select gtest_vendor
# RUN colcon build --packages-select iceoryx_hoofs
# RUN colcon build --packages-select osrf_testing_tools_cpp
# RUN colcon build --packages-select ament_cmake_core
# RUN colcon build --packages-select ament_flake8
# RUN colcon build --packages-select gmock_vendor
# RUN colcon build --packages-select iceoryx_posh
# RUN colcon build --packages-select ament_cmake_export_definitions
# RUN colcon build --packages-select ament_cmake_export_include_directories
# RUN colcon build --packages-select ament_cmake_export_libraries
# RUN colcon build --packages-select ament_cmake_export_link_flags
# RUN colcon build --packages-select ament_cmake_include_directories
# RUN colcon build --packages-select ament_cmake_libraries
# RUN colcon build --packages-select ament_cmake_python
# RUN colcon build --packages-select ament_cmake_version
# RUN colcon build --packages-select ament_pep257
# RUN colcon build --packages-select iceoryx_binding_c
# RUN colcon build --packages-select ament_cmake_export_dependencies
# RUN colcon build --packages-select ament_cmake_export_interfaces
# RUN colcon build --packages-select ament_cmake_export_targets
# RUN colcon build --packages-select ament_cmake_target_dependencies
# RUN colcon build --packages-select ament_cmake_test
# RUN colcon build --packages-select ament_copyright
# RUN colcon build --packages-select cyclonedds
# RUN colcon build --packages-select ament_cmake_google_benchmark
# RUN colcon build --packages-select ament_cmake_gtest
# RUN colcon build --packages-select ament_cmake_pytest
# RUN colcon build --packages-select ament_cpplint
# RUN colcon build --packages-select ament_index_python
# RUN colcon build --packages-select ament_lint_auto
# RUN colcon build --packages-select ament_lint_cmake
# RUN colcon build --packages-select ament_xmllint
# RUN colcon build --packages-select domain_coordinator
# RUN colcon build --packages-select ament_cmake_gen_version_h
# RUN colcon build --packages-select ament_cmake_gmock
# RUN colcon build --packages-select ament_cmake_lint_cmake
# RUN colcon build --packages-select rosidl_cli
# RUN colcon build --packages-select rpyutils
# RUN colcon build --packages-select ament_cmake
# RUN colcon build --packages-select ament_cmake_copyright
# RUN colcon build --packages-select ament_cmake_cppcheck
# RUN colcon build --packages-select ament_cmake_cpplint
# RUN colcon build --packages-select ament_cmake_flake8
# RUN colcon build --packages-select ament_cmake_pep257
# RUN colcon build --packages-select ament_cmake_xmllint
# RUN colcon build --packages-select uncrustify_vendor
# RUN colcon build --packages-select ament_pycodestyle
# RUN colcon build --packages-select ament_uncrustify
# RUN colcon build --packages-select foonathan_memory_vendor
# RUN colcon build --packages-select ament_cmake_uncrustify

# RUN dnf install asio-devel -y
# RUN colcon build --packages-select fastrtps
# RUN colcon build --packages-select ament_lint_common
# RUN colcon build --packages-select ament_cmake_ros
# RUN colcon build --packages-select ament_index_cpp
# RUN colcon build --packages-select fastrtps_cmake_module
# RUN colcon build --packages-select mimick_vendor
# RUN colcon build --packages-select performance_test_fixture
# RUN colcon build --packages-select python_cmake_module
# RUN colcon build --packages-select rmw_implementation_cmake
# RUN colcon build --packages-select rosidl_adapter
# RUN colcon build --packages-select rosidl_typesupport_interface
# RUN colcon build --packages-select rti_connext_dds_cmake_module
# RUN colcon build --packages-select spdlog_vendor
# RUN colcon build --packages-select rosidl_parser
# RUN colcon build --packages-select tracetools

# RUN colcon build --packages-select osrf_pycommon
# RUN colcon build --packages-select launch
# RUN colcon build --packages-select launch_xml
# RUN colcon build --packages-select launch_yaml
# RUN colcon build --packages-select launch_testing
# RUN colcon build --packages-select launch_testing_ament_cmake

# RUN colcon build --packages-select rcutils
# RUN colcon build --packages-select rosidl_cmake
# RUN colcon build --packages-select rcpputils
# RUN colcon build --packages-select rosidl_runtime_c
# RUN colcon build --packages-select libyaml_vendor
# RUN colcon build --packages-select rcl_logging_interface
# RUN colcon build --packages-select rmw
# RUN colcon build --packages-select test_interface_files

# RUN colcon build --packages-select rosidl_generator_c
# RUN colcon build --packages-select rosidl_runtime_cpp
# RUN colcon build --packages-select rosidl_typesupport_introspection_c
# RUN colcon build --packages-select rcl_logging_noop
# RUN colcon build --packages-select rcl_logging_spdlog
# RUN colcon build --packages-select rcl_yaml_param_parser
# RUN colcon build --packages-select rosidl_generator_cpp
# RUN colcon build --packages-select rosidl_typesupport_introspection_cpp
# RUN colcon build --packages-select rosidl_typesupport_fastrtps_cpp
# RUN colcon build --packages-select rosidl_typesupport_fastrtps_c
# RUN colcon build --packages-select rosidl_typesupport_c
# RUN colcon build --packages-select rosidl_generator_py
# RUN colcon build --packages-select rosidl_typesupport_cpp
# RUN colcon build --packages-select rosidl_default_generators
# RUN colcon build --packages-select rosidl_default_runtime
# RUN colcon build --packages-select builtin_interfaces
# RUN colcon build --packages-select rmw_dds_common
# RUN colcon build --packages-select unique_identifier_msgs
# RUN colcon build --packages-select action_msgs
# RUN colcon build --packages-select rcl_interfaces
# RUN colcon build --packages-select rmw_connextdds_common
# RUN colcon build --packages-select rmw_cyclonedds_cpp
# RUN colcon build --packages-select rmw_fastrtps_shared_cpp
# RUN colcon build --packages-select rosgraph_msgs
# RUN colcon build --packages-select statistics_msgs
# RUN colcon build --packages-select std_msgs
# RUN colcon build --packages-select rmw_connextdds
# RUN colcon build --packages-select rmw_connextddsmicro
# RUN colcon build --packages-select test_msgs
# RUN colcon build --packages-select rmw_fastrtps_cpp
# RUN colcon build --packages-select rmw_fastrtps_dynamic_cpp
# RUN colcon build --packages-select rmw_implementation
# RUN colcon build --packages-select rcl
# RUN colcon build --packages-select libstatistics_collector

# # RUN rm -R /ros2/src/ros2/rclcpp
# # RUN git clone -b humble https://github.com/ros2/rclcpp.git /ros2/src/ros2/rclcpp

# RUN sed -i '1s/^/#include <stdexcept> /' /ros2/src/ros2/rclcpp/rclcpp/include/rclcpp/context.hpp
# RUN sed -i '1s/^/#include <stdexcept> /' /ros2/src/ros2/rclcpp/rclcpp/src/rclcpp/logging_mutex.cpp
# RUN colcon build --packages-select rclcpp
