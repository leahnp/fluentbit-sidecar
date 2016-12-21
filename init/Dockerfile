FROM alpine:edge
MAINTAINER Eduardo Silva <eduardo@treasure-data.com>
LABEL Description="Fluent Bit docker image" Vendor="Fluent Organization" Version="1.1"

ENV GOPATH /go
ENV GOBIN $GOPATH/bin
ENV PATH $GOBIN:$PATH

# Do not split this into multiple RUN!
# Docker creates a layer for every RUN-Statement
# therefore an 'apk delete build*' has no effect
RUN apk --no-cache --update add \
                            build-base \
                            ca-certificates \
                            zlib-dev \
                            gdb \
                            git \
                            go \
                            vim \
                            cmake && \
    git clone https://github.com/fluent/fluent-bit && cd fluent-bit/build && \
    cmake -DCMAKE_C_FLAGS_INIT="-DGLOB_TILDE=0" -DFLB_DEBUG=On -DFLB_TRACE=On -DFLB_PROXY_GO=On ../ && \
    make && make install

RUN git clone https://github.com/leahnp/fluent-bit-kafka-output-plugin && \
    cd fluent-bit-kafka-output-plugin && \
    make

RUN adduser -D -g '' -u 1000 -h /home/fluent fluent
RUN chown -R fluent:fluent /home/fluent

# for log storage (maybe shared with host)
RUN mkdir -p /fluent-bit/log
# configuration/plugins path (default: copied from .)
RUN mkdir -p /fluent-bit/etc
RUN chown -R fluent:fluent /fluent-bit

USER fluent
WORKDIR /home/fluent

# ENTRYPOINT ["/usr/local/bin/fluent-bit", "-e", "/fluent-bit-kafka-output-plugin/out_kafka.so"]
ENTRYPOINT ["sh"]