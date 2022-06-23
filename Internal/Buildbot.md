We aim to replace as much of our code as possible using Buildbot.  This is a place to work out the architecture and stages of implementation.  The whole process the _buildserver_ is quite different than the Buildbot workflow.  Buildbot is very well developed and maintained, so it will save us a lot of work in the long run to make the switch.

The easiest place to get started with this is to read [Buildbot Concepts](https://docs.buildbot.net/current/manual/concepts.html).


- https://flathub.org/builds/#/
- https://docs.buildbot.net/latest/manual/configuration/www.html#reverse-proxy-configuration


### Implementation Stages

1. Get builds running in existing _buildserver_ VM by making a `Worker` per _buildserver_ instance that handles starting/stoping the VM, then running `fdroid build --server` for one single build.
2. Make `fdroid build` just take the command line args and generate the `Changes` and submit them to the `Scheduler` via `buildbot sendchange`.
3. Manage _buildserver_ VM start/reset from Buildbot workflow, each `Build` should run in a fresh VM instance.
4. Support multiple _buildserver_ VM instances while still running `fdroid build`.
5. Support `fdroid update` running in parallel with `fdroid build`
6. Support `fdroid deploy` running in parallel with `fdroid build`
7. Support `fdroid publish` running in parallel with `fdroid build`

`checkupdates` can probably run as its own instance of Buildbot.

# build

The build _worker_ should use a [`LatentWorker`](https://docs.buildbot.net/latest/manual/configuration/workers.html#latent-workers), either [`LibvirtWorker`](https://docs.buildbot.net/latest/manual/configuration/workers-libvirt.html) or we implement a `VagrantLatentWorker`.  At this point, I think `VagrantLatentWorker` will work best.

### examples

OpenVPN uses libvirt with Buildbot:
* https://github.com/OpenVPN/openvpn-buildbot
* https://github.com/OpenVPN/openvpn-vagrant

# verification

pip3 install         'buildbot-console-view==2.10.1'         'buildbot-grid-view==2.10.1'         'buildbot-waterfall-view==2.10.1'         'buildbot-www==2.10.1' buildbot-worker==2.10.1
Collecting buildbot-console-view==2.10.1



# checkupdates

* The `fdroid checkupdates` runs are being implemented using a [`DockerLatentWorker`](https://docs.buildbot.net/3.5.0/manual/configuration/workers-docker.html).  

* Each active app should get a `ChangeSource` set up on its source repo.  Then when that detects new commits, it should trigger a `fdroid checkupdates` run for that app.

* There should be a new job that uses a Git watcher on fdroid/fdroiddata, then whenever there are new commits, it add/updates/deletes all the relevant `ChangeSources` for all the active apps.