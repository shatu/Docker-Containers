#0.6

#docker pull shatu/pytorch:1.3-cuda10.1-onnxruntime0.5_nccl_apex

This Image is based on a DeepScale/DeepSpeed docker image: https://github.com/microsoft/DeepSpeed/blob/master/docker/Dockerfile

It has been modified for the following:
1. Supported installing arbitrary dependencies using requirements.txt
2. Removed dependence on .vimrc and manager.py files

Important: Note that this Docker doesn't contain the actual DeepScale/DeepSpeed package -- later versions will have the compatible package
