#-----------------------------------------------------------------------
# iroha - IROHA container
#
# build : docker build --shm-size 1g -t soramitsu/iroha .
#
# Copyright (c) 2016 Soramitsu,Co.,Ltd.
# All Rights Reserved.
#-----------------------------------------------------------------------
FROM soramitsu/iroha-dev

MAINTAINER Takeshi Yonezu <yonezu@soramitsu.co.jp>

LABEL BUILD="docker build --shm-size 1g -t soramitsu/iroha ."
LABEL RUN="docker run -it --name iroha soramitsu/iroha /bin/bash"

USER root

ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64
ENV IROHA_HOME=/opt/iroha

WORKDIR /opt
RUN git clone https://github.com/hyperledger/iroha.git
WORKDIR /opt/iroha
RUN git checkout feature/fix-sumeragi

WORKDIR /opt/iroha
RUN git clone https://github.com/real-logic/Aeron.git core/vendor/Aeron
RUN git clone https://github.com/real-logic/Agrona.git core/vendor/Agrona
RUN git clone https://github.com/nlohmann/json.git core/vendor/json
RUN git clone https://github.com/gvanas/KeccakCodePackage.git core/vendor/KeccakCodePackage
RUN git clone https://github.com/google/leveldb.git core/vendor/leveldb
RUN git clone https://github.com/luca3104/ed25519.git core/vendor/ed25519
RUN git clone --recursive  https://github.com/MizukiSonoko/Cappuccino.git core/vendor/Cappuccino

WORKDIR $IROHA_HOME/core/vendor/Agrona
RUN ./gradlew
WORKDIR $IROHA_HOME/core/vendor/Aeron
RUN ./gradlew; echo Force skip noisy test
RUN ./cppbuild/cppbuild
WORKDIR $IROHA_HOME/core/vendor/leveldb
RUN make
WORKDIR $IROHA_HOME/core/vendor/ed25519
RUN make
WORKDIR $IROHA_HOME/core/vendor/KeccakCodePackage
RUN make && make generic64/libkeccak.a
WORKDIR $IROHA_HOME/core/infra/crypto
RUN make

RUN mkdir /opt/iroha/build
WORKDIR /opt/iroha/build
RUN cmake ..
RUN make

## USER iroha
WORKDIR /opt/iroha

COPY ./start_iroha.sh /start_iroha.sh

CMD ["/bin/bash","/start_iroha.sh"]