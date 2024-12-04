######################################################################
##
## About:
##
## Starts ouisync with a given repository token, enables network
## options to find peers and start syncing.
##
## Usage:
##
## $ docker build . --tag seed-my-repo --build-arg REPO_TOKEN=<REPO_TOKEN>
## $ docker run -it --net=host seed-my-repo
##
######################################################################

FROM ubuntu:latest
WORKDIR /home/ubuntu

ARG REPO_TOKEN
# TODO: Using the version and timestamp in the file name makes it impossible to
# always download the latest ouisync version.
ARG OUISYNC_PACKAGE=https://github.com/equalitie/ouisync-app/releases/download/v0.8.3-production/ouisync-cli_0.8.3-20241114150353.2ebaebde_amd64.deb
# Useful when there are other devices on the same local network to seed with,
# possible values are `true` and `false`.
ARG ENABLE_LOCAL_DISCOVERY=true

ENV REPO_NAME=my-repo
ENV ENABLE_LOCAL_DISCOVERY=$ENABLE_LOCAL_DISCOVERY

# Install dependencies.
RUN apt-get update -y
RUN apt-get install -y wget libfuse2

# Download ouisync package.
RUN wget -O ouisync-cli.deb $OUISYNC_PACKAGE

# Install ouisync.
RUN dpkg -i ouisync-cli.deb

# Create directory where Ouisync will create ouisync.sock.
# TODO: Ouisync should attempt to create this directory if it doesn't exist
RUN mkdir -p /root/.cache

# Create/Import the repository using the REPO_TOKEN.
RUN ouisync create --share-token "$REPO_TOKEN" --name "$REPO_NAME"

# Enable announcing on the Bittorrent DHT.
RUN ouisync dht --name "$REPO_NAME" true

# Enable peer exchange.
RUN ouisync pex --name "$REPO_NAME" true

# Enable local discovery.
RUN ouisync local-discovery $ENABLE_LOCAL_DISCOVERY

# Bind to a random port and start
CMD ouisync bind quic/0.0.0.0:0 quic/[::]:0 && \
    ouisync start
