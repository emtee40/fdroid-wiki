## Things to do first

* rename many classes ([#318](https://gitlab.com/fdroid/fdroidclient/-/issues/318#note_450961188))
* optimize all imports according to Android Studio defaults

## UI

* update list faster, not waiting for DB, e.g. when deleting repo ([#2447](https://gitlab.com/fdroid/fdroidclient/-/issues/2447))
* allow users to add and manage the IPFS gateways ([#2541](https://gitlab.com/fdroid/fdroidclient/-/issues/2541))
* in app details, show from what repo the shown data comes from
* show repo icons and URL in list ([#536](https://gitlab.com/fdroid/fdroidclient/-/issues/536))

## Add repo flow

* use download lib, so proxy settings are respected ([#1651](https://gitlab.com/fdroid/fdroidclient/-/issues/1651))
* Add QR code scanner via intent ([#1863](https://gitlab.com/fdroid/fdroidclient/-/issues/1863))
* Don't show URL again when clicking on or scanning repo link (part of [#966](https://gitlab.com/fdroid/fdroidclient/-/issues/966))
* Show list of apps included in repo when adding, and later as well (part of [#966](https://gitlab.com/fdroid/fdroidclient/-/issues/966))
* Properly detect mirrors by fetching signer when validating URL ([#1718](https://gitlab.com/fdroid/fdroidclient/-/issues/1718))
* don't add invalid repos ([#598](https://gitlab.com/fdroid/fdroidclient/-/issues/598))
* use pinned signer fingerprint in client when available ([#2557](https://gitlab.com/fdroid/fdroidclient/-/issues/2557))
* split fingerprint off into its own field right after pasting URL ([#2078](https://gitlab.com/fdroid/fdroidclient/-/issues/2078))
* don't duplicate https:// when prepending ([#2523](https://gitlab.com/fdroid/fdroidclient/-/issues/2523))
* Strip semicolons from supplied repository fingerprint ([#900](https://gitlab.com/fdroid/fdroidclient/-/issues/900))

# Unrelated work

* use localized categories from index ([2544](https://gitlab.com/fdroid/fdroidclient/-/issues/2544))
* remove Guardian Project repo from default list ([#2556](https://gitlab.com/fdroid/fdroidclient/-/issues/2556))
* remove "Conflicts With Active VPN!" warning, allow override ([#2577](https://gitlab.com/fdroid/fdroidclient/-/issues/2577))
* Use `repo_web_base_url` for app sharing ([#1946](https://gitlab.com/fdroid/fdroidclient/-/issues/1946))
* Bad intent: Intent { act=BonjourStatus } [#2518](https://gitlab.com/fdroid/fdroidclient/-/issues/2518)
