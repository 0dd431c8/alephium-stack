FROM ubuntu

ARG RELEASE=3.6.2

RUN apt-get update && apt-get install -y openjdk-17-jre
RUN apt-get install -y curl
RUN apt-get install -y git
RUN apt-get install -y \
    build-essential \
    gcc \
    g++ \
    make \
    libc6-dev

RUN curl -L --proto "=https" https://github.com/alephium/alephium/releases/download/v${RELEASE}/alephium-${RELEASE}.jar -o /alephium.jar && \
  mkdir -p /alephium-home && usermod -d /alephium-home nobody && chown nobody /alephium-home && \
  mkdir -p ~nobody/.alephium && chown nobody ~nobody/.alephium && \
  mkdir -p ~nobody/.alephium-wallets && chown nobody ~nobody/.alephium-wallets


COPY devnet.conf /alephium-home/.alephium/user.conf
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

WORKDIR /
RUN git clone https://github.com/wolfcw/libfaketime.git -b v0.9.10
WORKDIR /libfaketime/src
RUN make install

WORKDIR /

RUN echo "+0" > ~nobody/.faketimerc



# http
EXPOSE 12973
# ws
EXPOSE 11973
# miner
EXPOSE 10973
# p2p
EXPOSE 9973

VOLUME /alephium-home/.alephium

VOLUME /alephium-home/.alephium-wallets

USER nobody

ENV JAVA_NET_OPTS="-Djava.net.preferIPv4Stack=true"
ENV JAVA_MEM_OPTS="-Xms2048M -XX:+UseStringDeduplication -XX:+OptimizeStringConcat -XX:+UseNUMA"
ENV JAVA_GC_OPTS="-XX:+UseShenandoahGC -XX:+DisableExplicitGC"
ENV JAVA_EXTRA_OPTS=""

ENV LD_PRELOAD=/usr/local/lib/faketime/libfaketime.so.1

ENV FAKETIME_NO_CACHE=1
# ENV FAKETIME="+0d"

ENTRYPOINT /entrypoint.sh
