FROM alpine:3.12.0

ARG test_path
ARG IC_path

COPY $test_path /tmp/$test_path
COPY $IC_path /tmp/$test_path/IC

CMD ["/bin/sh"]
