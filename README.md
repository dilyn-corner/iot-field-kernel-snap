# Core 22 kernel snaps

A lot of work was done to make snapcraft more easily build cross-architecture
snaps. That work was not, unfortunately, back-ported to be usable for earlier
bases than Core24.

Here is a cool tip for trivializing cross-building :)

Create the below LXD profile config YAML:

```yaml
name: default
description: Default LXD profile
config:
  cloud-init.user-data: |
    #!/bin/sh

    . /etc/os-release

    if [ "$VERSION_CODENAME" = "jammy" ]; then
      { echo 'Types: deb'; \
      echo 'URIs: http://ports.ubuntu.com/ubuntu-ports'; \
      echo 'Suites: jammy jammy-updates jammy-backports'; \
      echo 'Components: main universe restricted multiverse'; \
      echo 'Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg'; \
      echo 'Architectures: armhf arm64 riscv64'; } >> /etc/apt/sources.list.d/cross.sources

      sed -i 's/deb h/deb [arch=amd64] h/' /etc/apt/sources.list

      dpkg add-architecture armhf
      dpkg add-architecture arm64
      dpkg add-architecture riscv64

      apt update
      apt install libc6-armhf-cross libc6-arm64-cross libc6-riscv64-cross

      ln -sf /usr/arm-linux-gnueabihf/lib/ld-linux-armhf.so.3       /lib
      ln -sf /usr/aarch64-linux-gnu/ld-linux-aarch64.so.1           /lib
      ln -sf /usr/riscv64-linux-gnu/lib/ld-linux-riscv64-lp64d.so.1 /lib
    fi
devices:
  eth0:
    network: lxdbr0
    type: nic
  root:
    path: /
    pool: default
    type: disk
used_by: []
project: default
```

Then set that config as the default profile for snapcraft projects:
```bash
  lxc profile create --project snapcraft default < sc-profile.yaml
```

Feel free to add and remove architectures as you see fit. Note that first-time
builds of kernel snaps when using this profile may not work, as it does require
cloud-init so it may take some time for the changes to kick in.

If you'd like to make your SSH keys available to these builds, you can add:

```yaml
  ssh:
    path: /root/.ssh
    shift: "true"
    source: /home/dilyn/.ssh
    type: disk
```

To the `devices` section of the aforementioned config, however the `shift` key
may not be valid for "early" kernels or LXD versions (I don't have any data on
the earliest release these are valid for, apologies).
