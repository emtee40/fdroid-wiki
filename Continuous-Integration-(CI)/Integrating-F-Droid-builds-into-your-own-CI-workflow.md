If you are planning to publish your app on F-Droid, or already have done so, and you have a CI pipeline set up for your app, here is how you can integrate an F-Droid build. Doing so will help you ensure that your new commit does not inadvertently break the F-Droid build process.

[[_TOC_]]

# Prerequisites

We assume you have a CI pipeline set up for your app’s repository, and are somewhat familiar with GitLab CI, as well with the CI environment you use for your app. We also assume you already have an F-Droid build recipe for your app, or know how to create one.

# Steps

Essentially, what you need to do is to drop an F-Droid build recipe for your app into the source code tree, run the F-Droid build tool on the latest release of your code, using that recipe, ad finally configure your CI pipeline to run these steps for each commit.

## Provide a build recipe

If your app is already on F-Droid, use that build recipe. If not, create one. Either way, the build recipe needs to reside in the root folder of the source tree and be named `.fdroid.yml`.

Although this build recipe will be placed in the source tree, it still needs to specify `RepoType` and `Repo` – otherwise, F-Droid will exit with an error, telling you there are no apps to process.

We need to modify the build recipe so that F-Droid will always build from the latest commit of the current branch. For this, the `Builds` section needs to have one single entry, with `HEAD` as the commit. For example:

```yaml
Builds:
  - versionName: 0.1.0
    versionCode: 100
    commit: HEAD
    gradle:
      - yes
```

You should only have this one entry in your Build section—this tells F-Droid to build only one version, namely the last commit of the current branch. `versionName` and `versionCode` should match the versions in the latest commit of your app.

## Configure CI

We may not be able to cover each and every CI out there. However, if yours is not covered, use one of those described here and adapt it as needed—the commands to run are the same, and the steps should be similar in principle for most CI environments.

