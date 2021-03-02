
There are many servers required to run F-Droid, this is for notes about how they are setup and run.  The preferred OS is Debian/stable, with Debian/oldstable also in use.


## Maintenance log
There is a maintenance log, it is a private `@contributors` thread in the forum. It is a place to post simple updates on maintenance events.  It should not be used to post things like private keys, etc.
<https://forum.f-droid.org/t/maintenance-log>


## Ansible

As much as possible, the F-Droid servers are built, deployed and maintained using Ansible.  The relevant projects in GitLab are tagged with `Ansible`, for example:

https://gitlab.com/explore?utf8=%E2%9C%93&name=fdroid&sort=latest_activity_desc&tag=ansible


### Vault setup

Some of the Ansible projects use the Ansible Vault feature to manage information
that should not be public.  The F-Droid setup uses GPG keys to control access to
the Vault password.  This setup is to keep information from public view, like
who has access to root.  It should not be used to commit to git any private keys
and things that need strong security.  The git history is public, so compromise
of one admin's GPG would lead to compromise of that private key, etc.


## _f-droid.org_

_f-droid.org_ is run from a collection of 3-4 bare metal servers and one VM.  There is one bare metal server that is the canonical web server.  There are two or three bare metal servers that serve as caching front-end servers for the canonical web server. The [website deployserver](https://gitlab.com/fdroid/fdroid-deployserver/) is a VM that generates the [website](https://gitlab.com/fdroid/fdroid-website/) and _rsyncs_ it to the canonical server.  The _buildserver_ host is a bare metal server that generates the repo and runs builds in the [guest](https://gitlab.com/fdroid/fdroidserver/tree/2.0/buildserver). 