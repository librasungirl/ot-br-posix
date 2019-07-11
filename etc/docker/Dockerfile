#  Copyright (c) 2018, The OpenThread Authors.
#  All rights reserved.
#
#  Redistribution and use in source and binary forms, with or without
#  modification, are permitted provided that the following conditions are met:
#  1. Redistributions of source code must retain the above copyright
#     notice, this list of conditions and the following disclaimer.
#  2. Redistributions in binary form must reproduce the above copyright
#     notice, this list of conditions and the following disclaimer in the
#     documentation and/or other materials provided with the distribution.
#  3. Neither the name of the copyright holder nor the
#     names of its contributors may be used to endorse or promote products
#     derived from this software without specific prior written permission.
#
#  THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
#  AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
#  IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
#  ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE
#  LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
#  CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
#  SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
#  INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
#  CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
#  ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
#  POSSIBILITY OF SUCH DAMAGE.
#

FROM ubuntu

ENV DEBIAN_FRONTEND noninteractive
ENV PLATFORM ubuntu

COPY . /app
WORKDIR /app

ENV RELEASE 1
ENV NAT64 1
ENV DNS64 1
ENV DHCPV6_PD 0
ENV NETWORK_MANAGER 0

ENV OTBR_BUILD_DEPS \
    git \
    wget \
    apt-utils \
    build-essential \
    libtool \
    automake \
    libdbus-1-dev \
    autoconf-archive \
    libavahi-common-dev \
    libavahi-client-dev \
    ctags \
    libboost-dev \
    libboost-filesystem-dev \
    libboost-system-dev \
    libjsoncpp-dev

ENV OTBR_REQS \
        sudo \
        rsyslog \
        avahi-daemon \
        iproute2 \
        iputils-ping

ENV OTBR_NORELEASE_DEPS \
    cmake \
    doxygen \
    cpputest

ENV OTBR_NAT64_REQS \
    tayga \
    iptables

ENV OTBR_DNS64_REQS \
    bind9

ENV OTBR_NETWORK_MANAGER_REQS \
    network-manager

ENV OTBR_DHCP6_PD_REQS \
    dhcpcd5

RUN set -xe; \
    apt-get update; \
    apt-get install -y $OTBR_REQS; \
    apt-get install -y $OTBR_BUILD_DEPS; \
    if [ "${RELEASE}" = "0" ]; then apt-get install -y $OTBR_NORELEASE_DEPS; fi; \
    if [ "${NAT64}" = "1" ]; then apt-get install -y $OTBR_NAT64_REQS; fi; \
    if [ "${DNS64}" = "1" ]; then apt-get install -y $OTBR_DNS64_REQS; fi; \
    if [ "${NETWORK_MANAGER}" = "1" ]; then apt-get install -y $OTBR_NETWORK_MANAGER_REQS; fi; \
    if [ "${DHCPV6_PD}" = "1" ]; then apt-get install -y $OTBR_DHCP6_PD_REQS; fi; \
    git submodule update --init --recursive || true; \
    ./bootstrap; \
    ./script/setup; \
    chmod 644 /etc/bind/named.conf.options; \
    mv ./script /tmp; \
    mv ./etc /tmp; \
    find . -delete; \
    rm -rf /usr/include; \
    mv /tmp/script .; \
    mv /tmp/etc .; \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false $OTBR_BUILD_DEPS; \
    if [ "${RELEASE}" = "0" ]; then apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false $OTBR_NORELEASE_DEPS; fi; \
    rm -rf /var/lib/apt/lists/*;

ENTRYPOINT ["/app/etc/docker/docker_entrypoint.sh"]

EXPOSE 80
