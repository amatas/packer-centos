# Packer templates for CentOS

### Overview

This repository contains templates for Ubuntu that can create Vagrant boxes
using Packer.

## Current Boxes

64-bit boxes:

* [box-cutter/centos65](https://vagrantcloud.com/box-cutter/centos65) - CentOS 6.5 (64-bit)
* [box-cutter/centos65-desktop](https://vagrantcloud.com/box-cutter/centos65-desktop) - CentOS 6.5 Desktop (64-bit)
* [box-cutter/centos64](https://vagrantcloud.com/box-cutter/centos64) - CentOS 6.4 (64-bit)
* [box-cutter/centos64-desktop](https://vagrantcloud.com/box-cutter/centos64-desktop) - CentOS 6.4 Desktop (64-bit)
* [box-cutter/centos510](https://vagrantcloud.com/box-cutter/centos510) - CentOS 5.10 (64-bit)
* [box-cutter/centos59](https://vagrantcloud.com/box-cutter/centos59) - CentOS 5.9 (64-bit)

32-bit boxes:

* [box-cutter/centos65-i386](https://vagrantcloud.com/box-cutter/centos65-i386) - CentOS 6.5 (32-bit)
* [box-cutter/centos64-i386](https://vagrantcloud.com/box-cutter/centos64-i386) - CentOS 6.4 (32-bit)
* [box-cutter/centos510-i386](https://vagrantcloud.com/box-cutter/centos510-i386) - CentOS 5.10 (32-bit)
* [box-cutter/centos59-i386](https://vagrantcloud.com/box-cutter/centos59-i386) - CentOS 5.9 (32-bit)

## Building the Vagrant boxes

To build all the boxes, you will need Packer and both VirtualBox and VMware Fusion
installed.

A GNU Make `Makefile` drives the process via the following targets:

    make        # Build all the box types (VirtualBox & VMware)
    make test   # Run tests against all the boxes
    make list   # Print out individual targets
    make clean  # Clean up build detritus
    
### Tests

The tests are written in [Serverspec](http://serverspec.org) and require the
`vagrant-serverspec` plugin to be installed with:

    vagrant plugin install vagrant-serverspec
    
The `Makefile` has individual targets for each box type with the prefix
`test-*` should you wish to run tests individually for each box.

Similarly there are targets with the prefix `ssh-*` for registering a
newly-built box with vagrant and for logging in using just one command to
do exploratory testing.  For example, to do exploratory testing
on the VirtualBox training environmnet, run the following command:

    make ssh-box/virtualbox/centos65-nocm.box
    
Upon logout `make ssh-*` will automatically de-register the box as well.

### Makefile.local override

You can create a `Makefile.local` file alongside the `Makefile` to override
some of the default settings.  It is most commonly used to override the
default configuration management tool, for example with Chef:

    # Makefile.local
    CM := chef

Changing the value of the `CM` variable changes the target suffixes for
the output of `make list` accordingly.

Possible values for the CM variable are:

* `nocm` - No configuration management tool
* `chef` - Install Chef
* `puppet` - Install Puppet
* `salt`  - Install Salt

You can also specify a variable `CM_VERSION`, if supported by the
configuration management tool, to override the default of `latest`.
The value of `CM_VERSION` should have the form `x.y` or `x.y.z`,
such as `CM_VERSION := 11.12.4`

Another use for `Makefile.local` is to override the default locations
for the Ubuntu install ISO files.

For CentOS, the ISO path variables are:

* CENTOS59_X86_64
* CENTOS59_I386
* CENTOS510_X86_64
* CENTOS510_I386
* CENTOS64_X86_64
* CENTOS64_I386
* CENTOS65_X86_64
* CENTOS64_I386

This override is commonly used to speed up Packer builds by
pointing at pre-downloaded ISOs instead of using the default
download Internet URLs:
`CENTOS65_X86_64 := file:///Volumes/CentOS/CentOS-6.5-x86_64-bin-DVD1.iso`