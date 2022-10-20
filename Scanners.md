F-Droid uses all sorts of scanners to help look for proprietary libs, trackers, etc.

## `fdroid build --verbose appid`

Before executing the `build:` section steps, a source code scan is executed to catch problematic dependencies declared directly in the files.

## `fdroid scanner --verbose appid`

Same scanner as above, but does not start an actual build, just scans the source code.

## `fdroid build --scan-binary --verbose appid`

This provides a very simple but quite effective binary APK scanner **after** the build step. This will detect dependencies that most likely slipped through the source code scanner _(mentioned above)_ unnoticed because we allow the Google maven repo and they come from there. Based on the Exodus list, more info: https://gitlab.com/fdroid/fdroidserver/-/merge_requests/1137

Limitations:
* Needs more known non-free and tracker signatures.
* _proguard_ used in obfuscate mode could probably fool this.
* This deliberately skips over 'referenced' classes. That means somewhere in the dependency chain there's probably a compileOnly dependency to a blacklisted package but this isn't actually shipped inside the APKs.

* More info: https://gitlab.com/fdroid/fdroidserver/-/merge_requests/766

## `fdroid scanner --exodus --verbose apkfile.apk`

Same scanner as the binary one above, but does not start an actual build, just scans the APK file.
