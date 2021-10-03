FROM mcr.microsoft.com/azureml/base-gpu:openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04

COPY requirements.txt /tmp/requirements.txt

# To resolve horovod hangs due to a known NCCL issue in version 2.4.
# Can remove it once we upgrade NCCL to 2.5+.
# https://github.com/horovod/horovod/issues/893
ENV NCCL_TREE_THRESHOLD=0

# Install Horovod, temporarily using CUDA stubs
RUN ldconfig /usr/local/cuda/lib64/stubs && \
    conda install -y conda=4.6.14 python=3.6.2 && conda clean -ay && \
    # Install AzureML SDK
    pip install --no-cache-dir azureml-defaults && \
    # Install PyTorch
    pip install torch==1.3.1 && \
    pip install --no-cache-dir mkl==2018.0.3 && \
    # Install Horovod
    pip install --no-cache-dir horovod==0.18.1 && \
    ldconfig && \
    pip install tensorboard==1.14.0 && \
    pip install future==0.17.1


##############################################################################
# Temporary Installation Directory
##############################################################################
ENV STAGE_DIR=/tmp
RUN mkdir -p ${STAGE_DIR}

##############################################################################
# NVIDIA Apex commit 494f8ab3fc1b0b26949a3bcbb2bcac78008d48c1
# This is the latest commit in master as of the docker build time
##############################################################################
RUN git clone https://github.com/NVIDIA/apex.git ${STAGE_DIR}/apex
RUN cd ${STAGE_DIR}/apex && \
    git checkout 494f8ab3fc1b0b26949a3bcbb2bcac78008d48c1 && \
    pip install -v --no-cache-dir --global-option="--cpp_ext" --global-option="--cuda_ext" .
RUN rm -rf ${STAGE_DIR}/apex

RUN pip install -r /tmp/requirements.txt

