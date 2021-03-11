We aim to replace as much of our code as possible using _buildbot_.  This is a place to work out the architecture and stages of implementation.  The whole process the _buildserver_ is quite different than the _buildbot_ workflow.  _buildbot_ is very well developed and maintained, so it will save us a lot of work in the long run to make the switch.

The easiest place to get started with this is to read [Buildbot Concepts](https://docs.buildbot.net/current/manual/concepts.html)

## Architecture

This is the long run goal for the _buildbot_ system.  It will not look like this until the implementation is complete.  This is the final destination.

* `changes.GitPoller` looking for new commits to        https://gitlab.com/fdroid/fdroiddata, generating `Changes` to send to the `Scheduler`

* completed builds are marked by an APK being present in _unsigned/_, _repo/_, or _archive_ named using the F-Droid standard pattern: `(unsigned|repo|archive)/<packageName>_<versionCode>.apk` e.g. _unsigned/org.fdroid.fdroid_100000.apk_

* `Scheduler` checks whether completed build is present before turning the `Change` into a `BuildRequest`.


## Implementation Stages

1. Get builds running in existing _buildserver_ VM using `fdroid build --on-server`.
2. Generate 
4. Support multiple _buildserver_ VM instances while still running `fdroid build`.
5. Support `fdroid update` running in parallel with `fdroid build`
6. Support `fdroid deploy` running in parallel with `fdroid build`
7. Support `fdroid publish` running in parallel with `fdroid build`

`checkupdates` can probably run as its own instance of Buildbot.