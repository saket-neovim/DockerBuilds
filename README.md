# Docker Environments for Linux and Ansible Labs

Small Docker build/run examples for systemd practice, an SSH-accessible Ansible target, and a local Jenkins instance. These support isolated learning exercises rather than production workloads.

## Contents

| Directory | Purpose |
| --- | --- |
| [Centos](Centos/) | CentOS/systemd practice image and run helper |
| [ansibleUpdated](ansibleUpdated/) | Ubuntu 22.04 with Python, SSH, and an `ansible` user |
| [Jenkins](Jenkins/) | Run helper for the upstream Jenkins LTS image |

The tracked `DockerBuilds` entry is an uninitialized Git link without a `.gitmodules` URL. It is not needed by the examples below; its nested content is unavailable in a normal clone.

## Requirements and usage

Install Docker with a Linux container runtime. The systemd example requires a compatible cgroup environment and uses a privileged container; inspect its helper before running it.

Build and start an Ansible target using **your own public key**:

```bash
docker build --build-arg AUTHORIZED_KEYS="$(cat "$HOME/.ssh/id_ed25519.pub")" \
  -t ansible-target ./ansibleUpdated
docker run -d --name ansible-target -p 127.0.0.1:2222:22 ansible-target
ssh -p 2222 ansible@127.0.0.1
```

The image enables passwordless sudo for lab tasks and disables password SSH authentication. A build without `AUTHORIZED_KEYS` has an empty authorized-key file; it does not provide SSH access. Never supply a private key to the build.

For Jenkins:

```bash
docker run -d --name jenkins \
  -p 127.0.0.1:8080:8080 -p 127.0.0.1:50000:50000 \
  -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```

Complete setup in your local browser. Retrieve the initial admin password locally with `docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword`; do not save its output in this repository.

## Validation and cleanup

Check container status/logs and test SSH with the chosen public key. An image building successfully does not prove SSH, sudo, or systemd behavior. No committed deployment evidence or automated image tests are provided.

Remove the named lab containers after use with `docker rm -f ansible-target jenkins`. The named Jenkins volume survives container removal; delete it separately only after deciding its data is disposable. Keep credentials, identity files, and generated output local. The committed personal public-key file has been removed; older revisions still retain it.
