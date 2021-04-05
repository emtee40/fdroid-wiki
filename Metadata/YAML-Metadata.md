This page holds some (hopefully) useful hints on preparing YAML Metadata for our [Requests for Packaging](https://gitlab.com/fdroid/rfp/issues) – and to fix them up with our [merge requests](https://gitlab.com/fdroid/fdroiddata/-/merge_requests/).

## Basic skeleton
```yaml
AntiFeatures:
  - HopeNot
Categories:
  -
License:
AuthorName:
AuthorEmail:
AuthorWebSite:
WebSite:
SourceCode:
IssueTracker:
Translation:
Changelog:
Donate:
Bitcoin:

AutoName:

RepoType: git
Repo:

Builds:
  - versionName: '1.0'
    versionCode: 1
    commit: v1.0
    subdir: app
    submodules: true
    gradle:
      - yes

AutoUpdateMode: Version v%v
UpdateCheckMode: Tags
CurrentVersion: '1.0'
CurrentVersionCode: 1
```

**NOTES:**

* remove what is not used – eg. if an app has no AntiFeatures, remove the first two lines; if there's no WebSite, remove that line; if there're no submodules, remove that line
* versionNames/commit: if the value *could* be interpreted as a float or int (like here the `1.0`), it **must** be put in single quotes. Otherwise (eg. `1.0.0` has 2 dots and thus clearly is a string), it **must not.**
* there are more fileds available, as well as explanations on those and the ones in above skeleton. You can find them [here in our documentation](https://f-droid.org/en/docs/Build_Metadata_Reference/).
* summary & description are intentionally missing. Those should be provided upstream via [Fastlane](https://gitlab.com/snippets/1895688) or [Triple-T](https://gitlab.com/snippets/1901490).


## Specific to programming languages
The `Builds:` blocks will need adjustments depending on the programming language used by the given app.

### Java/Kotlin
In most cases, the build block works out-of-the-box as outlined above.

### Flutter
```yaml
Builds:
  - versionName: 0.0.3
    versionCode: 3
    commit: v0.0.3
    output: build/app/outputs/apk/release/app-release-unsigned.apk
    srclibs:
      - flutter@1.22.5
    rm:
      - ios
      - .vs
    prebuild: echo "flutter.sdk=$$flutter$$" >> ../local.properties
    build:
      - $$flutter$$/bin/flutter config --no-analytics
      - $$flutter$$/bin/flutter packages pub get
      - $$flutter$$/bin/flutter build apk --flavor fdroid

AutoUpdateMode: Version v%v
UpdateCheckMode: HTTP
UpdateCheckData: https://raw.githubusercontent.com/<owner>/<repo>/<branch>/pubspec.yaml|version:\s.+\+(\d+)|.|version:\s(.+)\+
CurrentVersion: 0.0.3
CurrentVersionCode: 3
```

This is a skeleton for a Flutter app that does not use build flavors. **Notes:**

* `output:` can differ, depending on how the project is set up. Unfortunately, the path used cannot be spotted in the logs of our CI or issuebot builds, so if you cannot build locally and see for yourself, this might be a try-and-err. Nevertheless you **must** specify `output` as else our build process considers it an `ant` build and will fail (using the wrong approach, then failing with an error message like `Invalid or unsupported command "update lib-project -p ."`). Other possible values include:
  - `build/app/outputs/apk/release/app-release.apk`
  - `build/app/outputs/apk/fdroid/release/app-fdroid-release.apk` (flavor: `fdroid`)
  - pattern with build flavor: `build/app/outputs/apk/[{flavor}/]release/app-[{flavor}-]release.apk`
* `srclibs:` never use something like `flutter@stable` or `flutter@dev`. The value after the `@` must match an existing [Flutter release](https://github.com/flutter/flutter/releases), so the very same build can reproducibly be run later again (also [see here](https://gitlab.com/fdroid/fdroiddata/-/merge_requests/8428#note_513934639) for reference)
* `rm` is project specific. Just remove everything that's not needed for the Android build.
* `prebuild` might need additional steps. The one specified in above skeleton suffices for most cases (and seems to be always needed).
* `build:` the first line **must** be there – as by default, Flutter has analytics enabled. The second line may be obsolete, but is often needed. In the third line, ommit the `--flavor <flavorName>` if the app has no build flavors.
* even though Flutter apps use gradlew, do **not** specify `gradle: yes` or `gradle: <flavor>`. That's dealt with differently with Flutter apps. Similarly, using `subdir` in most cases simply seems to break the build; it's not needed here as Flutter deals with it itself. This is why `rm` is important here.

If you want to find examples for a given keyword in connection with Flutter apps in our repo, you can change into the `metadata/` directory and run something like

```bash
grep 'output:' $(grep '$$flutter' *.yml | awk -F: '{print $1}' |uniq )
```

(this will e.g. list the `output:` lines for all our Flutter apps).

Hint to give to devs – [XDDK points out](https://gitlab.com/fdroid/rfp/-/issues/1615#note_490534508) devs can specify flutter version in `pubspec.yaml`

```yaml
environment:
    sdk: ">=2.7.0 <3.0.0" #the working dart sdk versions
    flutter: ^1.25.0-8.3.pre #the flutter sdk version
```

([Related discussion](https://github.com/flutter/flutter/issues/29211))

### NativeScript

Coming soon.

### ReactNative

See https://f-droid.org/2020/10/14/adding-react-native-app-to-f-droid.html