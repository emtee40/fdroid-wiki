# Get access

There are a number of _buildserver_ instances which community members can get access to.  You do not need to be a core contributor to get access. You may ask @jspricke @TheLastProject @uniqx or anyone else who has access to the machines, on [Matrix](https://matrix.to/#/#fdroid-dev:f-droid.org).

# Setup

Once you have been granted access, you will need to copy the relevant config from below and put it into _~/.ssh/config_.

## fdroid-buildserver-gcc136-public

```config
Host fdroidGcc136
  Hostname gcc136.osuosl.org
  Port 45015
  User tunnel

Host fdroid-buildserver-gcc136-public
  User fdroid
  Hostname 192.168.78.135
  ProxyJump fdroidGcc136
  ServerAliveInterval 30
  ServerAliveCountMax 5
```

This aims to be as close as possible to the production setup.  There is an Ubuntu/focal chroot included, since `fdroid publish` is run on Ubuntu/focal.  To use that chroot, just run: `schroot`.


## gcc147

```config
Host fdroidGcc147
  Hostname gcc147.osuosl.org
  Port 45026
  User tunnel

Host fdroid-buildserver-gcc147
  User fdroid
  Hostname 192.168.111.129
  ProxyJump fdroidGcc147
  ServerAliveInterval 30
  ServerAliveCountMax 5

```

These machines are hosted at OSUOSL as part of the [GCC Farm Project](https://cfarm.tetaneutral.net/machines/list/).


# Temporary instances

We got some short term credits from Azure.  These will disappear soon.  You can ssh to _root_ and _fdroid_.  This is what should go in your _~/.ssh/config_:

```config
Host fdroid-buildserver-azure-8core
  User fdroid
  Hostname 51.107.209.125

Host fdroid-buildserver-azure-1
  User fdroid
  Hostname 20.25.97.57

Host fdroid-buildserver-azure-2
  User fdroid
  Hostname 40.87.85.251

Host fdroid-buildserver-azure-buster
  User fdroid
  Hostname 20.231.66.66
```