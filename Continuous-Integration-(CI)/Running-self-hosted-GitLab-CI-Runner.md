When encountering GitLab's hosted shared CI runners' resource limits (e.g. disk space, memory), you may want to run a CI runner on your computer to rerun that job (or all jobs).

# How-to

1. Install [Docker](https://docs.docker.com/engine/install/) or [Podman](https://podman.io/getting-started/installation)
2. Start the gitlab-runner container

   ```shell
   # Docker
   docker run --name gitlab-runner -d --rm \
       -v gitlab-runner:/etc/gitlab-runner:Z \
       -v /var/run/docker.sock:/var/run/docker.sock:z \
       --security-opt label=disable \
       --security-opt seccomp=unconfined \
       --cap-add=NET_ADMIN,NET_RAW \
       gitlab/gitlab-runner:alpine

   # Or Podman
   systemctl --user enable --now podman
   podman run --name gitlab-runner -d --rm \
       -v gitlab-runner:/etc/gitlab-runner:Z \
       -v /run/user/1000/podman/podman.sock:/var/run/docker.sock:z
       --security-opt label=disable \
       --security-opt seccomp=unconfined \
       --cap-add=NET_ADMIN,NET_RAW \
       gitlab/gitlab-runner:alpine
   ```

3. Get the runner registration token and disable shared runners at <https://gitlab.com/[your_username]/fdroiddata/-/settings/ci_cd#js-runners-settings>
4. Register the runner

   ```shell
   GITLAB_REGISTERATION_TOKEN=<your_runner_registeration_token>
   # Docker
   docker exec gitlab-runner gitlab-runner register \
      --url https://gitlab.com/ \
      --executor docker \
      --docker-image registry.gitlab.com/fdroid/ci-images-client \
      --non-interactive \
      --registration-token ${GITLAB_REGISTERATION_TOKEN}

   # Or podman
   podman exec gitlab-runner gitlab-runner register \
      --url https://gitlab.com/ \
      --executor docker \
      --docker-image registry.gitlab.com/fdroid/ci-images-client \
      --non-interactive \
      --registration-token ${GITLAB_REGISTERATION_TOKEN}
   ```

5. Restart the container

   ```shell
   # Docker
   docker restart gitlab-runner

   # Or podman
   podman restart gitlab-runner
   ```

6. Run the job again
