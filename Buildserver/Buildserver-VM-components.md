A list of the components and how they are used to create a Buildserver VM.

## Contents

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

1. `Basebox` is created with [basebox/fdroid_basebox.py](https://gitlab.com/fdroid/basebox/-/blob/master/fdroid_basebox.py)
1. `Bootstrap Buildserver` can be used to setup a Buildserver locally

- Unknown time: `Buildserver provision script`s run
- Unknown time: `makebuildserver` runs