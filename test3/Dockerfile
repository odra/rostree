# Change a SELinux boolean. A conflict between overlayfs semantics
# and libselinux existed before fedora 37. This examples shows that
# selinux booleans can now be changed correctly as part of the build.
# https://github.com/SELinuxProject/selinux/pull/342

ARG BUILDER_VERSION=38

FROM registry.fedoraproject.org/fedora:38

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

RUN mkdir -p /ros2_humble/src
WORKDIR /ros2_humble
RUN vcs import --input https://raw.githubusercontent.com/ros2/ros2/humble/ros2.repos src
# RUN sed -i '1s/^/#include <stdint> /' /ros2_humble/src/ros2/rcpputils/include/rcppmath/rolling_mean_accumulator.hpp

RUN sudo dnf update -y

RUN sudo rosdep init
RUN rosdep update
RUN rosdep install --from-paths src --ignore-src -y --skip-keys "asio cyclonedds fastcdr fastrtps ignition-cmake2 ignition-math6 python3-babeltrace python3-mypy rti-connext-dds-6.0.1 urdfdom_headers"

# TODO: temporary workaround, remove once those packages can be built
RUN touch ./src/ros-visualization/rqt/rqt_gui_cpp/CATKIN_IGNORE && \
touch ./src/ros-visualization/qt_gui_core/qt_gui_app/CATKIN_IGNORE && \
touch ./src/ros-visualization/qt_gui_core/qt_gui_cpp/CATKIN_IGNORE

WORKDIR /ros2_humble/
RUN sed -i '1s/^/#include <stdint.h> /' /ros2_humble/src/ros2/rcpputils/include/rcpputils/filesystem_helper.hpp
RUN colcon build --symlink-install --cmake-args -DTHIRDPARTY_Asio=ON --no-warn-unused-cli

# RUN colcon build --packages-select ament_cppcheck
# RUN colcon build --packages-select ament_lint
# RUN colcon build --packages-select ament_package
# RUN colcon build --packages-select gtest_vendor
# RUN colcon build --packages-select ament_cmake_core
# RUN colcon build --packages-select ament_flake8
# RUN colcon build --packages-select gmock_vendor
# RUN colcon build --packages-select ament_cmake_export_definitions
# RUN colcon build --packages-select ament_cmake_export_include_directories
# RUN colcon build --packages-select ament_cmake_export_libraries
# RUN colcon build --packages-select ament_cmake_export_link_flags
# RUN colcon build --packages-select ament_cmake_include_directories
# RUN colcon build --packages-select ament_cmake_libraries
# RUN colcon build --packages-select ament_cmake_python
# RUN colcon build --packages-select ament_cmake_version
# RUN colcon build --packages-select ament_pep257
# RUN colcon build --packages-select ament_cmake_export_dependencies
# RUN colcon build --packages-select ament_cmake_export_interfaces
# RUN colcon build --packages-select ament_cmake_export_targets
# RUN colcon build --packages-select ament_cmake_target_dependencies
# RUN colcon build --packages-select ament_cmake_test
# RUN colcon build --packages-select ament_copyright
# RUN colcon build --packages-select ament_cmake_gtest
# RUN colcon build --packages-select ament_cmake_pytest
# RUN colcon build --packages-select ament_cpplint
# RUN colcon build --packages-select ament_lint_auto
# RUN colcon build --packages-select ament_lint_cmake
# RUN colcon build --packages-select ament_xmllint
# RUN colcon build --packages-select domain_coordinator
# RUN colcon build --packages-select ament_cmake_gen_version_h
# RUN colcon build --packages-select ament_cmake_gmock
# RUN colcon build --packages-select ament_cmake_lint_cmake
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
# RUN colcon build --packages-select ament_cmake_uncrustify
# RUN colcon build --packages-select ament_lint_common
# RUN colcon build --packages-select ament_cmake_ros

# RUN colcon build --packages-select google_benchmark_vendor
# RUN colcon build --packages-select osrf_pycommon
# RUN colcon build --packages-select osrf_testing_tools_cpp
# RUN colcon build --packages-select ament_cmake_google_benchmark
# RUN colcon build --packages-select ament_index_python
# RUN colcon build --packages-select launch
# RUN colcon build --packages-select launch_xml
# RUN colcon build --packages-select launch_yaml
# RUN colcon build --packages-select launch_testing
# RUN colcon build --packages-select mimick_vendor
# RUN colcon build --packages-select performance_test_fixture
# RUN colcon build --packages-select python_cmake_module
# RUN colcon build --packages-select launch_testing_ament_cmake

# RUN colcon build --packages-select rcutils

# RUN dnf install libc6-dev -y
# RUN sed -i '1s/^/#include <stdint.h> /' /ros2_humble/src/ros2/rcpputils/include/rcpputils/filesystem_helper.hpp
# RUN colcon build --packages-select rcpputils
