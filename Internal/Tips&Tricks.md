# `config.yml` tweaks

```
build_server_always: true          (so you don't need to specify at runtime)
deploy_process_logs: true          (so build logs are kept even if the build failed)
```

# Useful aliases for common tasks

```
alias fd='cd /home/fdroid/fdroiddata'

alias fb='fd && fdroid build --verbose --scan-binary'
alias fbl='fb --latest'
alias fcu='fd && fdroid checkupdates --verbose --auto --allow-dirty'
alias fl='fd && fdroid lint -f'
alias fp='fd && fdroid publish --verbose'
alias fpu='fp && fdroid update --create-metadata --delete-unknown --pretty --verbose' # add `--create-key` on the first run
alias frm='fd && fdroid rewritemeta --verbose'
alias fs='fd && fdroid scanner --verbose'
alias fse='fdroid scanner --verbose --exodus'
alias mf='fd && $EDITOR'
alias vg='cd /home/fdroid/fdroiddata/builder && vagrant resume && vagrant ssh'

alias ak='java -jar ~/apktool_2.6.1.jar'

alias ta='tmux new-session -A -s'
alias tl='tmux list-sessions'
```

Example workflow:

```
ta run                       (create a tmux session to run the build in; allows you to switch to another session and do something else in the meantime, not run another build but: scan, rewritemeta, lint, checkupdate, publish etc)
mf metadata/appid.yml        (edit the file)
fl appid                     (lint verify)
frm appid                    (rewritemeta to format it properly)
fbl appid                    (build latest version)
fp appid                     (sign the APK so you can test it on device)

To test AUM:
mf metadata/appid.yml        (edit the file to add AUM, edit versionCode to a smaller number to simulate)
fcu appid                    (run checkupdate to verify if newest version is picked up)
```

# How to suspend the VM after a build

As contributors we need to enter the VM rather often, since most recipes that we build have failed using the usual simple steps or the Gitlab CI can't handle some cases.

Hence we've can edit `fdroidserver/fdroidserver/build.py` and replaced `destroy` with `suspend` around [here](https://gitlab.com/fdroid/fdroidserver/-/blob/8267fb2cafe4fd5f517f27ea4d2646e7f1ff4ef2/fdroidserver/build.py#L304-L305) so at the end of the build the VM can still be used. We can enter it to tweak/test the steps of the recipe or analyse the dependency tree or search/extract an APK/file that was not found automatically, or we need etc.

# Moving files from/to a suspended VM

First install while, logged in as your normal user eg. `fdroid`, `scp` for Vagrant : `vagrant plugin install vagrant-scp`

Now you can extract files from the VM, _(you run in folder `~/fdroiddata/builder/`)_: `vagrant scp :/home/vagrant/build/appid/path/ .`\
(Don't forget the dot at the end or put a better path in its place to save files to)

Or inject files in the VM, _(you run in folder `~/fdroiddata/builder/`)_: `vagrant scp /path/to/localfile :/home/vagrant/path/`

# How to recover a suspended fdroidserver VM that was halted _(Stretch, to be updated for Bullseye)_

The trouble is that even if the VM was suspended there's a chance _(afaics bigger if the VM runs for longer or if it is left in suspended state longer)_ that the VM will refuse to restart. Same behaviour as if it was halted manually.

Trying to start it:

```
$ cd ~/fdroiddata/builder
$ vagrant halt ; vagrant up ; vagrant ssh    (or 'vg' alias above)
==> default: Halting domain...
Bringing machine 'default' up with 'libvirt' provider...
==> default: Starting domain.
==> default: Waiting for domain to get an IP address...
==> default: Waiting for SSH to become available...
==> default: Creating shared folders metadata...
==> default: Halting domain...
The following SSH command responded with a non-zero exit status.
Vagrant assumes that this means the command failed!
sed -i '/#VAGRANT-BEGIN/,/#VAGRANT-END/d' /etc/fstab
Stdout from the command:
Stderr from the command:
==> default: Domain is not suspended.
==> default: Domain is not running. Please run `vagrant up` or `vagrant resume` first.
```

Which command failed? Hmm, let's debug this `vagrant up --debug`

```
...
DEBUG ssh: stderr: bash: sudo: command not found
```

...ah, as expected, we nuke `sudo` after the first steps.

At @jspricke advice, _"you could mount the image and install sudo again"_

So let's do this them, looks [easy](https://gist.github.com/shamil/62935d9b456a6f9877b5) enough:

```
wget https://security.debian.org/debian-security/pool/updates/main/s/sudo/sudo_1.8.19p1-2.1+deb9u3_amd64.deb
sudo modprobe nbd max_part=8
sudo qemu-nbd --connect=/dev/nbd0 /var/lib/libvirt/images/builder_default.img (adapt to yours as needed)
sudo mount /dev/nbd0p1 /mnt/
sudo su   (so we don't mess perms later just in case)
mc   (so we don't deal with unzipping and messed perms)
...in mc from .deb copy CONTENTS/* to /mnt/
echo 'vagrant ALL = NOPASSWD: ALL' > /mnt/etc/sudoers.d/vagrant 
chmod 440 /mnt/etc/sudoers.d/vagrant 
exit
sudo umount /mnt/
sudo qemu-nbd --disconnect /dev/nbd0
sudo rmmod nbd
```

Now run `vagrant up ; vagrant ssh` and you end up logged as needed.

# Disabling merge request pipelines in MRs to `fdroid/fdroiddata`

[Merge request pipelines](https://docs.gitlab.com/ee/ci/pipelines/merge_request_pipelines.html) allows contributors to run CI pipelines within the context of the `fdroid/fdroiddata` without a credit card. However, sometimes it's not desirable (e.g., when you have more powerful self-hosted CI runners).

To disable merge request pipelines for your merge requests:

### Permanently

Define the variable in your project which has [higher precedence](https://docs.gitlab.com/ee/ci/variables/#cicd-variable-precedence) than that defined in the CI script.

### Temporarily

Push with `git push -o ci.variable="CI_ENABLE_MERGE_REQUEST_PIPELINES=false"`