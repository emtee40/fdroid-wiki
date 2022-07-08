Similar to the [instruction](<https://gitlab.com/fdroid/wiki/-/wikis/Continuous-Integration-(CI)/Running-self-hosted-GitLab-CI-Runner>) of creating self-hosted GitLab runners, we host shared GitLab runners on Azure to accelerate contributors' job and workaround the limitations of [GitLab.com SaaS runners](https://docs.gitlab.com/ee/ci/runners/), thanks to [Azure's Open Source sponsorship](https://gitlab.com/fdroid/admin/-/issues/296).

Note that it's different from https://gitlab.com/fdroid/fdroid-gitlab-runner used for automation.

## Application

All contributors to F-Droid are welcome to use these shared runners to save time when contributing. You just need to PM [your runner registration token](https://docs.gitlab.com/runner/register/#requirements) to [@proletarius101 on Matrix.org](https://matrix.to/#/@proletarius101:matrix.org).

## Specification

| Parameter        | Specification            |
| ---------------- | ------------------------ |
| Operating System | Linux (rhel 9.0)         |
| Size             | Standard B4ms            |
| vCPUs            | 4                        |
| RAM              | 16 GiB                   |
| Disk             | Standard HDD LRS 400 GiB |

## Limitations

- The runner has no access to `sysctl` for security. Add '|| true' to allow it to fail, and then ask [@proletarius101](https://matrix.to/#/@proletarius101:matrix.org) to add that setting in system level.
