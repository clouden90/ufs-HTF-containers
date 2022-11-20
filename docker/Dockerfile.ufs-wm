From noaaepic/ubuntu20.04-gnu9.3-hpc-stack:v1.2

CMD ["/bin/bash"]

ENV HOME=/home/builder
COPY --chown=builder:builder ./ufs-weather-model $HOME/ufs-weather-model

USER builder
ENV USER=builder
ARG test_name
ARG run_case
ENV test_name=$test_name
ENV run_case=$run_case
ENV CI_TEST=true
ENV RT_COMPILER=gnu
ENV RT_MACHINE=linux
ENV MACHINE_ID=linux

COPY --chown=builder:builder ./docker/$run_case $HOME/ufs-weather-model/tests/ufs_case_study/$run_case

RUN cd $HOME/ufs-weather-model/tests/ufs_case_study/$run_case && \
    wget https://my-ufs-inputdata.s3.amazonaws.com/Barry_IC.tar.gz && \
    tar -zxvf Barry_IC.tar.gz && \
    mv Barry_IC IC && \
    rm -rf *.tar.gz

WORKDIR $HOME/ufs-weather-model/tests
CMD ./ufs_case_study/$run_case/run_case -p $HOME/ufs-weather-model/tests -n ${test_name}
