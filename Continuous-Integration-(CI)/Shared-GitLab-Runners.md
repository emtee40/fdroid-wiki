
F-Droid relies heavily on GitLab CI, and that means we need Runners.  We use the gitlab.com shared runners, as well as a number that are maintained by other organizations.  These runners need to be oriented towards stability.  It is easy to cause issues that cause jobs to fail mysteriously.  If the dev doesn't know it was caused by runner instability, it can easily cause someone to spend hours trying to track down a problem.

`quay.io/gitlab/gitlab-runner-docker-cleanup` is always running on these runners to clean up Docker cruft.


## Shared GitLab Runners on Azure

When there is time available donated by Azure, then there can be
[Shared GitLab Runners on Azure](Continuous-Integration-(CI)/Shared-GitLab-Runners-on-Azure).  This is maintained by @proletarius101.


## OSUOSL runners

OSUOSL maintains some runners that are shared across a number of projects.  Maintained by [OSUOSL](https://osuosl.org/contact/): support@osuosl.org 

* [ci-aarch64-runner1-osuosl-docker-fdroid](https://gitlab.com/groups/fdroid/-/runners/15012154)
* [ci-x64-runner1-osuosl-docker-fdroid](https://gitlab.com/groups/fdroid/-/runners/15012034)
* [ci-x64-runner2-osuosl-docker-fdroid](https://gitlab.com/groups/fdroid/-/runners/15012150)

https://osuosl.org/services/hosting/details/#ci-cd-hardware-resources


## `pep-runner`

https://gitlab.com/groups/fdroid/-/runners/534721#/

@jspricke maintains this instance, which is donated by pep.security.  It is currently devoted to running _checkupdates_.

* 4 CPU
* 8GB RAM
* 100GB disk


## `fdroid-gitlab-runner-osuosl`

https://gitlab.com/groups/fdroid/-/runners/535145

@eighthave manages this instance in a VM provided by OSUOSL.

* 8 CPU
* 16GB RAM
* 80GB disk
* KVM shared to containers
