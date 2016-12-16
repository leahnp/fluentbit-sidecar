FROM alpine:3.4
MAINTAINER Leah Petersen <leahnpetersen@gmail.com>
LABEL Description="Fluent Bit with Kafka out docker image"

# Do not split this into multiple RUN!
# Docker creates a layer for every RUN-Statement
# therefore an 'apk delete build*' has no effect
RUN apk --no-cache --update add \
                            build-base \
                            ca-certificates \
                            zlib-dev \
                            cmake && \
    wget -O /tmp/fluent-bit-0.9.1.tar.gz http://fluentbit.io/releases/0.9/fluent-bit-0.9.1.tar.gz && \
    cd /tmp && tar zxfv fluent-bit-0.9.1.tar.gz && cd fluent-bit-0.9.1/build/ && \
    cmake -DFLB_DEBUG=On -DFLB_TRACE=On ../ && \
    make && make install && \
    apk del zlib-dev && \
    rm -rf /tmp/*


RUN adduser -D -g '' -u 1000 -h /home/fluent fluent
RUN chown -R fluent:fluent /home/fluent

# for log storage (maybe shared with host)
RUN mkdir -p /fluent-bit/log
# configuration/plugins path (default: copied from .)
RUN mkdir -p /fluent-bit/etc
RUN chown -R fluent:fluent /fluent-bit

# put output plugin in folder in plugins
RUN -O /fluent-bit/plugins/out_kafka/ wget https://github.com/leahnp/fluent-bit-kafka-output-plugin

# run the makefile to get a build
RUN Make #makefil in out_kafka plugin

USER fluent
WORKDIR /home/fluent

ENTRYPOINT ["sh"]