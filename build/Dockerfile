FROM debian:buster-slim as binary

ARG TARGETARCH
ARG UPSTREAM_VERSION
ENV DOWNLOAD_URL https://github.com/prysmaticlabs/prysm/releases/download

RUN DEBIAN_FRONTEND=noninteractive \
    apt update && apt install --assume-yes --no-install-recommends wget ca-certificates && \
    wget -q $DOWNLOAD_URL/$UPSTREAM_VERSION/validator-$UPSTREAM_VERSION-linux-${TARGETARCH:-amd64} -O /usr/local/bin/validator && \
    chmod +x  /usr/local/bin/validator

FROM debian:buster-slim

#Copy binaries from build stage
COPY --from=binary /usr/local/bin/validator /usr/local/bin/validator

RUN apt update && \
    apt install -y ca-certificates file && \
    rm -rf /var/lib/apt/lists/*

ADD ssl /ssl/

# Verify binary is in correct arch
RUN /usr/local/bin/validator --version


ENTRYPOINT [ "sh", "-c", "exec validator \
    --zinken \
    --datadir=/data \
    --log-file=/data/validator.log \
    --web \
    --web-host=0.0.0.0 \
    --web-port=80 \
    --beacon-rpc-gateway-provider=$BEACON_RPC_GATEWAY_PROVIDER \
    --beacon-rpc-provider=$BEACON_RPC_PROVIDER \
    $EXTRA_OPTS" \
    ]
