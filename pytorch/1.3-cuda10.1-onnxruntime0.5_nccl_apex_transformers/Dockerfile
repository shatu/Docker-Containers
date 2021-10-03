# Maps to the 0.6 internal version of the DockerImage
FROM shatu/pytorch:1.3-cuda10.1-onnxruntime0.5_nccl_apex_ninja

##############################################################################
# Installing custom dependencies
##############################################################################
ADD requirements.txt ${STAGE_DIR}/
RUN cd ${STAGE_DIR}/ && \
    pip install -r requirements.txt
RUN rm -rf ${STAGE_DIR}/requirements.txt

