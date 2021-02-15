> **NOTE:** This is an approach of documenting the `index-v1.json` structures by reverse-engineering them. Consider it a ***DRAFT*** for now, until properly reviewed. It's **not** (yet) an "authoritative reference"!!!

### General details
* the JSON Index always comes compressed inside the `index-v1.jar`
* it's a JSON (obviously) object with the properties `repo`, `apps`, `requests`, `packages` (that all?)
* `repo` is an object with the properties … (well, this part is easy to reverse engineer). Properties are the same as with the XML index, only 2 props are missing: `pubkey` and `url`. In XML these are attributes to the `<repo>` tag, in JSON they are properties of the `repo` object.
* `apps` is an array of apps, each item having the properties … and here it’s getting a bit difficult, as some of those properties are optional (e.g. localized and what might be in there)
* `requests` is an object holding the arrays install and uninstall (apps suggested by the repo to be automatically installed/uninstalled) – but how do those arrays look like when filled? A guess would be it’s just package names.
* `packages` are the available .apk files with their properties. Again, some of them seem to be optional (e.g. the `uses-permission` array is completely missing (not empty, missing) if an app doesn’t request any permission)

Hans writes:

> As much as possible, the index JSON data tries to be as close as possible to the [YAML build metadata files](https://f-droid.org/docs/Build_Metadata_Reference/)¹
>
> So `apps` is the list of all the app metadata from those files, and the `packages` metadata is derived from the `Builds` section.

¹ also see the [YAML Metadata](Metadata/YAML-Metadata) wiki page

And from Coffee comes:

> There’s https://gitlab.com/Matrixcoffee/twif-util which works with repo index files. It’s not documentation as such, but there’s code, and there are at least two half linters in there, which unfortunately don’t make a whole linter together quite yet. It’s the result of all my research and questions up until now though, and as such may contain useful information.


### `repo` section
This contains details on the repository itself. In XML, all but `description` (which is a child element of `repo`) are attributs of `repo`. In JSON, `repo` is an object at the top-level (`$json->repo`).

| XML | JSON | comment |
| --- | ---- | ------- |
| icon | icon | file name of the repo's icon |
| name | name | name of the repo |
| pubkey | - | public key of the repo (hash?) |
| timestamp | timestamp | timestamp of last update (?) |
| url | address | `https://example.com/fdroid/repo` |
| version | version | (int) of what? |
| description | description | text describing the repo |


### `apps` section
This is the `<application id="android.package.name">` tag in XML (XPath: `/fdroid/application[0..n]`), corresponding to the `$JSON->apps` array in JSON. Note that in JSON, optional fields can be missing (if empty, they are not set)!

| YAML | XML | JSON | comment |
| ---- | --- | ---- | ------- |
| (file name) | id | packageName | `android.package.name` |
| - | added | added | yyyy-mm-dd in XML, timestamp in JSON |
| - | lastupdated | lastUpdated | as `added` |
| Name | name | -<br>(see localized data) | human-readable name |
| Summary | summary | summary | (text) |
| - | icon | icon | file name w/o path |
| Description | desc | description | full description (HTML) |
| License | license | license | as in metadata |
|  |  |  |
| WebSite | web | webSite | URL |
| SourceCode | source | sourceCode | source code URL |
| IssueTracker | tracker | issueTracker | issue tracker URL |
| Changelog | changelog | changelog | URL |
|  |  |  |
| AuthorName | author | authorName | (text) |
| AuthorEmail | email | authorEmail | (`email@address`) |
| - | marketversion | - |  |
| - | marketvercode | - |  |
| RequiresRoot | requirements | ??? | e.g. `root` |
| AntiFeatures | antifeatures | antiFeatures | comma-separated list in XML, array in JSON |
| Categories | categories | categories | comma-separated in XML, array in JSON |
| - | category | - | just the main category (the first one mentioned in metadata) |
| CurrentVersionCode (if set) | - | suggestedVersionCode | (int) |
| (Build) | package | (moved to `packages` section) | see there |
|  |  |  |
| Donate | donate | donate | URL |
| FlattrID | flattr | flattrID |  |
| Liberapay | liberapay | liberapay | Liberapay user name |
| LiberapayID | liberapay | liberapayID | Liberapay user ID (deprecated) |
| OpenCollective | - | openCollective | OpenCollective user name |
| Bitcoin | bitcoin | bitcoin | Bitcoin address |
| LiteCoin | litecoin | litecoin | Litecoin address |
| MaintainerNotes | - | - |  |

#### localized data
These are only available with the JSON index, where they are now mandatory. To be found in `$json->apps[$i]->localized->{$lang}`, with lang being e.g. `en-US`. These are objects. For their corresponding metadata, see [All About Descriptions, Graphics and Screenshots](https://f-droid.org/en/docs/All_About_Descriptions_Graphics_and_Screenshots). Names here usually correspond to those of the [Fastlane structure](https://gitlab.com/snippets/1895688)'s directories resp. `.txt` files.

| field | comment |
| ----- | ------- |
| name | human-readable name of the app |
| featureGraphic | if such a graphic exists, this is the file name (e.g. 'featureGraphic.png' or 'featureGraphic.jpg) |
| phoneScreenshots | array [0..n] of graphic file names (if such exist) |
| | **guessed names:** |
| sevenInchScreenshots | dito |
| tenInchScreenshots | dito |
| tenInchScreenshots | dito |
| wearScreenshots | dito |
| icon | file name |
| tvBanner | file name |
| full_description | description |
| short_description | summary |
| title | (text) |
| video | (text) |
| ~~changelogs~~ | ~~array [`<versionCode>`] of text~~ that's the theory, but not reality. Reality looks like: |
| whatsNew | (text; content of the `changelogs/<versionCode>.txt` matching the latest APK) |


### packages
In XML directly within the `apps` section (XPath: `/fdroid/application[0..n]/package[0..n]`), in JSON its own `$json->packages` object (`$json->packages->{$package_name}` being an array of objects). Most fields have a correspondence in the YAMLs `Build` section.

| YAML | XML | JSON | comment |
| ---- | --- | ---- | ------- |
| versionName | version | versionName | human-readable version for this APK |
| versionCode | versioncode | versionCode | (int) |
| - | apkname | apkName | file name without path |
| - | hash | hash | hash value for the file |
| - | `<hash type=` | hashType | hash type, usually sha256 |
| - | size | size | size in bytes |
| - | sdkver | minSdkVersion | (int) minimal supported Android version |
| - | targetSdkVersion | targetSdkVersion | (int) targeted Android version |
| - | added | added | when this APK was added. XML: yyyy-mm-dd, JSON: (int) timestamp |
| - | sig | sig | hash of the signature |
| - | - | signer | ? (longer hash) |
| - | permissions | uses-permission | permissions required. XML: comma-separated list of short names. JSON: array[0..n] of array[0,1] with 0 being the full android.permission.name and 1 either `none` or (int) maxSdkVersion |
