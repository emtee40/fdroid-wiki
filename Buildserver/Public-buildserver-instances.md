# Get access

There are a number of _buildserver_ instances which community members can get access to.  You do not need to be a core contributor to get access. You may ask @jspricke @TheLastProject @uniqx or anyone else who has access to the machines, on [Matrix](https://matrix.to/#/#fdroid-dev:f-droid.org).

If you have any kind of status, e.g. "Reporter", in the F-Droid group or any project on https://gitlab.com/fdroid, you automatically have access to these instances.

Please note that this is not "the buildserver". These machines are not used to actually publish anything to f-droid.org and they do not have access to do so. They simply exist for testing and experimentation purposes.


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


## APK Playpen (<tt>fdroid1.osuosl.org</tt>)

This machine has large collections of APKs to work on.  This is useful for testing `fdroid update` for binary repos, APK signatures for reproducible builds, and feature extraction for _issuebot_.

* bare metal server
* MegaRAID SAS 2008 with Dell PERC H310 6TB
* Intel Xeon CPU E5-2665 @ 2.40GHz
* 126GB RAM


## Contributor _buildserver_ instances (<tt>fdroid2.osuosl.org</tt>)

These are shared instances, so please be careful in case someone else is using it also.  You can use `who` or `w` to see if anyone is currently logged in.  We coordinate on #fdroid-dev.

* bare metal server
* MegaRAID SAS 2008 with Dell PERC H310 6TB
* Intel Xeon CPU E5-2665 @ 2.40GHz
* 126GB RAM

```config
Host fdroid2.osuosl.org
  User tunnel

Host fdroid-buildserver-osuosl-earth
  User fdroid
  Hostname 192.168.100.10
  HostKeyAlias fdroid-buildserver-osuosl-earth
  ProxyJump fdroid2.osuosl.org

Host fdroid-buildserver-osuosl-wind
  User fdroid
  Hostname 192.168.101.20
  HostKeyAlias fdroid-buildserver-osuosl-wind
  ProxyJump fdroid2.osuosl.org

Host fdroid-buildserver-osuosl-fire
  User fdroid
  Hostname 192.168.102.20
  HostKeyAlias fdroid-buildserver-osuosl-fire
  ProxyJump fdroid2.osuosl.org

Host fdroid-buildserver-osuosl-water
  User fdroid
  Hostname 192.168.103.20
  HostKeyAlias fdroid-buildserver-osuosl-water
  ProxyJump fdroid2.osuosl.org
```

Do specify your `IdentityFile` in config for both the host and the tunnel server.

<details><summary>SSH host key fingerprints</summary>

* fdroid2.osuosl.org
  * `SHA256:usFlA1O6SpF14HlHqK/ElOlY3KyPi9qPGFAcwHUHApU (ECDSA)`
  * `SHA256:KNMdo6+OqVtxqx8QBm6fGmSG11hsYKS7/lOR88XwojA (ED25519)`
  * `SHA256:fdCJc6uK5O5HYto0jlVuAGN2UsJarc5BO+WTAdwioJk (RSA)`
* buildserver_earth
  * `SHA256:EJO5oTxBm6D16sqDh3Wk+AqiMTy+jS3BI9DIk8QzXd8 (ECDSA)`
  * `SHA256:WAYEzsWrNDsoV+JGjg8x2Kz/zK6wWAhi8hD5ThGGcWk (ED25519)`
  * `SHA256:MTYw+Pxvx8TBtbiu4MPA8vLdifZEC73tg71uu76TE0U (RSA)`
* buildserver_fire
  * `SHA256:tqJ4L36qUDN5KO1/G1xIpnfXroOnUGomuNw1kwpaflk (ECDSA)`
  * `SHA256:YBLgX2IDRNARZ1ltUyUKGt7ITYIPoWMuBDq9rlUxgIc (ED25519)`
  * `SHA256:WfO1lVLxS4QhdqSt3tulQtnswyAB6ywDoNXvlZrZ83U (RSA)`
* buildserver_water
  * `SHA256:Yq/jQVOMEPKeL56aEgbdCHcdpufZxXj2uFRpPxusHFg (ECDSA)`
  * `SHA256:K5h8wv+xzTTgDdaBBqdBPNOOTYsAg30XvtIWUYS1zxM (ED25519)`
  * `SHA256:BLsOntPLYKO9pZtxmZwej4FY+WJl5OVstYyyZjbqC5U (RSA)`
* buildserver_wind
  * `SHA256:g3BF6SwUOjm2hr5YbRF9C6Qt/79c3XocPmYoEnTBRLQ (ECDSA)`
  * `SHA256:iNADpiryyqZvNxvknmyiIoYk9xFIQuDQ0F7EaLuBVx4 (ED25519)`
  * `SHA256:41zoyzGDqnbVLPcjvDw68ARUFXBm+AQ9ocjmvgzLokw (RSA)`

</details>

# Temporary instances

If your SSH public key is listed [here](https://gitlab.com/fdroid/fdroid-cfarm-bootstrap/-/tree/master/files/contributor_keys), then you have access to all of these boxes:

## Updating

The setup is automated using an [Ansible Playbook](https://gitlab.com/fdroid/fdroid-bootstrap-buildserver).  Anyone with root access to the contributor boxes can run the automation.  It is meant to be safe to run repeatedly.  There are is one option to force-rebuild the _buildserver_ VM instance:

* `ansible-playbook --limit contributor_box provision.yml`
* `ansible-playbook --limit contributor_box --extra-vars "force_run_makebuildserver=true" provision.yml`

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
