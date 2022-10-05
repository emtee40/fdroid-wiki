#  When to delete an app -- and when to move it to archive?

1. *Remove* an app only if it violates the inclusion policy. If we erroneously archive something it's easier to take it out from the archive than rebuild.
1. *Remove* entirely if it has only a single build which also is disabled, and the app is no longer maintained upstream for 2+ years already (or even its source is gone).
1. *Archive* it if it can't be useful for anyone (e.g. this is a client to a network service that shut down).
1. *Archive* it if it's abandoned, but there's a successor ~~from the same author~~ (this happens all the time, sadly) in F-Droid already.
1. In other cases act on individual basis.

Process more or less from https://gitlab.com/fdroid/fdroiddata/-/issues/1479