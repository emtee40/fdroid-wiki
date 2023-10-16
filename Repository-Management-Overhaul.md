## Things to do first

* rename many classes ([#318](https://gitlab.com/fdroid/fdroidclient/-/issues/318#note_450961188))
* ~~optimize all imports according to Android Studio defaults~~

## UI

* ~~update list faster, not waiting for DB, e.g. when deleting repo ([#2447](https://gitlab.com/fdroid/fdroidclient/-/issues/2447))~~
* ~~show repo icons and URL in list ([#536](https://gitlab.com/fdroid/fdroidclient/-/issues/536))~~
* ~~in app details, show from what repo the shown data comes from (#1824)[https://gitlab.com/fdroid/fdroidclient/-/issues/1824]~~
* allow users to add and manage the IPFS gateways ([#2541](https://gitlab.com/fdroid/fdroidclient/-/issues/2541))

## Add repo flow

* ~~use download lib, so proxy settings are respected ([#1651](https://gitlab.com/fdroid/fdroidclient/-/issues/1651))~~
* ~~Add QR code scanner via intent ([#1863](https://gitlab.com/fdroid/fdroidclient/-/issues/1863))
  `new IntentIntegrator(this).initiateScan()`~~
* ~~Don't show URL again when clicking on or scanning repo link (part of [#966](https://gitlab.com/fdroid/fdroidclient/-/issues/966))~~
* ~~Show list of apps included in repo when adding, and later as well (part of [#966](https://gitlab.com/fdroid/fdroidclient/-/issues/966))~~
* ~~Properly detect mirrors by fetching signer when validating URL ([#1718](https://gitlab.com/fdroid/fdroidclient/-/issues/1718))~~
* ~~don't add invalid repos ([#598](https://gitlab.com/fdroid/fdroidclient/-/issues/598))~~
* ~~split fingerprint off into its own field right after pasting URL ([#2078](https://gitlab.com/fdroid/fdroidclient/-/issues/2078))~~ (doesn't make sense in new UI)
* ~~don't duplicate https:// when prepending ([#2523](https://gitlab.com/fdroid/fdroidclient/-/issues/2523))~~ (no more https:// in input field)
* ~~Strip semicolons from supplied repository fingerprint ([#900](https://gitlab.com/fdroid/fdroidclient/-/issues/900))~~ (doesn't make sense as we don't support separate fingerprint entry anymore, just create a proper link or qr code)
* ~~search chip to show apps in a specific repo reachable from repo details and show after adding a repo (which progress spinner while still updating) ([#570](https://gitlab.com/fdroid/fdroidclient/-/issues/570))~~
* ~~add `https://` in front of input, if no scheme was given~~
* ~~support adding repos behind basic auth~~
* ~~use pinned signer fingerprint in client when available ([#2557](https://gitlab.com/fdroid/fdroidclient/-/issues/2557))~~
* Should HTTP 30x Redirects be handled in the Add Repo flow?  For example, user adds https://service.tagesschau.de/app/repo, then Add Repo could fetch the new URL and present it to the user: do you want to use the new URL?

### Implementation notes

Handle incoming URL:
* ~~deny adding repos if unknown sources is disallowed by device manager~~
* ~~check if repo URL is valid, show error if not~~
* check if swap URL and if so redirect to swap workflow
* ~~maybe do some auto-probe on certain URLs such as bare domain name e.g. "guardianproject.info"~~
* ~~request orbot to start tor, if enabled (and needed for URL?)~~
* ~~if fingerprint is given and known, maybe check situation first before fetching repo~~
* ~~fetch extracted URL first to get info from repo such as fingerprint, icon, number of apps, anti-features etc.~~
* ~~check how using a repo from a flash drive works (just swap flow?)~~
* @uniqx looking into adding Storage Access Framework flow to Manage Repos (e.g. choosing a repo hosted on Nextcloud, Google Drive, Samba, etc)

possible results:
* could not connect to repo for whatever reason -> show error
* given fingerprint isn't matching the one from the repo -> show error
* new repo -> show repo info and ask user if they want to add this
* mirror of new repo (TODO how can this be?)
* existing repo -> tell user that this repo already exists, maybe show repo details?
* mirror of existing repo -> ask user if they want to add this repo as a mirror

## Representing apps from multiple repos

* Change database structure to be faster, better support multiple repos. ([#511](https://gitlab.com/fdroid/fdroidclient/-/issues/511))
* Properly support multiple repositories with the same app ([!375](https://gitlab.com/fdroid/fdroidclient/-/merge_requests/375))
* Add support for multiple repos with conflicting apps/apks ([#33](https://gitlab.com/fdroid/fdroidclient/-/issues/33))
* allow user to change order of repos in the list, adjusting priorities in the DB (open/old MR [!435](https://gitlab.com/fdroid/fdroidclient/-/merge_requests/435) [#1887](https://gitlab.com/fdroid/fdroidclient/-/issues/1887))


One idea: new repos are added with lower priority, so users can easily get new apps by adding repos without dealing with the hard questions.  Then per-app, users can decide to prioritize any repo as the selected source for that app.

# Test Links

Some repo links in the the newly introduced fdroid.link format.

* F-Droid repo: https://fdroid.link/#repo=https://f-droid.org/repo&fingerprint=43238d512c1e5eb2d6569f4a3afbf5523418b82e0a3ed1552770abb9a9c9ccab
* 3rd party repo: https://fdroid.link/#repo=https://obfusk.dev/fdroid/repo&fingerprint=2A21B7FFC93B878724B1991C05DAE113C72B93A556C193F49B5D3342884798B7

![image](uploads/08b26b70dc2ee9776ab03167ae57185f/image.png)

* missing fingerprint: https://fdroid.link/#repo=https://obfusk.dev/fdroid/repo
* non existent domain: https://fdroid.link/#repo=https://bad.example.com/fdroid/repo&fingerprint=43238d512c1e5eb2d6569f4a3afbf5523418b82e0a3ed1552770abb9a9c9ccab
* 308 Redirect https://fdroid.link/#repo=https://service.tagesschau.de/app/repo
* fdroidrepos https://fdroid.link/#repo=fdroidrepos://obfusk.dev/fdroid/repo&fingerprint=2A21B7FFC93B878724B1991C05DAE113C72B93A556C193F49B5D3342884798B7
* fdroidrepo https://fdroid.link/#repo=fdroidrepo://obfusk.dev/fdroid/repo&fingerprint=2A21B7FFC93B878724B1991C05DAE113C72B93A556C193F49B5D3342884798B7
* http: https://fdroid.link/#repo=http://obfusk.dev/fdroid/repo&fingerprint=2A21B7FFC93B878724B1991C05DAE113C72B93A556C193F49B5D3342884798B7


* Old URL now a 308 Redirect: https://service.tagesschau.de/app/repo

### Other app stores

* https://play.google.com/store/apps/details?id=net.osmand.plus
* https://amazon.com/gp/mas/dl/android/net.osmand.plus

# Unrelated work

* ~~use localized categories from index ([2544](https://gitlab.com/fdroid/fdroidclient/-/issues/2544))~~
* remove Guardian Project repo from default list ([#2556](https://gitlab.com/fdroid/fdroidclient/-/issues/2556))
* remove "Conflicts With Active VPN!" warning, allow override ([#2577](https://gitlab.com/fdroid/fdroidclient/-/issues/2577))
* Use `repo_web_base_url` for app sharing ([#1946](https://gitlab.com/fdroid/fdroidclient/-/issues/1946))
* ~~Bad intent: Intent { act=BonjourStatus } [#2518](https://gitlab.com/fdroid/fdroidclient/-/issues/2518)~~
