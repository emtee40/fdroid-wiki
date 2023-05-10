### General details
The JSON-v2 index was a large overhaul, changing structures a lot again. `index-v2.json` now comes with two sections only:

* `repo` holding the information on the served repository itself
* `packages` holding details on the apps, grouped by their package name

Localized data are now assigned directly to the corresponding element and are no longer a "separate tree". The role of the "default value" is now assigned to the `en-US` locale, as it is the case with Fastlane. Find discussions, references and examples [here in the F-Droid Wiki](https://gitlab.com/fdroid/wiki/-/wikis/Index-V2).

### `repo` section
This contains details on the repository itself.

| field | comment |
| ----- | ------- |
| name  | name of the repo, per-locale. `en-US holds the default. |
| description | text describing the repo, again per-locale. `en-US holds the default. |
| icon  | file name of the repo's icon, again per-locale. `en-US holds the default. |
| address | URL to the repo (APK directory: `https://example.com/fdroid/repo`) |
| timestamp | timestamp of last update (?) |
| antiFeatures | list of antiFeatures defined in this repo. Each with fields `name`, `description` and `icon`, where all of those are per-locale again (e.g. `Ads->name->en-US`) |
| categories | categories defined in this repo. Same structure as antiFeatures |
| mirrors | array of objects, each with `url` (string) and optional `isPrimary` (boolean) |
| releaseChannels | object with (additional) release-channels, each with (localized) `name` and `description` (e.g. `releaseChannels->Beta->name->en-US`) |

### `packages` section
Holds all the apps the repo provides, bundled by their package name, corresponding to their YAML metadata files. Note that optional fields can be missing (if empty, they are not set)! For each app, the corresponding section holds two branches:

* `metadata` with the app's metadata
* `versions` with details on the corresponding APK files

#### `metadata`
Holds all the YAML's metadata:

| field | comment |
| ----- | ------- |
| added | timestamp of when the app was added to the index |
| lastUpdated | when the app was last updated (youngest APK) |
| categories | array 0..n of category names (referring `categories` of the `repo` section) |
| license | SPDX name of the license |
| authorName | string with the name of the author |
| authorEmail | string with the email address of the author |
| authorWebSite | string with the website URL |
| webSite | string with the website URL |
| translation | string with the URL |
| sourceCode | the `SourceCode:` URL |
| changelog | the `Changelog:` URL from the YAML |
| issueTracker | the issue tracker URL |
| donate | array 0..n of donation URLs |
| bitcoin | bitcoin address (string) |
| litecoin | litecoin address (string) |
| flattrID | FlattR ID (string) |
| liberapay | LiberaPay user name (string) |
| openCollective | openCollective user name (string) |
| screenshots | screenshots (if any), grouped by their types (`phone`, `sevenInch`, `tenInch`, `tv`, `wear`), then by locale (`en-US` should always be present) as an array 0..n of an object with `name` (file name relative to `repo/`), `sha256` and `size` (in bytes) |
| name | name of the app, again per-locale (with `en-US` always present); taken from Fastlane/Triple-T `title`, YAML `Name` or extracted from the APK |
| summary | short description, again per-locale (with `en-US` always present) |
| description | full description, again per-locale (with `en-US` always present) |
| video | URL (string) |
| icon | per locale, like `screenshots->phone` |
| featureGraphic | like `icon` |
| tvBanner | like `icon` |
| preferredSigner | hash |

#### `versions`
Versions are grouped by the file hash. For each APK (hash), there's the following structure:

| field | comment |
| ----- | ------- |
| added | timestamp of when the file was added to the index |
| file  | subset with the fields `name` (file name with a leading slash), `sha256` (the hash again) and `size` (in bytes) |
| manifest | subset with the fields `versionname`, `versionCode`, `usesSdk` (`minSdkVersion`, `targetSdkVersion`), `signer` (`sha256` as array 0..n of hashes), `usesPermissions` (array 0..n of `name` with the fully qualified permission name and optionally also `maxSdkVersion`), `nativecode` (optional; array of strings) |
| antiFeatures | just the names with an empty assignment (referring `antiFeatures` of the `repo` section) |
| src   | details on the source tarball (structure like `file`) |
| releaseChannels | array 0..n of string, referring the release-channel defined in `repo` |
| whatsNew | localized changelog for this version (from Fastlane/Triple-T) |
