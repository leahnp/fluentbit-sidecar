FROM ubuntu

MAINTAINER Leah Petersen <leahnpetersen@gmail.com>
LABEL Description="Fluent Bit docker image" Vendor="Fluent Organization" Version="1.1"

ENV GOPATH /go
ENV GOBIN $GOPATH/bin
ENV PATH $GOBIN:$PATH

RUN   apt-get update && \
        apt-get -y upgrade && \
        apt-get install -y \
        build-essential \
        zlib1g-dev \
        git \
        golang \
        cmake && \
    git clone https://github.com/fluent/fluent-bit && cd fluent-bit/build && \
    cmake -DCMAKE_BUILD_TYPE=DEBUG -DFLB_DEBUG=On -DFLB_TRACE=On -DFLB_PROXY_GO=On ../ && \
    make && make install

RUN git clone https://github.com/leahnp/fluent-bit-kafka-output-plugin && \
    cd fluent-bit-kafka-output-plugin && \
    make

RUN useradd -ms /bin/bash fluent
RUN chown -R fluent:fluent /home/fluent

# for log storage (maybe shared with host)
RUN mkdir -p /fluent-bit/log
# configuration/plugins path (default: copied from .)
RUN mkdir -p /fluent-bit/etc
RUN chown -R fluent:fluent /go
RUN chown -R fluent:fluent /fluent-bit
RUN chown -R fluent:fluent /fluent-bit-kafka-output-plugin

COPY fluent.conf /home/fluent/fluent.conf


USER fluent
WORKDIR /home/fluent

ENTRYPOINT ["sh"]