FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.1-cudnn8-ubuntu18.04
RUN apt-get update


# create conda environment
RUN conda update -n base -c defaults conda -y
RUN conda create -n marlin python=3.8 -y
RUN echo ". /opt/miniconda/etc/profile.d/conda.sh" >> ~/.bashrc


ADD requirements.txt /workdir/
WORKDIR /workdir

RUN /opt/miniconda/envs/marlin/bin/pip install -r requirements.txt
ENV PATH /opt/miniconda/envs/marlin/bin:$PATH

