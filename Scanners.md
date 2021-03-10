F-Droid uses all sorts of scanners to help look for proprietary libs, trackers, etc.

## `fdroid build --scan-binary`

This provides very simple but quite effective binary APK scanner after the build step. It currently only `com.google.android.gms` and `com.google.firebase`. These are the ones most likely to slip through the current scanner unnoticed because we allow the google maven repo and they come from there.

Limitations:

* Needs more known non-free and tracker signatures.
* _proguard_ used in obfuscate mode could probably fool this.
* This deliberately skips over 'referenced' classes. That means somewhere in the dependency chain there's probably a compileOnly dependency to a blacklisted package but this isn't actually shipped inside the APKs.

* More info: https://gitlab.com/fdroid/fdroidserver/-/merge_requests/766
