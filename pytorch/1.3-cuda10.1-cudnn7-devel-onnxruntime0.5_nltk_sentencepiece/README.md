#0.5.3
This Image is based on the officla AzureML base image: https://github.com/Azure/AzureML-Containers/blob/master/base/gpu/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04/Dockerfile

It has been modified for the following:
1. Support PyTorch1.3.1
2. Support Apex commit ID: 494f8ab3fc1b0b26949a3bcbb2bcac78008d48c1
3. Support installing arbitrary dependencies using requirements.txt
