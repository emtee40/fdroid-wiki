
There are certain rare cases that require a emergency, manual release process to get a new release out as soon as  possible.  This includes security vulnerabilities in essential and/or widely used apps.  This is documentation for how to do that.  Keep in mind: this is a labor-intensive procedure that can require volunteers spend hours of time.  We cannot guarantee that we will be able to handle all cases via this procedure.

## Start the build development as soon as possible

With security issues, there is often a coordinated release with a [fixed time](https://matrix.org/blog/2022/09/23/pre-disclosure-upcoming-critical-security-release-of-matrix-sd-ks-and-clients/) in the future made for the release.  In this case, the work to get the build setup should start as soon as possible.  All of the non-sensitive bits can be pushed to a merge request in https://gitlab.com/fdroid/fdroiddata so that the CI can run and progress can be tracked.  For example:

* https://gitlab.com/fdroid/fdroiddata/-/merge_requests/11812
* https://gitlab.com/fdroid/fdroiddata/-/merge_requests/11813

## Use patches for the sensitive bits

Patch files are a common way to share source code.  The sensitive parts of the coordinated release should be in a patch file.  If this is a patch on an existing release, then the patch should include all the changes needed to update `versionCode` and `versionName`.   This patch must apply cleanly to a well documented commit in the source repo.  That means these work: `git apply fix-vuln.patch` or `patch -p1 < fix-vuln.patch`.  If the patch requires options like `--ignore-space-change` or `--ignore-whitespace`, it will not work.  Refresh the patch against the exact commit, then `git format-patch` will generate a clean patch.

Here's an example session for refreshing a patch:

```bash
cd build/im.vector.app
git reset --hard v1.4.36
git clean -fdx
git apply --ignore-space-change --ignore-whitespace ~/element_android_sec_1.4.37.patch
git status  # see any new files
git add path/to/new.file
git commit -a -m "element_android_sec_1.4.36-1.patch"
git format-patch -n1
ls -l 0001-element_android_sec_1.4.36-1.patch.patch 
```
