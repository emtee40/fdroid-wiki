
Running Android emulators in GitLab CI is very handy for running test suites on merge requests.  Also, it lets you test on old Android versions even if you have no device with that Android version.  This was announced as a release in a [blog post](https://f-droid.org/2021/02/24/running-emulator-tests-on-gitlab-ci.html)

We use our own Docker images to support this:
* https://gitlab.com/fdroid/ci-images-client
* https://gitlab.com/fdroid/ci-images-base

Unfortunately, the quality control on the `system-image` packages is spotty, so a number of them are not really usable. It is important to use the `default` rather than `google_apis` system-images they do not contain the Google Play and apps binary blobs, and because the Google apps seem to slow down the boot process a lot.  Also, it seems the `android-22` through `android-27` system images seem to require less resources than the newer ones, so much so that they are unlikely to work at all for some projects.

Here are some example runs to see what works:

* all available _system-images_ without KVM ("no accel") in regular shared runners:
  * https://gitlab.com/eighthave/fdroidclient/-/pipelines/258788881
  * https://gitlab.com/eighthave/fdroidclient/-/pipelines/260697855
* all available _system-images_ with KVM in _fdroid_ runners:
  * https://gitlab.com/eighthave/fdroidclient/-/pipelines/260698010
  * https://gitlab.com/eighthave/fdroidclient/-/pipelines/258791968


## Usage

The [_fdroiclient_ setup](https://gitlab.com/fdroid/fdroidclient/-/blob/8c2fa955/.gitlab-ci.yml) uses YAML templates to make it easy to choose specific emulator setups, specifically, the [`test-template`](https://gitlab.com/fdroid/fdroidclient/-/blob/8c2fa955/.gitlab-ci.yml#L24), the [`connected-template`](https://gitlab.com/fdroid/fdroidclient/-/blob/8c2fa955/.gitlab-ci.yml#L80), and the [`kvm-template`](https://gitlab.com/fdroid/fdroidclient/-/blob/8c2fa955/.gitlab-ci.yml#L109).

We use the default image in _fdroidclient_ like this:

```yaml
no-accel 22 default x86:
  <<: *test-template
  <<: *connected-template

kvm 29 default x86_64:
  <<: *kvm-template
```


## Test image based on Google's

Google has [their own scripts](https://github.com/google/android-emulator-container-scripts) for creating Docker images for the Android emulator.  This is an experimental project to do something like _fdroid/ci-images-client_ with their images as a base:

* <https://gitlab.com/eighthave/ci-images-emulator-kvm>


## microg system-images

The [microg system-image repository](https://github.com/microg/GmsCore/wiki/Development-Tools) is also [included](https://gitlab.com/fdroid/ci-images-client/-/commit/85ed9490) in the F-Droid setup.  There are currently two images:

* `system-images;android-29;microg;x86_64`
* `system-images;android-23;microg;x86` - requires emulator v28 or older because it lacks a "ranchu" kernel.
