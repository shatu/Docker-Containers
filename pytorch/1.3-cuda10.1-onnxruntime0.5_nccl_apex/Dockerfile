FROM nvidia/cuda:10.1-devel-ubuntu18.04

MAINTAINER Anbang Hu <anbhu@microsoft.com>

##############################################################################
# NCCL
##############################################################################
ENV NCCL_VERSION=2.4.8
RUN apt-get update && apt-get install -y --allow-downgrades --allow-change-held-packages --no-install-recommends \
        libnccl2=${NCCL_VERSION}-1+cuda10.1 \
        libnccl-dev=${NCCL_VERSION}-1+cuda10.1 && \
    rm -rf /var/lib/apt/lists/*

##############################################################################
# CUDNN
##############################################################################
ENV CUDNN_VERSION 7.6.5.32
RUN apt-get update && apt-get install -y --no-install-recommends \ 
        libcudnn7=${CUDNN_VERSION}-1+cuda10.1 \
        libcudnn7-dev=${CUDNN_VERSION}-1+cuda10.1 && \
    apt-mark hold libcudnn7 && \
    rm -rf /var/lib/apt/lists/*

##############################################################################
# Temporary Installation Directory
##############################################################################
ENV STAGE_DIR=/tmp
RUN mkdir -p ${STAGE_DIR}

##############################################################################
# Installation/Basic Utilities
##############################################################################
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        software-properties-common build-essential autotools-dev \
        nfs-common pdsh pssh\
        cmake g++ gcc \
        curl wget dos2unix vim tmux emacs less unzip \
        htop iftop iotop ca-certificates openssh-client openssh-server \
        rsync iputils-ping net-tools sudo

##############################################################################
# Installation Latest Git
##############################################################################
RUN add-apt-repository ppa:git-core/ppa -y && \
    apt-get update && \
    apt-get install -y git && \
    git --version

##############################################################################
# Client Liveness & Uncomment Port 22 for SSH Daemon
##############################################################################
# Keep SSH client alive from server side
RUN echo "ClientAliveInterval 30" >> /etc/ssh/sshd_config
RUN cp /etc/ssh/sshd_config ${STAGE_DIR}/sshd_config && \
    sed "0,/^#Port 22/s//Port 22/" ${STAGE_DIR}/sshd_config > /etc/ssh/sshd_config

##############################################################################
# Vim config
##############################################################################
#ADD .vimrc /root/.vimrc
#RUN dos2unix -q /root/.vimrc

##############################################################################
# Mellanox OFED
##############################################################################
ENV MLNX_OFED_VERSION=4.6-1.0.1.1
RUN apt-get install -y libnuma-dev
RUN cd ${STAGE_DIR} && \
    wget -q -O - http://www.mellanox.com/downloads/ofed/MLNX_OFED-${MLNX_OFED_VERSION}/MLNX_OFED_LINUX-${MLNX_OFED_VERSION}-ubuntu18.04-x86_64.tgz | tar xzf - && \
    cd MLNX_OFED_LINUX-${MLNX_OFED_VERSION}-ubuntu18.04-x86_64 && \
    ./mlnxofedinstall --user-space-only --without-fw-update --all -q && \
    cd ${STAGE_DIR} && \
    rm -rf ${STAGE_DIR}/MLNX_OFED_LINUX-${MLNX_OFED_VERSION}-ubuntu18.04-x86_64*

##############################################################################
# nv_peer_mem
##############################################################################
RUN mkdir -p ${STAGE_DIR} && \
    git clone https://github.com/Mellanox/nv_peer_memory.git ${STAGE_DIR}/nv_peer_memory && \
    cd ${STAGE_DIR}/nv_peer_memory && \
    ./build_module.sh && \
    cd ${STAGE_DIR} && \
    tar xzf ${STAGE_DIR}/nvidia-peer-memory_1.0.orig.tar.gz && \
    cd ${STAGE_DIR}/nvidia-peer-memory-1.0 && \
    apt-get install -y dkms && \
    dpkg-buildpackage -us -uc && \
    dpkg -i ${STAGE_DIR}/nvidia-peer-memory_1.0-8_all.deb

##############################################################################
# OPENMPI
##############################################################################
ENV OPENMPI_BASEVERSION=4.0
ENV OPENMPI_VERSION=${OPENMPI_BASEVERSION}.1
RUN cd ${STAGE_DIR} && \
    wget -q -O - https://download.open-mpi.org/release/open-mpi/v${OPENMPI_BASEVERSION}/openmpi-${OPENMPI_VERSION}.tar.gz | tar xzf - && \
    cd openmpi-${OPENMPI_VERSION} && \
    ./configure --prefix=/usr/local/openmpi-${OPENMPI_VERSION} && \
    make -j"$(nproc)" install && \
    ln -s /usr/local/openmpi-${OPENMPI_VERSION} /usr/local/mpi && \
    # Sanity check:
    test -f /usr/local/mpi/bin/mpic++ && \
    cd ${STAGE_DIR} && \
    rm -r ${STAGE_DIR}/openmpi-${OPENMPI_VERSION}
ENV PATH=/usr/local/mpi/bin:${PATH} \
    LD_LIBRARY_PATH=/usr/local/lib:/usr/local/mpi/lib:/usr/local/mpi/lib64:${LD_LIBRARY_PATH}
# Create a wrapper for OpenMPI to allow running as root by default
RUN mv /usr/local/mpi/bin/mpirun /usr/local/mpi/bin/mpirun.real && \
    echo '#!/bin/bash' > /usr/local/mpi/bin/mpirun && \
    echo 'mpirun.real --allow-run-as-root --prefix /usr/local/mpi "$@"' >> /usr/local/mpi/bin/mpirun && \
    chmod a+x /usr/local/mpi/bin/mpirun

##############################################################################
# Python
##############################################################################
ENV DEBIAN_FRONTEND=noninteractive
ENV PYTHON_VERSION=3
RUN apt-get install -y python3 python3-dev && \
    rm -f /usr/bin/python && \
    ln -s /usr/bin/python3 /usr/bin/python && \
    curl -O https://bootstrap.pypa.io/get-pip.py && \
        python get-pip.py && \
        rm get-pip.py && \
    pip install --upgrade pip && \
    # Print python an pip version
    python -V && pip -V
RUN pip install ipython

##############################################################################
# PyTorch
##############################################################################
ENV PYTORCH_VERSION=1.3.1
ENV TENSORBOARDX_VERSION=2.0
RUN pip install torch==${PYTORCH_VERSION}
RUN pip install tensorboardX==${TENSORBOARDX_VERSION}

##############################################################################
# Some Packages
##############################################################################
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        libsndfile-dev \
        libcupti-dev \
        libjpeg-dev \
        libpng-dev \
        screen
RUN pip install psutil \
                yappi \
                cffi \
                ipdb \
                pandas \
                matplotlib \
                py3nvml \
                pyyaml \
                pyarrow \
                graphviz \
                astor \
                boto3 \
                tqdm \
                sentencepiece \
                msgpack \
                requests \
                pandas \
                sphinx \
                sphinx_rtd_theme \
                scipy \
                numpy \
                sklearn \
                scikit-learn \
                nvidia-ml-py3

# AGI required packages
RUN pip install nltk && \
    python -m nltk.downloader -d /usr/share/nltk_data punkt
RUN pip install spacy==1.8.2 && \
    python -m spacy download en

# Install pssh
#RUN pip install pssh==2.3.1
#RUN sed -i 's/import version/from . import version/g' /usr/local/lib/python3.6/dist-packages/psshlib/cli.py
#ADD manager.py /usr/local/lib/python3.6/dist-packages/psshlib

##############################################################################
# NVIDIA Apex commit 494f8ab3fc1b0b26949a3bcbb2bcac78008d48c1
# This is the latest commit in master as of the docker build time
##############################################################################
RUN git clone https://github.com/NVIDIA/apex.git ${STAGE_DIR}/apex
RUN cd ${STAGE_DIR}/apex && \
    git checkout 494f8ab3fc1b0b26949a3bcbb2bcac78008d48c1 && \
    pip install -v --no-cache-dir --global-option="--cpp_ext" --global-option="--cuda_ext" .
RUN rm -rf ${STAGE_DIR}/apex


##############################################################################
# Installing custom dependencies
##############################################################################
ADD requirements.txt ${STAGE_DIR}/
RUN cd ${STAGE_DIR}/ && \
    pip install -r requirements.txt
RUN rm -rf ${STAGE_DIR}/requirements.txt


##############################################################################
# DeepScale
# NOTE: This step assumes that DeepScale source repository exists in current
#       directory
##############################################################################
#ADD DeepScale ${STAGE_DIR}/DeepScale
#RUN cd ${STAGE_DIR}/DeepScale && \
#    git checkout . && \
#    git checkout v2.1 && \
#    pip install .
#RUN rm -rf ${STAGE_DIR}/DeepScale
#RUN python -c "import deepscale; print(deepscale.__version__)"
