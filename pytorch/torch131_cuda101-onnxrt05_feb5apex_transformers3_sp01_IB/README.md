#0.8
#docker pull shatu/pytorch:torch131_cuda101-onnxrt05_feb5apex_transformers3_sp01_IB

This Image is based on a DeepScale/DeepSpeed docker image: https://github.com/microsoft/DeepSpeed/blob/master/docker/Dockerfile

It has been modified for the following:
1. Supported installing arbitrary dependencies using requirements.txt
2. Removed dependence on .vimrc and manager.py files
3. Incorporation of recommendations from Nvidia on installing Mellanox related packages

Important:
1) Note that this Docker doesn't contain the actual DeepScale/DeepSpeed package -- later versions might have the compatible package
