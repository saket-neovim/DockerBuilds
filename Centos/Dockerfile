FROM quay.io/centos/centos:stream9

ENV container=docker

# Install systemd and RHCSA tools (minimal, no docs to reduce image size)
RUN dnf -y install \
    systemd systemd-libs systemd-udev \
    vim man-pages man-db \
    net-tools iproute \
    util-linux \
    lvm2 \
    tar \
    && dnf clean all \
    && rm -rf /var/cache/dnf

# Needed for systemd inside container
VOLUME [ "/sys/fs/cgroup" ]
STOPSIGNAL SIGRTMIN+3
CMD ["/sbin/init"]
