As the name suggests, this page collects questions asked frequently and puts some answers to them – so we don't have to answer them over and again.

## How long does it take for my app to show up on website and client?
Your MR has just been merged, but the APK did not show up immediately? That's normal: the APK must first been built on the build server (happens automatically when the next build cycle starts), then signed (manual step), then a new index must be created and deployed.

* a build cycle currently takes about 36h
* manual signing happens afterwards
* next build cycle starts after signing

So if you're lucky, it takes 2 days (new build cycle just started minutes after the merge). If your not-that-lucky (merge happened a minute after a build cycle started), it will take 4 days. If you're very unlucky, Murphy visits in between with some problems… So no panic before 5 days have passed, please :wink:


## Why do we always ask for static values on versionCode/versionName in `build.gradle`?
> fdroidserver has to check 2753 repositories for updates every build cycle. If we spend, for example, as little as 30 seconds for each, this will be 23 hours. Thus, updates checking needs to be fast. We cannot afford launching a virtual machine (like we do for building apps) for that.
>
> Those constraints impose a big limitation: we can't run build.gradle to find new values of versionCode and versionName because this is untrusted code. We can only rely on static analysis of build.gradle contents. Currently fdroidserver recognizes versionCode and versionName only when they are defined as literals.

([source](https://gitlab.com/fdroid/fdroiddata/-/merge_requests/6570#note_353356522); note the number of repositories has quite increased since, and long passed 3k)

