# Get access

There are a number of _buildserver_ instances which community members can get access to.  You do not need to be a core contributor to get access. You may ask @jspricke @TheLastProject @uniqx or anyone else who has access to the machines, on [Matrix](https://matrix.to/#/#fdroid-dev:f-droid.org).

# Setup

Once you have been granted access, you will need to copy the relevant config from below and put it into _~/.ssh/config_.

This aims to be as close as possible to the production setup.  There is an Ubuntu/focal chroot included, since `fdroid publish` is run on Ubuntu/focal.  To use that chroot, just run: `schroot`.

## <tt>fdroid-buildserver-full-copy</tt>

This instance is set up to be a close approximation of the complete production _buildserver_ setup, with a complete copy of the _buildserver_ VM, app source repos, and all the files from the _repo_ and _archive_ sections.


```config
Host fdroid-buildserver-full-copy
  User fdroid
  Hostname 140.211.166.80
  PasswordAuthentication no
```

* Hosted on OSUOSL's OpenStack Cloud
* _rsync_ for _repo_ and _archive_ in _/etc/cron.monthly/rsync-fdroid_
* _/home/fdroid/fetch-all-source-repos.sh_ for getting app source repos


# Temporary instances

If your SSH public key is listed [here](https://gitlab.com/fdroid/fdroid-cfarm-bootstrap/-/tree/master/files/contributor_keys), then you have access to all of these boxes:

## Updating

The setup is automated using an [Ansible Playbook](https://gitlab.com/eighthave/fdroid-bootstrap-buildserver).  Anyone with root access to the contributor boxes can run the automation.  It is meant to be safe to run repeatedly.  There are is one option to force-rebuild the _buildserver_ VM instance:

* `ansible-playbook --limit contributor_box provision.yml`
* `ansible-playbook --limit contributor_box --extra-vars "force_run_makebuildserver=true" provision.yml`

## gcc147

```config
Host gcc147.osuosl.org
  Port 45026
  User tunnel


Host fdroid-buildserver-gcc147-bubu
  User fdroid
  Hostname 192.168.103.20
  HostKeyAlias fdroid-buildserver-gcc147-bubu
  ProxyJump tunnel@gcc147.osuosl.org

Host fdroid-buildserver-gcc147-jspricke
  User fdroid
  Hostname 192.168.102.20
  HostKeyAlias fdroid-buildserver-gcc147-jspricke
  ProxyJump tunnel@gcc147.osuosl.org

Host fdroid-buildserver-gcc147-licaon
  User fdroid
  Hostname 192.168.100.10
  HostKeyAlias fdroid-buildserver-gcc147-licaon
  ProxyJump tunnel@gcc147.osuosl.org

Host fdroid-buildserver-gcc147-uniqx
  User fdroid
  Hostname 192.168.101.20
  HostKeyAlias fdroid-buildserver-gcc147-uniqx
  ProxyJump tunnel@gcc147.osuosl.org

```

## gcc148

```config
Host gcc148.osuosl.org
  Port 45027
  User tunnel

Host fdroid-buildserver-gcc148-bubu
  User fdroid
  Hostname 192.168.103.20
  HostKeyAlias fdroid-buildserver-gcc148-bubu
  ProxyJump tunnel@gcc148.osuosl.org

Host fdroid-buildserver-gcc148-jspricke
  User fdroid
  Hostname 192.168.102.20
  HostKeyAlias fdroid-buildserver-gcc148-jspricke
  ProxyJump tunnel@gcc148.osuosl.org

Host fdroid-buildserver-gcc148-licaon
  User fdroid
  Hostname 192.168.100.10
  HostKeyAlias fdroid-buildserver-gcc148-licaon
  ProxyJump tunnel@gcc148.osuosl.org

Host fdroid-buildserver-gcc148-uniqx
  User fdroid
  Hostname 192.168.101.20
  HostKeyAlias fdroid-buildserver-gcc148-uniqx
  ProxyJump tunnel@gcc148.osuosl.org

```

These machines are hosted at OSUOSL as part of the [GCC Farm Project](https://cfarm.tetaneutral.net/machines/list/).

## Azure

We got some short term credits from Azure.  These will disappear soon.  You can ssh to _root_ and _fdroid_.  This is what should go in your _~/.ssh/config_:

```config
Host fdroid-buildserver-azure-1000
  User fdroid
  Hostname fdroid-fdroiddata-test317133756000.eastus.cloudapp.azure.com
  Port 51243
```

## OSUOSL OpenStack

OSUOSL has given F-Droid an allocation on their [OpenStack setup](https://osuosl.org/services/hosting/details/#openstack):

* 10 instances
* 64 VPUs
* 200GB RAM
* 7.8TB volume storage
* 50 floating IP addresses
