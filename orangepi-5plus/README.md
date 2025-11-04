# Orange Pi 5+ kernel snap

Kernel snap for running Ubuntu Core 22 on the [Orange Pi 5+](http://www.orangepi.org/html/hardWare/computerAndMicrocontrollers/details/Orange-Pi-5-plus-32GB.html).

## Notices

Some notices.

### This kernel is in search of a maintainer!

If you are interested in maintaining this kernel snap, please let us know!
It would also be helpful if the same person maintained this platform's
[gadget snap](https://github.com/canonical/iot-field-gadget-snap/tree/22/orangepi-5plus) :)

## Overview

This snap is built out of three components:

1) A fork of the Ubuntu kernel,
2) The Debian linux firmware package, and
3) An initrd built from a minimal Jammy base image

## Building

This snap should be built using the experimental kernel and
initrd snapcraft plugins.

```
  snap install --classic --channel=latest/edge/kernel-initrd-plugin snapcraft
  snapcraft --enable-experimental-plugins
```
