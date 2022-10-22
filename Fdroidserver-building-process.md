This is a detailed note about how the *fdroidserver* builds an app based on the `Builds` blocks in the metadata.

1. NDK is checked. If `ndk` is set, *fdroidserver* will check if NDK is installed. If not, build will failed directly.
1. Env virables are set. `ANDROID_HOME`, `ANDROID_SDK` and `ANDROID_SDK_ROOT` are set for Android SDK. `JAVA{version}_HOME` is set for Java. `ANDROID_NDK`, `NDK` and `ANDROID_NDK_HOME` are set for Android NDK.
1. `sudo` is executed. Please note that the `Repo` hasn't been cloned so that `sudo` is executed in the root directory of *fdroiddata*. Virables hasn't been replaced so that `$$xxx$$`s do not work. After `sudo`, sudo will be removed.
1. `Repo` is cloned into `build/{appID}` and checkout to `commit`. If `submodules` is ture then submodules will be inited.
1. Path is changed to `build/{appID}`. If `subdir` is set, the path will change to it.
1. `init` is executed. Please not that `$$srclibs$$`s are not available in `init`.
1. `patch` is applied on `build/{appID}`.
1. `srclibs` are prepared.
1. `local.properties` files are updated. `sdk.dir` and `ndk.dir` are set.
1. If `gradle` is set, `build.gradle`/`build.gradle.kts` is processed.
1. Debuggable flags are removed.
1. `forceversion` and `forcevercode` are inserted.
1. Pathes in `rm` are removed.
1. Keysigning configs will be removed from build.gradle properties files. Please note this may break the build and needs to be fixed in `prebuild`.
1. `prebuild` is executed.
1. If none of `maven`, `gradle`, `buildozer` and `output` is set, ant will be used by default and `android update project` will be executed. Because `android` has been removed from Android SDK, the build will fail.
1. The `clean` task is performed.
1. The source code is scanned for common problems. Binaries in `scandelete` will be removed and in `scanignore` will be ignored.
1. `build` is executed.
1. `buildjni` is executed.
1. `maven`/`buildozer`/`gradle`/`ant` is executed.
1. Apk in output path is picked and checked. Output path is calculated from the build method ot `output`.