The CI job is similar to the `fdroid build` job from the [fdroiddata](https://gitlab.com/fdroid/fdroiddata) CI script, with a few exceptions:
* We skip the `git diff` loop and subsequent processing of `$CHANGED` to determine packages to build—we have only one recipe to build.
* For the same reason we run `fdroid build` only once, rather then in a loop for each package.
* We run `fdroid build` without any üackage names, causing it to build from `.fdroid.yml` in the root of the source tree.

### GitLab CI

By default, CI jobs are defined in `.gitlab-ci.yml` in the root directory of your source tree. This is configurable in GitLab—if you use a different file name, edit that file instead.

Add a new section to create a new job:

```gitlab-ci
fdroid build:
  image: registry.gitlab.com/fdroid/ci-images-client:latest
  allow_failure: true
  artifacts:
    # name: "${CI_PROJECT_PATH}_${CI_JOB_STAGE}_${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHA}"
    paths:
      - unsigned/
    when: always
    # expire_in: 1 month
  cache:
    key: "$CI_JOB_NAME"
    paths:
      - .gradle
  script:
    - test -d build || mkdir build
    - test -d fdroidserver || mkdir fdroidserver
    - git ls-remote https://gitlab.com/fdroid/fdroidserver.git master
    - curl --silent https://gitlab.com/fdroid/fdroidserver/-/archive/master/fdroidserver-master.tar.gz
          | tar -xz --directory=fdroidserver --strip-components=1
    - export PATH="`pwd`/fdroidserver:$PATH"
    - export PYTHONPATH="$CI_PROJECT_DIR/fdroidserver:$CI_PROJECT_DIR/fdroidserver/examples"
    - export PYTHONUNBUFFERED=true

    - bash fdroidserver/buildserver/setup-env-vars $ANDROID_HOME
    - adduser --disabled-password --gecos "" vagrant
    - ln -s $CI_PROJECT_DIR/fdroidserver /home/vagrant/fdroidserver
    - mkdir -p /vagrant/cache
    - wget -q https://services.gradle.org/distributions/gradle-5.6.2-bin.zip
          --output-document=/vagrant/cache/gradle-5.6.2-bin.zip
    - bash fdroidserver/buildserver/provision-gradle
    - bash fdroidserver/buildserver/provision-apt-get-install http://deb.debian.org/debian
    - source /etc/profile.d/bsenv.sh
    - apt-get dist-upgrade

    # install fdroidserver from git, with deps from Debian, until fdroidserver
    # is stable enough to include all the things needed here
    - apt-get install -t stretch-backports
         fdroidserver
         python3-asn1crypto
         python3-ruamel.yaml
         yamllint
    - apt-get purge fdroidserver

    - export GRADLE_USER_HOME=$PWD/.gradle
    # each `fdroid build --on-server` run expects sudo, then uninstalls it
    - set -x
    - apt-get install sudo
    - fdroid fetchsrclibs --verbose
    # this builds the latest version of the app from its source dir, using the build recipe in .fdroid.yml
    - fdroid build --verbose --on-server --no-tarball
```

And that’s it! Commit and push your changes, and wait for the pipeline to finish.

### CircleCI

By default the configuration is found in `.circleci/config.yml`.

In the `jobs` section, add another subsection like this:

```circle-ci
  build_fdroid:
    docker:
      - image: registry.gitlab.com/fdroid/ci-images-client:latest
    steps:
      - checkout
      - run:
          name: Build with F-Droid
          command: |
            test -d build || mkdir build
            test -d fdroidserver || mkdir fdroidserver
            git ls-remote https://gitlab.com/fdroid/fdroidserver.git master
            curl --silent https://gitlab.com/fdroid/fdroidserver/-/archive/master/fdroidserver-master.tar.gz | tar -xz --directory=fdroidserver --strip-components=1
            export PATH="`pwd`/fdroidserver:$PATH"
            export PYTHONPATH="$CI_PROJECT_DIR/fdroidserver:$CI_PROJECT_DIR/fdroidserver/examples"
            export PYTHONUNBUFFERED=true
            bash fdroidserver/buildserver/setup-env-vars $ANDROID_HOME
            adduser --disabled-password --gecos "" vagrant
            ln -s $CI_PROJECT_DIR/fdroidserver /home/vagrant/fdroidserver
            mkdir -p /vagrant/cache
            wget -q https://services.gradle.org/distributions/gradle-5.6.2-bin.zip --output-document=/vagrant/cache/gradle-5.6.2-bin.zip
            bash fdroidserver/buildserver/provision-gradle
            bash fdroidserver/buildserver/provision-apt-get-install http://deb.debian.org/debian
            source /etc/profile.d/bsenv.sh
            apt-get dist-upgrade
            apt-get install -t stretch-backports fdroidserver python3-asn1crypto python3-ruamel.yaml yamllint
            apt-get purge fdroidserver
            export GRADLE_USER_HOME=$PWD/.gradle
            set -x
            apt-get install sudo
            fdroid build --verbose --on-server --no-tarball
      - store_artifacts:
           name: Store APK
           path: unsigned
           destination: apk
```

It is important to run all of these commands in a single `run` section: we are setting some environment variables here, and if you spread the commands out over multiple `run` sections, variables will not be shared between them.

After that, be sure to add the new job to any workflow you would like to run it in.

# Refinements

## Using a prebuilt CI image

The CI script is currently rather long because we are not using a dedicated F-Droid build server image. Rather, we are using a generic image and need to install a couple of things on top of it. This is the same setup the [fdroiddata](https://gitlab.com/fdroid/fdroiddata) repository is using (as of February, 2021).

There is an experimental CI image for a fully configured F-Droid build server. Using it will slightly speed up your CI job and make the script simpler. The steps from the script have been moved into the image, hence there should not be any regressions on test results.

To use it, use the following job definition (for GitLab CI):

```gitlab-ci
fdroid build:
  image: registry.gitlab.com/mvglasow/ci-images-server:latest
  allow_failure: true
  artifacts:
    # name: "${CI_PROJECT_PATH}_${CI_JOB_STAGE}_${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHA}"
    paths:
      - unsigned/
    when: always
    # expire_in: 1 month
  cache:
    key: "$CI_JOB_NAME"
    paths:
      - .gradle
  script:
    # each `fdroid build --on-server` run expects sudo, then uninstalls it
    - set -x
    - apt-get install sudo
    - fdroid fetchsrclibs --verbose
    # this builds the latest version of the app from its source dir, using the build recipe in .fdroid.yml
    - fdroid build --verbose --on-server --no-tarball
```

For any other CI:
* Change the image to `registry.gitlab.com/mvglasow/ci-images-server:latest`
* Drop all install steps from the script, starting with `set -x` (we still need to install `sudo` before each run, as F-Droid will uninstall it when running in `--on-server` mode).

## Dynamically generated version codes

If your app generates the version code on the fly during the build process, and in a manner you cannot easily predict (e.g. the string includes the commit hash, or the version code is based on the timestamp of the last commit), F-Droid will build your app but will fail when verifying the version code.

You can work around this by skipping the version code check. To do so, edit your build recipe (`.fdroid.yml`). In the `Builds` entry, add the following line (be sure to use the correct indentation):
```yaml
novcheck: yes
```

This is generally discouraged (even more so in the actual build recipe on F-Droid) as it takes away a useful level of sanity checking. However, in some setups it is the easiest way to get a working F-Droid build when you cannot supply the correct version codes.

## Signing artifacts

The above workflow will generate an unsigned release APK, which cannot be installed directly on an Android system, as Android requires APKs to be signed. However, you can sign the APK after F-Droid finishes, allowing it to be installed.

Although the APK is a release one, nothing prevents you from signing it with a debug key. On your local machine, a debug keystore is created automatically the first time you build and install a debug version of an Android app. The F-Droid CI setup does not do this for you, hence you will need to create the debug keystore manually. Also, you need to make sure your CI caches it between runs so each build is signed with the same key as the previous one.

The Android toolchain will create the debug keystore in `$HOME/.android/debug.keystore`. However, some CI environments restrict caching to certain directories (for example, GitLab CI will not cache anything outside the project directory), so you may need to adjust the path.

If you use a debug keystore for other CI workflows as well, you may want to ensure they all use the same path. If needed, simply modify the path to `debug.keystore` to whatever you are already using in your CI environment.

You need to test if a keystore is already present and create one if necessary. Then you need to sign the APK generated by F-Droid. Signing is done in place (i.e. modifying the APK), which may not always be desirable – if necessary, simply create a copy and sign that.

Add the following commands to your CI script to do all of that (create a keystore in your project dir if needed, create a copy of the unsigned APK and sign it):

```
# create a keystore if we don’t have one
ls .android || mkdir .android
ls .android/debug.keystore || keytool -genkey -v -keystore .android/debug.keystore -storepass android -alias androiddebugkey -keypass android -keyalg RSA -keysize 2048 -validity 10000 -dname "C=US, O=Android, CN=Android Debug"
# sign the apk
cp -R unsigned signed
jarsigner -verbose -keystore .android/debug.keystore -storepass android -keypass android signed/*.apk androiddebugkey
```

Then, ensure `.android` is cached between sessions (on GitLab CI, you would just need to add `.android` to your cache paths).

Run CI twice and watch the output: both runs should generate a signed APK; a fresh keystore should get generated during the first run but not during the second.

In theory, the same procedure would work for a release signature as well. However, you would need to find a way for the CI workflow to securely access your release keystore, and modify commands to use the correct keystore.