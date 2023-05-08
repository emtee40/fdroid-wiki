A list of the components and how they are used to create a _buildserver_ guest VM.  The production _buildserver_ guest runs in a VM using Vagrant with either VirtualBox or _libvirt_.  The release builds need the security isolation provided by a VM. The CI build setup runs in Docker, since Docker is better suited for CI and security isolation is not so important.

[[_TOC_]]

## Components

- [Basebox](https://gitlab.com/fdroid/basebox)
- [Bootstrap Buildserver](https://gitlab.com/fdroid/fdroid-bootstrap-buildserver)
- [Buildserver provision scripts](https://gitlab.com/fdroid/fdroidserver/-/tree/master/buildserver)
  - [provision-android-ndk](https://gitlab.com/fdroid/fdroidserver/-/blob/master/buildserver/provision-android-ndk)
  - [provision-android-sdk](https://gitlab.com/fdroid/fdroidserver/-/blob/master/buildserver/provision-android-sdk)
  - [provision-apt-get-install](https://gitlab.com/fdroid/fdroidserver/-/blob/master/buildserver/provision-apt-get-install)
  - [provision-apt-proxy](https://gitlab.com/fdroid/fdroidserver/-/blob/master/buildserver/provision-apt-proxy)
  - [provision-gradle](https://gitlab.com/fdroid/fdroidserver/-/blob/master/buildserver/provision-gradle)
  - [setup-env-vars](https://gitlab.com/fdroid/fdroidserver/-/blob/master/buildserver/setup-env-vars)
- [makebuildserver](https://gitlab.com/fdroid/fdroidserver/-/blob/master/makebuildserver)
- [ansible-debian-enable-kvm-nesting](https://gitlab.com/fdroid/ansible-debian-enable-kvm-nesting)
- [ansible-role-install-fdroidserver-dependencies](https://gitlab.com/fdroid/ansible-role-install-fdroidserver-dependencies)
- [ansible-debian-install-vagrant-libvirt](https://gitlab.com/fdroid/ansible-debian-install-vagrant-libvirt)

## Assembling

1. _basebox_ is created with [basebox/fdroid_basebox.py](https://gitlab.com/fdroid/basebox/-/blob/master/fdroid_basebox.py)
1. _fdroid-bootstrap-buildserver_ will create complete _buildserver_ instance with a _buildserver_ guest VM, both the host and guest VMs are built on top of _basebox_.  The production _buildserver_ host runs on bare metal, so it uses a Debian install, not _basebox_.

- Unknown time: `Buildserver provision script`s run
- Unknown time: `makebuildserver` runs


## "Control" scripts

There are scripts in _/home/fdroid/control_ which automate the main steps of the whole build cycle.  There are a couple of manual triggers for triggering things outside of the regular cycle, at the next opportunity:

* `touch ~/control/makebs-next` - run _makebuildserver_.
* `touch ~/control/pullfdroidserver-next` - pull in the latest _fdroidserver_ commits.


## Maintenance

On the GCC CFarm instances using Vagrant/libvirt, it is possible to shrink the libvirt disks:

* shut down VM
* `virt-sparsify --in-place fdroid-gitlab-runner_default.img`

### Azure setup

* mount _/home_ on extra storage
* set up libvirt default storage pool on _/home/default-storage-pool_
* bind mount _/var/lib/libvirt/images_:<br/>
  `/home/default-storage-pool  /var/lib/libvirt/images none    bind    0       0`
* _fdroidserver/makebuildserver.config.py_ -> `apt_package_cache = False`
* `mkdir -p /root/code/fdroid`
* `cd /root/code/fdroid`
* `git clone https://gitlab.com/fdroid/fdroid-bootstrap-buildserver.git`
* `git clone https://gitlab.com/fdroid/fdroid-cfarm-bootstrap.git`
* _authorized_keys_ from fdroid-cfarm-bootstrap/files/contributor_keys
