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

## Tuning

- https://docs.gitlab.com/ee/ci/large_repositories/#docker-executor-example
- https://docs.gitlab.com/ee/ci/large_repositories/index.html#our-gitlab-ciyml

## Configuration

Based on https://gitlab.com/TyIsI/gitlab-runner-docker-compose.

`docker-compose.override.yml`

```yaml
version: "3.5"

services:
  dind:
    image: docker:dind-rootless

  watchtower:
    image: containrrr/watchtower
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```

`/etc/gitlab-runner/config.toml`

```toml
concurrent = 10
check_interval = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "docker-runner"
  url = "https://gitlab.com/"
  token = "TOKEN"
  executor = "docker"
  builds_dir = "/builds"
  cache_dir = "/cache"
  
  environment = [
    "GIT_DEPTH=1000",
    "GIT_CLONE_PATH=$CI_BUILDS_DIR/$CI_CONCURRENT_ID/$CI_PROJECT_NAME"
  ]

  [runners.custom_build_dir]
    enabled = true
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
    [runners.cache.azure]

  [runners.docker]
    tls_verify = false
    image = "alpine:latest"
    privileged = true
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = [ "/var/cache/apt", "/cache", "/builds", "/opt/android-sdk" ]
    shm_size = 0
```