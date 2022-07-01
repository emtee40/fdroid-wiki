Several stages happen before an app update is published. In this page, we will explain all the steps that happen.

## Finding updates

First, F-Droid checks for app updates existing. To see if the update has been detected, look at the app in the [metadata](https://gitlab.com/fdroid/fdroiddata/-/tree/master/metadata) directory of fdroiddata (for example, in F-Droid's case the file is [org.fdroid.fdroid](https://gitlab.com/fdroid/fdroiddata/-/blob/master/metadata/org.fdroid.fdroid.yml)). The list of known versions is listed under the `Builds` key.

If the latest version isn't under `Builds` yet, `checkupdates` either hasn't found the build yet, or automatic updates aren't enabled for the app. Please give it at least 2 days and check [fdroiddata](https://gitlab.com/fdroid/fdroiddata) `Issues` and `Merge Requests` to see if anyone is working on it.

## Build cycle

Each build cycle starts at a certain fdroiddata commit. At https://monitor.f-droid.org/builds/build you can see the current cycle.

If you're lucky, you may already see your app under `Succcesful builds` (or `Failed builds`). If the build failed, usually a fixed version will be tried the next build cycle or upstream will be contacted if the F-Droid team can't fix the build failure themselves.

If your app isn't listed, it either hasn't been built yet or will be part of the next cycle. On the webpage under `fdroiddata version` you can see which commit is currently building. Click this link and go to the app's entry in `metadata` again. If the latest version is under `Builds`, it is part of this cycle. If not, it will be part of the next cycle.

## Signing

After a few days (on average about 3), someone will sign all the built apps on an airgapped machine and push the signed APK files to the F-Droid repository. You can tell this happened from an fdroiddata commit with the name [Update known apks](https://gitlab.com/fdroid/fdroiddata/-/commits/master?search=Update+known+apks). After this, a new cycle starts.