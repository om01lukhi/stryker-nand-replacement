# syntax=docker/dockerfile:1

FROM ubuntu:24.04 AS rootfs-builder

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        debootstrap \
        ca-certificates \
        xz-utils \
        gnupg \
    && rm -rf /var/lib/apt/lists/*

# Use i386 Ubuntu 10.04 because old LTIB BSPs are often happier in a 32-bit userspace.
# If your host/kernel does not allow i386 containers, see the amd64 note below.
RUN debootstrap \
    --arch=i386 \
    --variant=minbase \
    --no-check-gpg \
    lucid \
    /lucid \
    http://old-releases.ubuntu.com/ubuntu/

RUN printf '%s\n' \
    'deb http://old-releases.ubuntu.com/ubuntu lucid main restricted universe multiverse' \
    'deb http://old-releases.ubuntu.com/ubuntu lucid-updates main restricted universe multiverse' \
    'deb http://old-releases.ubuntu.com/ubuntu lucid-security main restricted universe multiverse' \
    > /lucid/etc/apt/sources.list

RUN mkdir -p /lucid/etc/apt/apt.conf.d && \
    printf '%s\n' \
    'Acquire::Check-Valid-Until "false";' \
    'APT::Get::AllowUnauthenticated "true";' \
    > /lucid/etc/apt/apt.conf.d/99old-releases

FROM --platform=linux/386 scratch

COPY --from=rootfs-builder /lucid /

ENV DEBIAN_FRONTEND=noninteractive
ENV LC_ALL=C
ENV LANG=C

RUN apt-get update && \
    apt-get install -y --force-yes \
        sudo \
        bash \
        wget \
        curl \
        tar \
        gzip \
        bzip2 \
        unzip \
        patch \
        diffstat \
        file \
        perl \
        make \
        autoconf \
        automake \
        gettext \
        libgtk2.0-dev \
        rpm \
        bison \
        m4 \
        libfreetype6-dev \
        libdbus-glib-1-dev \
        liborbit2-dev \
        intltool \
        ccache \
        ncurses-dev \
        zlib1g \
        zlib1g-dev \
        gcc \
        g++ \
        libtool \
        libncurses5-dev \
        uuid-dev \
        liblzo2-dev \
        tcl \
        dpkg \
        aptitude \
        gawk \
        flex \
        texinfo \
        help2man \
        cvs \
        subversion \
        git-core \
        rsync \
        bc \
    && apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN useradd -m -s /bin/bash -u 1000 builder && \
    echo 'builder ALL=(ALL) NOPASSWD:ALL' > /etc/sudoers.d/builder && \
    chmod 0440 /etc/sudoers.d/builder

USER builder
WORKDIR /work

CMD ["/bin/bash"]
