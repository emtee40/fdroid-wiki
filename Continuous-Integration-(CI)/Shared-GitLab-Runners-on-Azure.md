Similar to the [instruction](https://gitlab.com/fdroid/wiki/-/wikis/Continuous-Integration-(CI)/Running-self-hosted-GitLab-CI-Runner) of creating self-hosted GitLab runners, we host shared GitLab runners on Azure to accelerate contributors' job and workaround the limitations of [GitLab.com SaaS runners](https://docs.gitlab.com/ee/ci/runners/), thanks to [Azure's Open Source sponsorship](https://gitlab.com/fdroid/admin/-/issues/296 "apply for Azure credits to open source projects").

Note that it's different from https://gitlab.com/fdroid/fdroid-gitlab-runner used for automation.

## Application

All contributors to F-Droid are welcome to use these shared runners to save time when contributing. You just need to PM [your runner registration token](https://docs.gitlab.com/runner/register/#requirements) to [@proletarius101 on ](https://matrix.to/#/@proletarius101:matrix.org)Matrix.org.

## Usage

By default, all CI pipelines of Merge Requests to [fdroid/fdroiddata](https://gitlab.com/fdroid/fdroiddata) are [Merge Request Pipelines](https://docs.gitlab.com/ee/ci/pipelines/merge_request_pipelines.html#run-pipelines-in-the-parent-project) running on the F-droid infrastructure. To leverage the shared runners on Azure:

* Add a CI/CD variable <span dir="">`CI_ENABLE_MERGE_REQUEST_PIPELINES`</span> with value `false` to your fork;
* Uncheck the `Protect variable` option for that variable to make it available for your non-main branch.

## Specification

| Parameter | Specification |
|-----------|---------------|
| Operating System | Linux (rhel 9.0) |
| Size | Standard_F8s_v2 |
| vCPUs | 8 |
| RAM | 16 GiB |
| Disk | Premium SSD P10 128GB |

## Limitations

- The runner has no access to `sysctl` for security. Add '|| true' to allow it to fail, and then ask [@proletarius101](https://matrix.to/#/@proletarius101:matrix.org) to add that setting in system level.

## Tuning

- https://docs.gitlab.com/ee/ci/large_repositories/#docker-executor-example
- https://docs.gitlab.com/ee/ci/large_repositories/index.html#our-gitlab-ciyml

## Configuration

Based on https://gitlab.com/TyIsI/gitlab-runner-docker-compose.

`docker-compose.override.yml`

https://gitlab.com/secure-system/fdroid/fdroid-azure-gitlab-runner

`/etc/gitlab-runner/config.toml`

```toml
concurrent = 12
check_interval = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "docker-runner"
  url = "https://gitlab.com/"
  token = "***"
  executor = "docker"
  builds_dir = "/builds"
  cache_dir = "/cache"
  environment = [
    "GIT_CLONE_PATH=$CI_BUILDS_DIR/$CI_CONCURRENT_ID/$CI_PROJECT_NAME"
  ]

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
    volumes = [ "/datadrive/fdroid-azure-gitlab-runner/data/builds:/builds", "/cache:/cache" ]
    shm_size = 300000
    dns = ["1.1.1.1", "1.0.0.0"]
```