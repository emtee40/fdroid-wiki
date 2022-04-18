As the name suggests, this page collects questions asked frequently and puts some answers to them – so we don't have to answer them over and again.

**Contents:**

[[_TOC_]]

## General
### How long does it take for my app to show up on website and client?
Your MR has just been merged, but the APK did not show up immediately? That's normal: the APK must first been built on the build server (happens automatically when the next build cycle starts), then signed (manual step), then a new index must be created and deployed.

* a build cycle currently takes about 36h
* apps are manually signed after that, and then uploaded
* next build cycle starts after signing is completed

So if you're lucky, it takes 2 days (new build cycle just started minutes after the merge). If your not-that-lucky (merge happened a minute after a build cycle started), it will take 4 days. If you're very unlucky, Murphy visits in between with some problems… So no panic before 5 days have passed, please :wink:

Please also note that the website is updated asynchronously – so while the index might already have reached your local client, website might still be a bit behind.


### Can I see the current build status?
Sure: for this we have the [F-Droid Monitor](https://monitor.f-droid.org/builds) where you can see the current build cycle ([running](https://monitor.f-droid.org/builds/running)), the last completed one ([build](https://monitor.f-droid.org/builds/build)), [disabled](https://monitor.f-droid.org/builds/disabled) builds, and more.

## Merge Requests
### Why do we always ask for static values on versionCode/versionName in `build.gradle`?
> fdroidserver has to check 2753 repositories for updates every build cycle. If we spend, for example, as little as 30 seconds for each, this will be 23 hours. Thus, updates checking needs to be fast. We cannot afford launching a virtual machine (like we do for building apps) for that.
>
> Those constraints impose a big limitation: we can't run build.gradle to find new values of versionCode and versionName because this is untrusted code. We can only rely on static analysis of build.gradle contents. Currently fdroidserver recognizes versionCode and versionName only when they are defined as literals.

([source](https://gitlab.com/fdroid/fdroiddata/-/merge_requests/6570#note_353356522); note the number of repositories has quite increased since, and long passed 3k)

### How can I trigger the bot/build to run again?
Pipelines are run automatically whenever a new commit was pushed to the merge request. However, sometimes there's no update needed for the MR itself, as fixes have been applied in the app's source repo – or some pipeline failed for other reasons, like network errors. The account that opened the merge request (as well as members with status developer and up) can always trigger the pipelines again:

![image](uploads/fd7c1a2a6514c617de8cf396b98f7611/image.png)

Members with status developer and up usually can trigger them too – unless the source branch is protected. So please make sure to use a separate branch when opening merge requests – as the `master`/`main` branch is always protected in GitLab.

**Note:** clicking on the *name* of the job (left of the ② in above screenshot) will bring you to the corresponding logs. This way you can e.g. figure out why a build failed – or what labels *issuebot* would have applied to the MR (similar to what it does on RFP) if it had "higher privileges".

### Can I obtain the `*.apk` your CI built?
It can be quite helpful to get your hands on that, yes. And you easily can

* **CI build:** click on *fdroid build* (shown in above screenshot), then use the "browse" button on the right side of the opening page, navigate to the `unsigned/` directory.
* **Issuebot:** If the issuebot report says "`<com.example.packageName>` builds", follow the final link of that report. Again, the `*.apk` is available in the `unsigned/` directory.


## Misc
### What are the criteria for an app to show up in the "Latest" tab?
The F-Droid client features a "Latest" tab – and there's always confusion about what shows up there and in which order. So let's sum that up:

The sorting logic [can be found here](https://gitlab.com/fdroid/fdroidclient/-/merge_requests/971/diffs#fbc34fe643b953bd45f1cc19fc874453c683b074_854_865). Don't let yourself be misled by the name "Latest" – it's not really meant as "Newest", but rather like "latest fashion"; though some of us argued `last_updated` should have a much higher weight, it in fact has not. The sorting algorithm from above linked code, put in easy words, is rather:

* pick all apps which are localized to what the user has set as their device locale
* order that by name (nulls last; i.e. "make sure it has a name")
* then by icon (i.e. "has an icon")
* then by summary, description (i.e. has summary, has description)
* then by "has any graphic" (screenshot, featureGraphic etc – one of them suffices)
* then by "was just added now or updated in the last 7 days" (ahem, this should rather compare to the index date but does not)
* then by whether it has a "whatsNew" (i.e. per-release changelogs in Fastlane)
* **only then by when it was last updated** (descending, so newest first)
* then by when it was added (oldest first – meaning, "this app already is with us for a long time and still well maintained")

### How can I verify the downloaded F-Droid.apk?
Some details on this can be found in our documentation at [Release Channels and Signing Keys](https://f-droid.org/docs/Release_Channels_and_Signing_Keys/). To make it easier to understand for non-devs, here are the steps in short:

* download https://f-droid.org/F-Droid.apk.asc and https://f-droid.org/F-Droid.apk
* load the public key for admin@f-droid.org
* compare the fingerprint with what above mentioned page states
* verify the file

On Linux, this is what it would look like:

```bash
# download the files
wget -q https://f-droid.org/F-Droid.apk.asc
wget -q https://f-droid.org/F-Droid.apk
# load the public key
gpg --keyserver keyserver.ubuntu.com --recv-key 37D2C98789D8311948394E3E41E7044E1DBA2E89
# compare the fingerprint
gpg --fingerprint 0x41E7044E1DBA2E89
# verify the file
gpg --verify F-Droid.apk.asc F-Droid.apk
```

The expected output of the last command should look like:

```
gpg: Signature made Mon 09 Aug 2021 11:17:55 PM CEST
gpg:                using RSA key 802A9799016112346E1FEFF47A029E54DD5DCE7A
gpg: Good signature from "F-Droid <admin@f-droid.org>" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 37D2 C987 89D8 3119 4839  4E3E 41E7 044E 1DBA 2E89
     Subkey fingerprint: 802A 9799 0161 1234 6E1F  EFF4 7A02 9E54 DD5D CE7A
```

The warning can be ignored. If it disturbs you: as you verified the signature, you could express your trust to the key and sign it locally (i.e. just for you):

```bash
$> gpg --edit-key 0x41E7044E1DBA2E89
gpg> trust
3     # I trust marginally
gpg> lsign
gpg> save
```

Run `gpg -verify` again and the warning will be gone:

```
gpg: Signature made Mon 09 Aug 2021 11:17:55 PM CEST
gpg:                using RSA key 802A9799016112346E1FEFF47A029E54DD5DCE7A
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   3  signed:  19  trust: 0-, 0q, 0n, 0m, 0f, 3u
gpg: depth: 1  valid:  19  signed:   1  trust: 1-, 0q, 0n, 14m, 4f, 0u
gpg: depth: 2  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 1f, 0u
gpg: next trustdb check due at 2021-11-05
gpg: Good signature from "F-Droid <admin@f-droid.org>" [full]
Primary key fingerprint: 37D2 C987 89D8 3119 4839  4E3E 41E7 044E 1DBA 2E89
     Subkey fingerprint: 802A 9799 0161 1234 6E1F  EFF4 7A02 9E54 DD5D CE7A
```
