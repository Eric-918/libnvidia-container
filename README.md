# libnvidia-container

[![GitHub license](https://img.shields.io/badge/license-New%20BSD-blue.svg?style=flat-square)](https://raw.githubusercontent.com/NVIDIA/libnvidia-container/master/LICENSE)
[![GitHub release](https://img.shields.io/github/release/NVIDIA/libnvidia-container/all.svg?style=flat-square)]()
[![Packages ubuntu](https://img.shields.io/badge/packages-ubuntu-b956e8.svg?style=flat-square)](https://packages.nvidia.com/app/compute/container/search?q=libnvidia-container&filter=debs)
[![Packages centos](https://img.shields.io/badge/packages-centos-b956e8.svg?style=flat-square)](https://packages.nvidia.com/app/compute/container/search?q=libnvidia-container&filter=rpms)
[![Travis](https://img.shields.io/travis/NVIDIA/libnvidia-container.svg?style=flat-square)](https://travis-ci.org/NVIDIA/libnvidia-container)
[![Coverity Scan](https://img.shields.io/coverity/scan/12444.svg?style=flat-square)](https://scan.coverity.com/projects/nvidia-libnvidia-container)

**Warning: This project is an alpha release, it is not intended to be used in production systems.**

This repository provides a library and a simple CLI utility to automatically configure GNU/Linux containers leveraging NVIDIA hardware.\
The implementation relies on kernel primitives and is designed to be agnostic of the container runtime.

## CLI usage example

```sh
# Setup a rootfs based on Ubuntu 16.04 inside new namespaces
cd $(mktemp -d) && mkdir rootfs
sudo unshare --mount --pid --fork
curl http://cdimage.ubuntu.com/ubuntu-base/releases/16.04/release/ubuntu-base-16.04-core-amd64.tar.gz | tar -C rootfs -xz
useradd -R $(realpath rootfs) -U -u 1000 -s /bin/bash nvidia
mount --bind rootfs rootfs
mount --make-private rootfs
cd rootfs

# Mount standard filesystems
mount -t proc none proc
mount -t sysfs none sys
mount -t tmpfs none tmp
mount -t tmpfs none run

# Isolate the first GPU device along with basic utilities
nvidia-container-cli --load-kmods configure --no-cgroups --utility --device 0 .

# Change into the new rootfs
pivot_root . mnt
umount -l mnt
exec chroot --userspec 1000:1000 . env -i bash

# Run nvidia-smi from within the container
nvidia-smi -L
```

## Copyright and License

This project is released under the [BSD 3-clause license](https://github.com/NVIDIA/libnvidia-container/blob/master/LICENSE).

## Issues and Contributing

A signed copy of the [Contributor License Agreement](https://raw.githubusercontent.com/NVIDIA/libnvidia-container/master/CLA) needs to be provided to <a href="mailto:digits@nvidia.com">digits@nvidia.com</a> before any change can be accepted.

* Please let us know by [filing a new issue](https://github.com/NVIDIA/libnvidia-container/issues/new)
* You can contribute by opening a [pull request](https://help.github.com/articles/using-pull-requests/)
