When a release asset is created from the GitLab web UI, the file can be attached to the release notes. A unique url to the file will be generated, like https://gitlab.com/user/repo/uploads/05ac12dcadcc22e6aa7b75cb3fc8f859/app-release.apk. Then you can add this linke as Release assets link. But the url won't be changed.

For reproducible build, F-Droid requires a url to the upstream binary, which should be stable so that F-Droid can get the url for every release. Therefore when the upstream binaries are hosted on GitLab release, a stable url is required.

GitLab supports [stable release assets link](https://docs.gitlab.com/ee/api/releases/links.html#create-a-release-link) but it can only be set via the api. First, you need to get the link to the file. Then you need to create a release from the tag. To use the api you need to generate a token in https://gitlab.com/-/profile/personal_access_tokens. Now you can create the stable assets link with curl:

```
curl --request POST \
  --data name="<your_assets_name>" \
  --data url="<your_binary_url>" \
  --data filepath="<your_file_path>" \
  --header "PRIVATE-TOKEN: <your_access_token>" \
  "https://gitlab.com/api/v4/projects/<your_project_id>/releases/<your_release_name>/assets/links"
```

The assets name and the file path can be set as you like. You can simply set the file path as "/app-release.apk" so that the url is https://gitlab.com/user/repo/-/releases/<release_name>/downloads/app-release.apk. The project id can be found on your repo main page.