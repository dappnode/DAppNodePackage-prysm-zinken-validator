FROM debian:buster-slim as binary

ARG TARGETARCH
ARG UPSTREAM_VERSION
ENV DOWNLOAD_URL https://github.com/prysmaticlabs/prysm/releases/download
ENV BINARY_URL $DOWNLOAD_URL/$UPSTREAM_VERSION/beacon-chain-$UPSTREAM_VERSION-linux-$TARGETARCH

RUN DEBIAN_FRONTEND=noninteractive \
    apt update && apt install --assume-yes --no-install-recommends wget && \
    wget -q $BINARY_URL -O /usr/local/bin/beacon-chain && \
    chmod +x  /usr/local/bin/beacon-chain


FROM debian:buster-slim

#Copy binaries from build stage
COPY --from=binary /usr/local/bin/beacon-chain /usr/local/bin/beacon-chain

RUN apt update && \
    apt install -y ca-certificates && \
    rm -rf /var/lib/apt/lists/*

ADD ssl /ssl/

# Verify binary is in correct arch
RUN beacon-chain --version


ENTRYPOINT [ "sh", "-c", "exec beacon-chain --medalla --datadir=/data --log-file=/data/beacon-chain.log --rpc-host 0.0.0.0 --grpc-gateway-host 0.0.0.0 --monitoring-host 0.0.0.0 --http-web3provider=$HTTP_WEB3PROVIDER $EXTRA_OPTS" ]