# Docker image with conda (Python 3.6), NumPy,
# and OpenCV (version 3.4.1)

FROM ubuntu:16.04

MAINTAINER Oleksandr Semeniuta <oleksandr.semeniuta@gmail.com>

RUN apt-get update && apt-get -qq -y install curl bzip2 \
    && curl -sSL https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -o /tmp/miniconda.sh \
    && bash /tmp/miniconda.sh -bfp /usr/local \
    && rm -rf /tmp/miniconda.sh \
    && conda install -y python=3.6 numpy \
    && conda update conda \
    && apt-get -qq -y remove curl bzip2 \
    && apt-get -qq -y autoremove \
    && apt-get autoclean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log \
    && conda clean --all --yes

RUN echo "source /usr/local/etc/profile.d/conda.sh" >> ~/.bashrc \
&& echo "conda activate" >> ~/.bashrc

ENV PYEXEC /usr/local/bin/python
ENV PYPP /usr/local/lib/python3.6/site-packages
ENV PYLIB /usr/local/lib/libpython3.6m.so
ENV PYID /usr/local/include/python3.6m
ENV OPENCV_VERSION 3.4.1

RUN apt-get update && apt-get upgrade -y \
&& apt-get -qq -y install git build-essential cmake pkg-config \
libjpeg8-dev libtiff5-dev libjasper-dev libpng12-dev \
libavcodec-dev libavformat-dev libswscale-dev libv4l-dev \
libxvidcore-dev libx264-dev \
libgtk-3-dev libatlas-base-dev gfortran \
&& mkdir /tmp/opencv_install \
&& cd /tmp/opencv_install \
&& git clone https://github.com/opencv/opencv.git \
&& git clone https://github.com/opencv/opencv_contrib.git \
&& cd opencv && git checkout $OPENCV_VERSION \
&& cd ../opencv_contrib && git checkout $OPENCV_VERSION \
&& mkdir ../build \
&& cd ../build \
&& cmake \
    -D CMAKE_BUILD_TYPE=RELEASE \
    -D OPENCV_EXTRA_MODULES_PATH=../opencv_contrib/modules \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D PYTHON3_EXECUTABLE=$PYEXEC \
    -D PYTHON3_PACKAGES_PATH=$PYPP \
    -D PYTHON3_LIBRARIES=$PYLIB \
    -D PYTHON3_INCLUDE_DIRS=$PYID \
    -D BUILD_opencv_python2=OFF \
    -D BUILD_opencv_python3=ON \
    -D INSTALL_C_EXAMPLES=OFF \
    -D INSTALL_PYTHON_EXAMPLES=OFF \
    -D BUILD_EXAMPLES=ON \
    ../opencv > cmake_log.txt \
&& make -j4 \
&& make install \
&& mkdir /root/opencv_build \
&& mv cmake_log.txt install_manifest.txt /root/opencv_build \
&& rm -rf /tmp/opencv_install \
&& apt-get -y remove git build-essential cmake \
&& apt-get -y autoremove \
&& apt-get autoclean

CMD /bin/bash
