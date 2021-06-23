This page documents how to setup automatic building of [Basebox](https://gitlab.com/fdroid/basebox) VM images with the use of Gitlab-CI. Still WIP …

## Introduction

The goal is to build Basebox VM images with the use of the [fdroid_basebox.py][fdroid-basebox-py] script automatically. One could just hack together a new job in `.gitlab-ci.yml`, but this won't work very well.

[`fdroid_basebox.py`][fdroid-basebox-py] uses [`vmdebootstrap`][vmdebootstrap-debian], which uses loopback devices to create the VM image. However the standard Gitlab "shared" runners are using [Docker as executor][gl-docs-docker] and loopback devices don't really work well inside Docker containers.
This seems to be related to the concept of containerization and the sharing of resources with the host OS. Unfortunately there is very few evidence out there what's the exact cause, but this [Serverfault (StackExchange) question][stackexchange-docker] might be a hint.

The solution is simply not to use a docker executor for the Gitlab runner, but something that doesn't interferes with the host OS: VirtualBox.

Now a documentation of how to setup a Gitlab-Runner with a [VirtualBox executor][gl-docs-virtualbox] will follow.

[fdroid-basebox-py]: https://gitlab.com/fdroid/basebox/-/blob/master/fdroid_basebox.py
[vmdebootstrap-debian]: https://packages.debian.org/buster/vmdebootstrap
[gl-docs-docker]: https://docs.gitlab.com/runner/executors/docker.html
[stackexchange-docker]: https://serverfault.com/questions/701384/loop-device-in-a-linux-container
[gl-docs-virtualbox]: https://docs.gitlab.com/runner/executors/virtualbox.html


## Setup

### System architecture

The guide is for setting up a Gitlab-Runner on a bare metal machine. You'll need two machines and an USB stick. One machine is for preparation (e.g. reading this guide ;) the other one to install the Gitlab-Runner on.

### Setting up the bare metal machine

First, you have to install Debian on a bare metal machine.

- [Download][debian-dl] the latest Debian 10 (Buster) 64-Bit network install ("Netinst") ISO from their homepage.
- Flash this file on an USB stick. You might want to use the `usb-creator-gtk` tool from the Debian repositories.
- Put the USB on the desired bare metal and start a graphical install.
- Set up everything how you want to except:
    - Skip the creation of the _root_ user. This will create a "custom" user with root privileges.
    - When coming to _Software selection_: Unselect everything other except _SSH-Server_ and _standard system utilities_. This will make the OS "headless".
- Finish the installation and reboot.
- If you like, you can now SSH into your "Runner machine" from your "preparation machine".

Now the funny part begins. Let's install some software on our bare bones Debian! We need:
- VirtualBox
- Gitlab-Runner
- qemu-kvm

```bash
# Install qemu-kvm.
sudo apt install qemu-kvm


# Install VirtualBox.
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -

sudo apt install software-properties-common
sudo add-apt-repository "deb [arch=amd64] http://download.virtualbox.org/virtualbox/debian $(lsb_release -cs) contrib"
sudo apt update && sudo apt install virtualbox-6.1

# We also need to install the VirtualBox Extension pack to be able to SSH into our VM.
# You might also look for the latest version of the pack by visiting https://download.virtualbox.org/virtualbox
wget https://download.virtualbox.org/virtualbox/6.1.22/Oracle_VM_VirtualBox_Extension_Pack-6.1.22.vbox-extpack
sudo VBoxManage extpack install Oracle_VM_VirtualBox_Extension_Pack-6.1.22.vbox-extpack


# Install Gitlab-Runner
curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
sudo -E apt-get install gitlab-runner
```

#### References

- How To Install Debian 10 Linux Headless With No GUI Or Window Manager\
https://realtechreviews.com/how-to-install-headless-debian-10-linux-image
- How to Install VirtualBox on Debian Linux 10 Linux\
https://linuxize.com/post/how-to-install-virtualbox-on-debian-10
- Install GitLab Runner using the official GitLab repositories\
https://docs.gitlab.com/runner/install/linux-repository.html

[debian-dl]: https://www.debian.org/distrib/

### Preparing the VirtualBox VM image Gitlab-Runner will run

To be continued …