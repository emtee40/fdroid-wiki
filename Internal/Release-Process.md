
For each piece of the F-Droid ecosystem, there is a specific release process.  They generally follow the same patterns, but all have unique steps.  The official signing keys are listed in [Release Channels and Signing Keys](https://f-droid.org/docs/Release_Channels_and_Signing_Keys).


# <tt>fdroidclient</tt>

0. Commit changes in Weblate (e.g. `wlc commit`)
1. Fetch in latest commits from Weblate
1. Run `./tools/pick-complete-translations.py`
2. run `./tools/check-format-strings.py` and
   `./tools/remove-unused-and-blank-translations.py` and commit
   changes
3. rebase the _weblate_ branch on the latest commits in _master_ of
   <https://gitlab.com/fdroid/fdroidclient> and fix any conflicts
4. push local _weblate_ branch to your fork create a merge request, and tag it with the _localization_ label
5. Make sure all tests pass before merging, including all _android-22_ emulator tests.  Run all the emulator tests locally, many of them do not run in GitLab CI.
6. once the _weblate_ branch is merged, reset the git repo in <https://hosted.weblate.org/projects/f-droid/f-droid/#repository>
7. Set _versionCode_ in _app/build.gradle_ (see [Versioning](#versioning)).
8. Add new entry to _CHANGELOG.md_.
9. For alpha builds, edit the _metadata/en-US/changelogs/_ file for the upcoming
   release so the translators can work on it (e.g. for 1.12-alpha1/`1012001`, edit
   _metadata/en-US/changelogs/1012050.txt_).
10. make a local branch for the final release, then run
    `./tools/trim-incomplete-translations-for-release.py`
	to remove any incomplete translations from the final while leaving
	them in _master_
10. add git signed tag named after the exact version name (no
   preceeding __v__)
11. add new `Build:` entry to
    [metadata file](https://gitlab.com/fdroid/fdroiddata/blob/master/metadata/org.fdroid.fdroid.yml)

### major release

If it is a major release, there are a few more steps.

1. temporarily remove the _iw/in_ links: `find app/src/main/res/ -type l -delete`
2. In Android Studio, go to _Analyze_ -> _Inspect Code_
3. After running on the whole project, go to _Android Lint:
   Performance_, and click on _Unused Resources_, then click the
   _Remove All Unused Resources_ button.  Only commit changes to
   _strings.xml_, **not** _styles.xml_ or other files.
4. Then it is time to create a new stable branch (e.g. _stable-v1.0_
   or _stable-v1.1_) should be created first.  The incomplete
   translations should be removed using
   `./tools/trim-incomplete-translations-for-release.py`.  This has to
   happen in the stable branch only, since it will remove the
   incomplete translations.  If the incomplete translations are removed
   from _master_, then Weblate will sync with that, and remove them
   there as well.

### Versioning

Each stable version follows the `X.Y` pattern. Hotfix releases - i.e. when a stable has an important bug that needs immediate fixing - will follow the `X.Y.Z` pattern.

Before each stable release, a number of alpha releases will be released. They will follow the pattern `X.Y-alphaN`, where `N` is the current alpha number. These will usually include changes and new features that have not been tested enough for a stable release, so use at your own risk. Testers and reporters are very welcome.

The version codes use a number of digits per each of these keys: `XXXYYYZNN`. So for example, 1.3.1 would be `1003150` and 0.95-alpha13 would be `95013` (leading zeros are omitted).

Note that we use a trailing `50` for actual stable releases, so alphas are limited to `-alpha49`.

This is an example of a release process for **0.95**:

* We are currently at stable **0.94**
* **0.95-alpha1** is released
* **0.95-alpha2** is released
* **0.95-alpha3** is released
* `stable-v0.95` is branched and frozen
* **0.95** is released
* A bug is reported on the stable release and fixed
* **0.95.1** is released with only that fix

As soon as a stable is tagged, master will move on to `-alpha0` on the next version. This is a temporary measure - until `-alpha1` is released - so that moving from stable to master doesn't require a downgrade. `-alpha0` versions will not be tagged nor released.



# <tt>fdroidserver</tt>

1. Make sure [all of the tests](https://gitlab.com/fdroid/fdroidserver/#build-status)
   listed on top of the README are passing.
2. pull in latest commits from Weblate into a local branch called
   _weblate_
3. rebase the _weblate_ branch on the latest commits in _master_ of
   <https://gitlab.com/fdroid/fdroidserver> and fix any conflicts
4. push local _weblate_ branch to your fork create a merge request,
   and tag it with the _localization_ label
5. make sure all tests pass before merging
6. Update official release languages using
   `locale/pick-complete-translations.py`, then commit changes.
7. set `version=''` in _setup.py_
8. add git signed tag named after the exact version name (no
   preceeding __v__), using the exact same string as `version=''`
9. upload to pypi.python.org using `python3 setup.py release`
10. update [Debian package](https://salsa.debian.org/python-team/packages/fdroidserver)
11. upload to [F-Droid PPA](https://launchpad.net/~fdroid/+archive/ubuntu/fdroidserver)
12. Update translation template using: `make -C locale update`. Commit updates
    to _locale/fdroidserver.pot_ and push.

### Stable release branches

There are some very conservative stable release branches.  These are for targeted bugfixes only, and are setup to only have the changes that would allow the releases to get into Debian/stable and Ubuntu/LTS releases.

* 2.0.x targets Debian/bullseye and Ubuntu/hirsute
* 1.1.x targets Debian/buster and Ubuntu/focal


# <tt>privileged-extension</tt>

1. pull in latest commits from Weblate into a local branch called
   _weblate_
2. run `./tools/check-format-strings.py` and
   `./tools/remove-unused-and-blank-translations.py` and commit
   changes
3. rebase the _weblate_ branch on the latest commits in _master_ of
   <https://gitlab.com/fdroid/privileged-extension> and fix any conflicts
4. push local _weblate_ branch to your fork create a merge request,
   and tag it with the _localization_ label
5. make sure all tests pass before merging
6. once the _weblate_ branch is merged, reset the git repo in
   <https://hosted.weblate.org/projects/f-droid/privileged-extension/#repository>
7. set _versionCode_ in _app/src/main/AndroidManifest.xml_
8. add new entry in _metadata/en-US/changelogs/_
9. copy that new entry to _CHANGELOG.md_
10. add git signed tag named after the exact version name (no
   preceeding __v__)
11. If the OTA update ZIP should be updated, add new `Build:` entry to
    [metadata file](https://gitlab.com/fdroid/fdroiddata/blob/master/metadata/org.fdroid.fdroid.privileged.ota.txt)
12. The APK should auto-update based on the signing tag.  Double-check
    that in that
    [metadata file](https://gitlab.com/fdroid/fdroiddata/blob/master/metadata/org.fdroid.fdroid.privileged.txt)


# <tt>fdroid-website</tt>

1. Review the [failing checks](https://hosted.weblate.org/checks/?project=f-droid&component=website) in Weblate, and try to fix any as possible in the Weblate UI (not locally!).
2. Commit all work [in Weblate](https://hosted.weblate.org/projects/f-droid/website/#repository)
   or with `wlc commit`.
3. Fetch the latest commits from Weblate into a git remote called _weblate_.
4. Reset your local _master_ branch to the main repo's _master_, e.g. `git checkout -B master upstream/master` (careful, this will remove commits in _master_ that are not in _upstream/master_).
5. Run `./tools/pick-complete-translations.py`.
6. Change to new _merge_weblate_ branch and run all checker scripts (`./tools/check-*.py`), make sure they all pass, then commit those changes.
7. Push _merge_weblate_ branch to your fork and create a merge request. Tag it with the _localization_ label.
9. Make sure all tests pass before merging.
10. Add git signed tag named after the exact version name (no preceeding __v__)
8. `git fetch weblate` and check that Weblate rebased on the latest
   commits, otherwise, there is a merge conflict that needs to be
   fixed before Weblate will sync again.
9. Run `./tools/post-release-translation-merge.sh` to make all changes 
   available for translation, and commit to _master_.  Only commit the changed
   translation files (_po/*.po*_), do not commit new translation files
   here (new languages are added via Weblate).
10. Push release tag and new translation commits with a GPG signature in the [trusted keyring](https://gitlab.com/fdroid/fdroid-deployserver/-/blob/aa4ffff3b686259d28de9bcdd75733abbd2e93b4/provision.yml#L396).


# <tt>jekyll-fdroid</tt>

1. Make sure all the tests are passing
2. set `s.version` and `s.date` in _jekyll-fdroid.gemspec_
3. Make signed tag matching version number (no preceeding __v__)
4. Update _fdroid-website_ to use this release by setting both
   `revision:` and the version number in `specs: jekyll-fdroid
   (1.0.1)` in _Gemfile.lock_


# Repomaker

1. Review and merge any merge requests from Weblate on
   <https://gitlab.com/fdroid/repomaker/merge_requests>.
2. Pull in latest commits, including any merged Weblate commits
3. Make sure all tests on GitLab CI pass before merging.
4. Set `VERSION =''` in _repomaker/__init__.py_.
5. Add git signed tag named after the exact version name (no
   preceeding __v__), using the exact same string as `VERSION =''`.
6. Make sure the local git repo is fully clean, so random files are
   not included in the release tarball, e.g. `git clean -fdx`.
7. Make sure all dependencies are installed, like in a _venv_.
8. Run `./pre-release.sh`
9. Build a dist tarball using `./setup.py release`.
10. Upload to pypi.python.org.
11. Update <https://gitlab.com/fdroid/fdroid-repomaker-flatpak>
    and make sure CI passes.
12. Push changes to official repo on GitHub:
	<https://github.com/flathub/org.fdroid.Repomaker>
