# Change a SELinux boolean. A conflict between overlayfs semantics
# and libselinux existed before fedora 37. This examples shows that
# selinux booleans can now be changed correctly as part of the build.
# https://github.com/SELinuxProject/selinux/pull/342

ARG BUILDER_VERSION=38

FROM quay.io/fedora-ostree-desktops/base:38 as kernel-query
RUN rpm -qa kernel --queryformat '%{VERSION}-%{RELEASE}.%{ARCH}' > /kernel-version.txt

FROM registry.fedoraproject.org/fedora:${BUILDER_VERSION} as builder
ARG BUILDER_VERSION
COPY --from=kernel-query /kernel-version.txt /kernel-version.txt
WORKDIR /etc/yum.repos.d
RUN curl -L -O https://src.fedoraproject.org/rpms/fedora-repos/raw/f${BUILDER_VERSION}/f/fedora-updates-archive.repo && \
    sed -i 's/enabled=AUTO_VALUE/enabled=true/' fedora-updates-archive.repo



ARG FEDORA_VERSION=38

ARG ROS_VERSION=iron

ENV LANG=en_US.UTF-8
ENV RPM_ARCH=x86_64
ENV ROS_PYTHON_VERSION=3

RUN dnf update -y

RUN dnf install -y \
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

RUN python3 -m pip install -U \
  flake8-blind-except==0.1.1 \
  flake8-class-newline \
  flake8-deprecated

RUN mkdir -p /opt/ros2/src
WORKDIR /opt/ros2
RUN vcs import --input https://raw.githubusercontent.com/ros2/ros2/${ROS_VERSION}/ros2.repos src

RUN rosdep init
RUN rosdep update
RUN rosdep install \
     --from-paths src \
     --ignore-src -y \
     --skip-keys "assimp fastcdr ignition-cmake2 ignition-math6 python3-matplotlib python3-pygraphviz rti-connext-dds-6.0.1 urdfdom_headers"

WORKDIR /opt/ros2

# TODO: temporary workaround, remove once those packages can be built
RUN touch ./src/ros-visualization/rqt/rqt_gui_cpp/CATKIN_IGNORE && \
touch ./src/ros-visualization/qt_gui_core/qt_gui_app/CATKIN_IGNORE && \
touch ./src/ros-visualization/qt_gui_core/qt_gui_cpp/CATKIN_IGNORE

RUN colcon build --symlink-install

RUN dnf clean all



WORKDIR /
# Uses project_id from: https://release-monitoring.org/project/11706/
RUN curl "https://release-monitoring.org/api/v2/versions/?project_id=11706" | jq --raw-output '.stable_versions[0]' >> /zfs_version.txt
RUN curl -L -O https://github.com/openzfs/zfs/releases/download/zfs-$(cat /zfs_version.txt)/zfs-$(cat /zfs_version.txt).tar.gz && \
    tar xzf zfs-$(cat /zfs_version.txt).tar.gz && mv zfs-$(cat /zfs_version.txt) zfs
WORKDIR /zfs
RUN ./configure -with-linux=/usr/src/kernels/$(cat /kernel-version.txt)/ -with-linux-obj=/usr/src/kernels/$(cat /kernel-version.txt)/ \
    && make -j1 rpm-utils rpm-kmod

FROM quay.io/fedora-ostree-desktops/base:38
COPY --from=builder /zfs/*.rpm /
# For the example we install all RPMS (debug, test, etc).
# In real use cases probably just want the module rpm.
RUN rpm-ostree install /*.$(rpm -qa kernel --queryformat '%{ARCH}').rpm && \
    # we don't want any files on /var
    rm -rf /var/lib/pcp && \
    ostree container commit 
