FROM ubuntu:latest

# Install tools
RUN \
    DEBIAN_FRONTEND=noninteractive apt update && \
    DEBIAN_FRONTEND=noninteractive apt upgrade -y && \
    DEBIAN_FRONTEND=noninteractive apt install -y \
    git \
    wget \
    make \
    gawk \
    xz-utils \
    bzip2 \
    gcc \
    g++ \
    build-essential \
    cmake \
    autoconf \
    automake \
    libtool \
    python3 \
    mono-devel \
    qemu-user-static

# Clone musl-cross-make
WORKDIR /build
RUN git clone https://github.com/richfelker/musl-cross-make
WORKDIR /build/musl-cross-make

# Build ARM toolchain
RUN mkdir /opt/armv6
RUN printf \
    # TARGET = arm-linux-musleabihf
    # OUTPUT = /opt/armv6
    # COMMON_CONFIG += CFLAGS="-O2" CXXFLAGS="-O2" LDFLAGS="-s"
    # GCC_CONFIG += --with-arch=armv6 --with-mode=arm --with-fpu=vfp
    "TARGET = arm-linux-musleabihf\nOUTPUT = /opt/armv6\nCOMMON_CONFIG += CFLAGS=\"-O2\" CXXFLAGS=\"-O2\" LDFLAGS=\"-s\"\nGCC_CONFIG += --with-arch=armv6 --with-mode=arm --with-fpu=vfp" \
    > config.mak && \
    make install -j$(nproc)

# Clone mono
WORKDIR /build
RUN git clone --recursive https://gitlab.winehq.org/mono/mono.git
WORKDIR /build/mono

# Build C# code
RUN ./autogen.sh --prefix=/opt/mono --disable-boehm --enable-nls=no --enable-minimal=profiler,debug,logging && \
    make install -j$(nproc) && \
    make clean

# Build ARM tooling
RUN \
    CC=/opt/armv6/bin/arm-linux-musleabihf-gcc \
    CXX=/opt/armv6/bin/arm-linux-musleabihf-g++ \
    AR=/opt/armv6/bin/arm-linux-musleabihf-ar \
    AS=/opt/armv6/bin/arm-linux-musleabihf-as \
    LD=/opt/armv6/bin/arm-linux-musleabihf-ld \
    STRIP=/opt/armv6/bin/arm-linux-musleabihf-strip \
    RANLIB=/opt/armv6/bin/arm-linux-musleabihf-ranlib \
    CFLAGS="-march=armv6k -marm -mtune=mpcore -mtp=soft" \
    LDFLAGS=$CFLAGS \
    ./autogen.sh --host=arm-linux-musleabihf --disable-shared --enable-static-gcc-libs --disable-boehm --disable-mcs-build --prefix=/opt/mono --enable-nls=no --enable-minimal=profiler,debug,logging && \
    make install -j$(nproc) && \
    make clean

# We're done
WORKDIR /