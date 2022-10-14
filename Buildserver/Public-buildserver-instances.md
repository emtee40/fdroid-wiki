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

## gcc147

```config
Host gcc147.osuosl.org
  Port 45026
  User tunnel

Host fdroid-buildserver-gcc137-bubu
  User fdroid
  Hostname 192.168.103.20
  ProxyJump tunnel@gcc147.osuosl.org
  ServerAliveInterval 30
  ServerAliveCountMax 5

Host fdroid-buildserver-gcc137-jspricke
  User fdroid
  Hostname 192.168.102.20
  ProxyJump tunnel@gcc147.osuosl.org
  ServerAliveInterval 30
  ServerAliveCountMax 5

Host fdroid-buildserver-gcc137-licaon
  User fdroid
  Hostname 192.168.100.20
  ProxyJump tunnel@gcc147.osuosl.org
  ServerAliveInterval 30
  ServerAliveCountMax 5

Host fdroid-buildserver-gcc137-uniqx
  User fdroid
  Hostname 192.168.101.20
  ProxyJump tunnel@gcc147.osuosl.org
  ServerAliveInterval 30
  ServerAliveCountMax 5

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
