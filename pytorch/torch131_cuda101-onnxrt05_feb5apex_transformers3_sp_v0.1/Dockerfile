# Maps to the 0.6.3 internal version of the DockerImage
FROM shatu/pytorch:1.3-cuda10.1-onnxruntime0.5_nccl_apex_ninja

##############################################################################
# Installing custom dependencies
##############################################################################
RUN apt-get update -y
RUN apt-get install fuse -y


ADD requirements.txt ${STAGE_DIR}/
RUN cd ${STAGE_DIR}/ && \
    pip install -r requirements.txt
RUN rm -rf ${STAGE_DIR}/requirements.txt


RUN ldconfig /usr/local/cuda/lib64/stubs && \
    # Install AzureML SDK
    pip install --no-cache-dir --index-url https://azuremlsdktestpypi.azureedge.net/dev/aml/office/134157926D8F --extra-index-url https://pypi.python.org/simple azureml-defaults==0.1.0.* && \
    pip install --no-cache-dir --index-url https://azuremlsdktestpypi.azureedge.net/dev/aml/office/134157926D8F --extra-index-url https://pypi.python.org/simple azureml-telemetry==0.1.0.* && \
    pip install --no-cache-dir --index-url https://azuremlsdktestpypi.azureedge.net/dev/aml/office/134157926D8F --extra-index-url https://pypi.python.org/simple azureml-dataprep

