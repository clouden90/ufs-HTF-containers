FROM alpine:3.12.0

ARG test_path

COPY $test_path /tmp/$test_path

RUN cd /tmp/$test_path && wget https://my-ufs-inputdata.s3.amazonaws.com/Barry_IC.tar.gz && \
    tar -zxvf Barry_IC.tar.gz && \
    mv Barry_IC IC && \
    chmod -R 755 IC && \
    rm -rf *.tar.gz 

CMD ["/bin/sh"]
