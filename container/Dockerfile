# -----------
# Build stage
# -----------

FROM debian:12.8 AS build
WORKDIR /stk

# Set stk version that should be built
ENV STK_VERSION=1.4
ENV ASSET_VERSION=1.1

# Install build dependencies
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && \
    apt-get install --no-install-recommends -y \
      build-essential \
      cmake \
      libbluetooth-dev \
      libsdl2-dev \
      libcurl4-openssl-dev \
      libenet-dev \
      libfreetype6-dev \
      libharfbuzz-dev \
      libjpeg-dev \
      libogg-dev \
      libopenal-dev \
      libpng-dev \
      libssl-dev \
      libvorbis-dev \
      libmbedtls-dev \
      pkg-config \
      zlib1g-dev \
      ca-certificates \
      subversion \
      git curl

# Get code and assets
RUN git clone --branch ${STK_VERSION} --depth=1 https://github.com/supertuxkart/stk-code.git
#RUN git clone --branch 1.4        --depth=1 https://github.com/supertuxkart/stk-code.git
RUN svn checkout https://svn.code.sf.net/p/supertuxkart/code/stk-assets-release/${ASSET_VERSION}/ stk-assets

# Build server
RUN mkdir stk-code/cmake_build && \
    cd stk-code/cmake_build && \
    cmake .. -DSERVER_ONLY=ON -USE_SYSTEM_ENET=ON && \
    make -j$(nproc) && \
    make install

# -----------
# Final stage
# -----------

FROM debian:12.8
WORKDIR /stk

# Install libcurl dependency
RUN apt-get update && \
    apt-get install --no-install-recommends -y libcurl4-openssl-dev curl && \
    rm -rf /var/lib/apt/lists/*

# Copy artifacts from build stage
COPY --from=build /usr/local/bin/supertuxkart /usr/local/bin
COPY --from=build /usr/local/share/supertuxkart /usr/local/share/supertuxkart
COPY entrypoint.sh entrypoint.sh

# Expose the ports used to find and connect to the server
EXPOSE 2757
EXPOSE 2759

ENTRYPOINT ["/stk/entrypoint.sh"]
