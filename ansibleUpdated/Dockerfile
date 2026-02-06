FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# Install required packages
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
      openssh-server sudo python3 python3-apt && \
    rm -rf /var/lib/apt/lists/*

# Create ansible user with passwordless sudo (sudoers.d for safety)
RUN useradd -ms /bin/bash ansible && \
    echo "ansible ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/ansible && \
    chmod 0440 /etc/sudoers.d/ansible

# Set up SSH service and home directory
RUN mkdir -p /var/run/sshd /home/ansible/.ssh && \
    chmod 700 /home/ansible/.ssh && \
    chown -R ansible:ansible /home/ansible/.ssh

# Optional: provide authorized keys at build time
# Build with: docker build --build-arg AUTHORIZED_KEYS="ssh-ed25519 AAAA..." .
ARG AUTHORIZED_KEYS=""
RUN if [ -n "$AUTHORIZED_KEYS" ]; then \
      echo "$AUTHORIZED_KEYS" > /home/ansible/.ssh/authorized_keys; \
    else \
      touch /home/ansible/.ssh/authorized_keys; \
    fi && \
    chown ansible:ansible /home/ansible/.ssh/authorized_keys && \
    chmod 600 /home/ansible/.ssh/authorized_keys

# Configure sshd (no root login, no password auth)
RUN sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config && \
    sed -i 's/^#\?PasswordAuthentication.*/PasswordAuthentication no/' /etc/ssh/sshd_config && \
    echo "AllowUsers ansible" >> /etc/ssh/sshd_config

WORKDIR /work
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
