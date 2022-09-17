# Guest Image

This is a "fully enabled" base image.

It extends [eons/img_base](https://github.com/eons-dev/img_base) by combining the following "img_*-enabled" images:
* [img_network-enabled](https://github.com/infrastructure-tech/img_network-enabled/)
* [img_storage-enabled](https://github.com/infrastructure-tech/img_storage-enabled/)

This image is intended to be run on [img_host](https://github.com/infrastructure-tech/img_host), as some of its features require kernel modules and other special accommodations.  
To run this image locally, you have to at least run with `--privileged` and possibly do some extra system configuration.

The goal with this image is to provide full-operating system capabilities in a docker container: giving you the best of all worlds.
Containerizing whole operating system comes with at the cost of reduced speed and increased complexity of development and deployment.  

## Optimization 

### Performance

To minimize the impact on performance, this image (and all images based on eons/img_base) are built with [Alpine Linux](https://www.alpinelinux.org/) and are kept as lightweight as possible. When building binaries for our images, we will compile them in the [eons/img_generic-builder](https://github.com/eons-dev/img_generic-builder), publish them to the [Eons Infrastructure Technologies package repository](https://infrastructure.tech/package), and install them with [EMI](https://github.com/eons-dev/bin_emi). Through this workflow, we guarantee that all that is present on our images is:
* the software we want to run
* the necessary libraries for that software
* EMI (which currently means python3 and some python packages too)
* system utilities for running our software (e.g. `bash`)

See the [docs on img_host](https://github.com/infrastructure-tech/img_host#optimization) for how it's optimized.

### Complexity

To minimize the complexity of development, just use [EBBS](https://github.com/eons-dev/bin_ebbs). EBBS has a `docker` builder that keeps things simple (see below).

To minimize the complexity of deployment, use EBBS, EMI, and infrastructure.tech. We try to make it easy for ya ;)

## Usage

You should set this image as the base image for any workloads you'd like to run on any [Eons Infrastructure Technologies](https://infrastructure.tech) platform. 

### Configuration

When building images off this one, it is best to use the ebbs `docker` builder: [https://github.com/eons-dev/build_docker](https://github.com/eons-dev/build_docker). Doing so will let you easily configure the "enabled" features of this image.

#### Networking

To add a network, configure a directory to mount as described in the [network-enabled docs](https://github.com/infrastructure-tech/img_network-enabled). You must mount this directory by configuring Filesystems (below) (recommended) or by changing your infrastructure to provide the storage in both img_host and img_guest.

Once your network configuration has been added to your image, you can enable it through the [docker builder](https://github.com/eons-dev/build_docker#networks).

#### Filesystems

To add persistent storage to your container, add a `/root/.config/rclone/rclone.conf` file containing all the Rclone filesystems you'd like to mount. Then, mount those filesystems as described in the [docker builder](https://github.com/eons-dev/build_docker#filesystems).