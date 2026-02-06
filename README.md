# DockerBuilds

This folder contains Docker build/run helpers for local labs. All examples are safe to publish; do not paste real passwords, private keys, or tokens into these files.

## Centos (systemd lab)

Build:
```bash
docker build -t rhcsa-centos9 ./Centos
```

Run:
```bash
docker run --privileged -it \
  --name rhcsa-lab \
  -v /sys/fs/cgroup:/sys/fs/cgroup:ro \
  rhcsa-centos9
```

## ansibleUpdated (Ansible target image)

Build without keys (empty authorized_keys):
```bash
docker build -t ansible-target ./ansibleUpdated
```

Build with a public key (recommended):
```bash
docker build \
  --build-arg AUTHORIZED_KEYS="ssh-ed25519 AAAA... user@example.com" \
  -t ansible-target \
  ./ansibleUpdated
```

Run:
```bash
docker run -d \
  --name ansible-target \
  -p 2222:22 \
  ansible-target
```

## Jenkins

Run:
```bash
docker run -d \
  --name jenkins \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
```

Get the initial admin password:
```bash
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```
