We are creating a new index format to solve a lot of issue and improve things.  Track progress here:

* https://gitlab.com/groups/fdroid/-/issues?scope=all&state=opened&label_name[]=index-v2
* https://gitlab.com/groups/fdroid/-/merge_requests?scope=all&state=opened&label_name[]=index-v2

Weekly Meeting: Tuesdays, 1300 UTC on #fdroid-dev and https://meet.calyx.net/fdroid

## Open Topics

#### Can we go with readable json only? I.e. Don't use the jar format and rely on the web server compressing the data.

We need the JAR, it provides the signing method.  The code for verifying signed JARs is built into Android and Java. Also, there is no guarantee that there is a webserver serving the repo.  It can also come from IPFS, USB Disk, Bluetooth, etc. -@eighthave
We could go with inline signing like Debian does, let's discuss this in the next meeting -@jspricke
Publish both a `entry-v2.jar` and a `entry-v2.json`.

#### Do we need the `"version": 30001` field?

Absolutely, that's the index format version.  It is the way to tell the client to upgrade its database. For example, like when a new metadata field gets added, like `Liberapay:`. -@eighthave
It should be enough for the client to update the DB when there is an new client version, let's discuss in the meeting. -@jspricke


### Subdirectories

* Test if feasible (Jochen: which old client versions to test?)
* https://gitlab.com/fdroid/fdroidserver/-/issues/966
* maybe move the toplevel directory, i.e. https://f-droid.org/packages?

### Tickets

* [ ] ~~[Tag reproducible builds](https://gitlab.com/fdroid/fdroidserver/-/issues/178)~~
* [x] [File name can be path](https://gitlab.com/fdroid/fdroidclient/-/issues/1164)
* [x] [Changelog per version, not app](https://gitlab.com/fdroid/fdroidserver/-/issues/527)
* [ ] [Anti-Feature Maintainer-Notes as structured text](https://gitlab.com/fdroid/fdroidserver/-/issues/683)
* [x] [Signing](https://gitlab.com/fdroid/fdroidserver/-/issues/835) [Algorithm](https://gitlab.com/fdroid/fdroidclient/-/issues/1989) -> Jarsigner without legacy algorithm
* [ ] ~~["frosting" in the APK Signing Block](https://gitlab.com/fdroid/fdroidserver/-/issues/935)~~
* [ ] [Extension to app descriptions](https://gitlab.com/fdroid/fdroidserver/-/issues/953)
* [x] [multiple donation links](https://gitlab.com/fdroid/fdroidserver/-/issues/968)

## New index format

* JSON 1.0 data format
* all JSON files are encoded using UTF-8

### entry point

* Entry point is `entry.jar` (signed) linking to different files.
* Diff is oldTimestamp to current version.
* Diff life in diff/ subdirectory.

```json
{
  "timestamp": 1641697189000,
  "version": 30001,
  "maxAge": 14,
  "files": {
    "index": {
      "name": "index-v2.json",
      "sha256": "c4bd600d7ed554a69201d7aaa4f7f7ef7cd13dc20cdd4af254d17ffd12bd7cdc",
      "size": 123
    },
    "1641697179000": {
      "name": "diff/1641697179000-1641697189000.json",
      "sha256": "b1f27fa87f8cabca50cdcd462a0f500d79d883b965a498d0e49eea560b39be1f",
      "size": 123
    },
    "1641697169000": {
      "name": "diff/1641697179000-1641697189000.json",
      "sha256": "b862213931d033000b55b1836c8980a4a64ab06903b7abca48d436b7ac45954e",
      "size": 123
    }
  }
}
```


### repo and package index

* `index-v2.json` contains all the data:
  - The key for packages is a full Java-language-style package name, it may contain uppercase or lowercase letters ('A' through 'Z'), numbers, and underscores (`_`): https://developer.android.com/guide/topics/manifest/manifest-element#package
    - it is already used in the metadata with `metadata/<id>.yml`
    - rational: the primary use case of the index is to distribute Android apps which have these restrictions already.
    - maybe extend this to all characters except `-`?
  - Use `versionCode` as the key in the versions dict.
    - this is not unique. Proposals for other things to use:
      - per-versionCode lists `{"<versionCode>": [{}, {}]}`
      - filename
      - sha256
* Handle `preferredSigner` https://gitlab.com/fdroid/fdroidserver/-/issues/153
* [Android Manifest elements](https://developer.android.com/guide/topics/manifest/manifest-element) are included in `manifest`
  * They are all transformed from XML to JSON using [the GData Convention](https://wiki.open311.org/JSON_and_XML_Conversion/#the-gdata-convention) (_xmltodict_ implements this).
  * The `http://schemas.android.com/apk/res/android` XML namespace prefix is dropped, (`android:maxSdkVersion=` -> `"maxSdkVersion":`).
  * Elements in any other namespaces are stripped out.
  * `fdroid update` includes allowlist of elements to include in the index.
  * The included elements are convertable back to XML, albeit without preserving code format or order.
  * This data maps to [android.content.pm.PackageInfo](https://developer.android.com/reference/android/content/pm/PackageInfo) and related classes.

```json
{
  "repo": {
    "name": "F-Droid",
    "icon": {
      "name": "fdroid-icon.png",
      "sha256": "b1f27fa87f8cabca50cdcd462a0f500d79d883b965a498d0e49eea560b39be1f",
      "size": 1234
    },
    "address": "https://f-droid.org/repo",
    "description": "\nThe official F-Droid Free Software repository.  Everything in this\nrepository is always built from the source code.\n",
    "mirrors": [
      {
        "url": "https://ftp.fau.de/fdroid/repo",
        "location": "de"
      },
      {
        "url": "https://mirror.cyberbits.eu/fdroid/repo",
      },
      {
        "url": "https://ftp.lysator.liu.se/pub/fdroid/repo",
        "location": "se"
      },
      {
        "url": "https://plug-mirror.rcac.purdue.edu/fdroid/repo",
        "location": "us"
      }
    ],
    "antiFeatures": {
      "Advertising": {
        "icon": {
          "name": "advertising-icon.png",
          "sha256": "b1f27fa87f8cabca50cdcd462a0f500d79d883b965a498d0e49eea560b39be1f",
          "size": 123
        },
        "description": {
          "en-US": "This Anti-Feature is applied to an app that contains advertising."
        }
      }
    },
    "categories": {
      "System": {
        "image": {
          "name": "category_games.png",
          "sha256": "b1f27fa87f8cabca50cdcd462a0f500d79d883b965a498d0e49eea560b39be1f",
          "size": 123
        },
        "description": {
          "en-US": "Apps for your System"
        }
      }
    }
  },
  "packages": {
    "org.fdroid.fdroid": {
      "metadata": {
        "categories": [
          "System"
        ],
        "packageName": "org.fdroid.fdroid",
        "changelog": "https://gitlab.com/fdroid/fdroidclient/raw/HEAD/CHANGELOG.md",
        "suggestedVersionCode": 1013051,
        "donate": "https://f-droid.org/donate",
        "flattrID": 343053,
        "issueTracker": "https://gitlab.com/fdroid/fdroidclient/issues",
        "liberapayID": 27859,
        "license": "GPL-3.0-or-later",
        "openCollective": "F-Droid-Euro",
        "sourceCode": "https://gitlab.com/fdroid/fdroidclient",
        "translation": "https://hosted.weblate.org/projects/f-droid/f-droid",
        "webSite": "https://f-droid.org",
        "added": 1295222400000,
        "localized": {
          "de": {
            "description": "F-Droid ist ein installierbarer Katalog mit Libre Software Android-Apps. \nDer F-Droid-Client erleichtert die App-Suche und -Installation \nund hält sie auf Ihrem Gerät aktuell.\n\nEr verbindet sich mit jeder mit F-Droid kompatiblen Paketquelle. Die \nStandardquelle wird auf f-droid.org gehosted, die ausschließflich echte \nfreie und quelloffene Software enthält.\n\nAndroid selbst ist dahingehend offen, dass jeder die Wahl hat, woher \ner APKs installieren möchte. Es gibt aber viele gute Gründe, F-Droid \nals App-Manager für freie Software zu verwenden:\n\n* Benachrichtigungen zu verfügbaren Aktualisierungen erhalten \n* Aktualisierungen wahlweise automatisch herunterladen und installieren \n* Vorgänger- und Beta-Versionen überblicken \n* Mit dem Gerät inkompatible Apps aussortieren\n* Apps nach Kategorien und durchsuchbaren Beschreibungen finden \n* Auf URLs zugreifen, die mit Quelltext, Spendenmöglichkeiten, usw. verknüpft sind \n* Durch Überprüfung von Quell-Signaturen und APK-Hashes geschützt bleiben\n",
            "name": "F-Droid",
            "summary": "Der App-Store, der Freiheit und Privatsphäre respektiert"
          },
          "en-US": {
            "description": "F-Droid is an installable catalogue of libre software\napps for Android. The F-Droid client app makes it\neasy to browse, install, and keep track of updates on your device.\n\nIt connects to any F-Droid compatible repositories. The default repo\nis hosted at f-droid.org, which contains only bona fide libre software.\n\nAndroid itself is open in the sense that you are free to install APKs\nfrom anywhere you wish, but there are many good reasons for using\nF-Droid as your libre software app manager:\n\n* Get notified when updates are available\n* Optionally download and install updates automatically\n* Keep track of older and beta versions\n* Filter out apps incompatible with the device\n* Find apps via categories and searchable descriptions\n* Access associated URLs for donations, source code etc.\n* Stay safe by checking repo index signatures and APK hashes\n",
            "featureGraphic": {
              "name": "featureGraphic_PTun9TO4cMFOeiqbvQSrkdcxNUcOFQCymMIaj9UJOAY=.jpg",
              "sha256": "b1f27fa87f8cabca50cdcd462a0f500d79d883b965a498d0e49eea560b39be1f",
              "size": 123
            },
            "name": "F-Droid",
            "screenshots": {
              "phone": [
                {
                  "name": "screenshot-app-details.png",
                  "sha256": "b1f27fa87f8cabca50cdcd462a0f500d79d883b965a498d0e49eea560b39be1f",
                  "size": 125
                },
                {
                  "name": "screenshot-dark-details.png",
                  "sha256": "9893a7da6d959b1a0024dfcbb4f515103471491d05596e5a138c639104d45b8a",
                  "size": 43
                }
              ]
            },
            "icon": {
              "name": "org.fdroid.fdroid.1014003.png",
              "sha256": "b1f27fa87f8cabca50cdcd462a0f500d79d883b965a498d0e49eea560b39be1f",
              "size": 124
            },
            "summary": "The app store that respects freedom and privacy"
          }
        }
      },
      "versions": {
        "1013051": {
          "added": 1628567668000,
          "file": {
            "name": "org.fdroid.fdroid_1013051.apk",
            "sha256": "9893a7da6d959b1a0024dfcbb4f515103471491d05596e5a138c639104d45b8a",
            "size": 8010257
          },
          "manifest": {
            "versionCode": 1013051,
            "versionName": "1.13.1",
            "package": "org.fdroid.fdroid",
            "uses-sdk": {
              "minSdkVersion": 22,
              "targetSdkVersion": 25
            },
            "uses-permission": [
              {
                "name": "android.permission.INTERNET"
              },
              {
                "name": "android.permission.ACCESS_NETWORK_STATE"
              },
              {
                "name": "android.permission.ACCESS_WIFI_STATE"
              },
              {
                "name": "android.permission.CHANGE_WIFI_MULTICAST_STATE"
              },
              {
                "name": "android.permission.CHANGE_NETWORK_STATE"
              },
              {
                "name": "android.permission.CHANGE_WIFI_STATE"
              },
              {
                "name": "android.permission.BLUETOOTH"
              },
              {
                "name": "android.permission.BLUETOOTH_ADMIN"
              },
              {
                "name": "android.permission.RECEIVE_BOOT_COMPLETED"
              },
              {
                "name": "android.permission.READ_EXTERNAL_STORAGE"
              },
              {
                "name": "android.permission.WRITE_EXTERNAL_STORAGE"
              },
              {
                "name": "android.permission.WRITE_SETTINGS"
              },
              {
                "name": "android.permission.NFC"
              },
              {
                "name": "android.permission.USB_PERMISSION",
                "maxSdkVersion": 22
              },
              {
                "name": "android.permission.WAKE_LOCK"
              },
              {
                "name": "android.permission.FOREGROUND_SERVICE"
              }
            ],
            "uses-permission-sdk-23": [
              {
                "name": "android.permission.ACCESS_COARSE_LOCATION"
              }
            ]
          },
          "signer": {
            "sha256": [
              "b1f27fa87f8cabca50cdcd462a0f500d79d883b965a498d0e49eea560b39be1f",
              "c35b2ac9428509f9c2906c1f32df64d5935114de968bc366f687ffaf6050a689"
            ]
          },
          "src": {
            "name": "org.fdroid.fdroid_1013051_src.tar.gz",
            "sha256": "9893a7da6d959b1a0024dfcbb4f515103471491d05596e5a138c639104d45b8a",
            "size": 64
          }
        },
        "1013050": {
          "added": 1625887690000,
          "packageName": "org.fdroid.fdroid",
          "file": {
            "name": "org.fdroid.fdroid_1013050.apk",
            "sha256": "c35b2ac9428509f9c2906c1f32df64d5935114de968bc366f687ffaf6050a689",
            "size": 8014353
          },
          "manifest": {
            "versionCode": 1013050,
            "versionName": "1.13",
            "package": "org.fdroid.fdroid",
            "uses-sdk": {
              "minSdkVersion": 22,
              "targetSdkVersion": 25
            },
            "uses-permission": [
              {
                "name": "android.permission.INTERNET"
              },
              {
                "name": "android.permission.ACCESS_NETWORK_STATE"
              },
              {
                "name": "android.permission.ACCESS_WIFI_STATE"
              },
              {
                "name": "android.permission.CHANGE_WIFI_MULTICAST_STATE"
              },
              {
                "name": "android.permission.CHANGE_NETWORK_STATE"
              },
              {
                "name": "android.permission.CHANGE_WIFI_STATE"
              },
              {
                "name": "android.permission.BLUETOOTH"
              },
              {
                "name": "android.permission.BLUETOOTH_ADMIN"
              },
              {
                "name": "android.permission.RECEIVE_BOOT_COMPLETED"
              },
              {
                "name": "android.permission.READ_EXTERNAL_STORAGE"
              },
              {
                "name": "android.permission.WRITE_EXTERNAL_STORAGE"
              },
              {
                "name": "android.permission.WRITE_SETTINGS"
              },
              {
                "name": "android.permission.NFC"
              },
              {
                "name": "android.permission.USB_PERMISSION",
                "maxSdkVersion": 22
              },
              {
                "name": "android.permission.WAKE_LOCK"
              },
              {
                "name": "android.permission.FOREGROUND_SERVICE"
              }
            ],
            "uses-permission-sdk-23": [
              {
                "name": "android.permission.ACCESS_COARSE_LOCATION"
              }
            ]
          },
          "signer": {
            "sha256": [
              "b1f27fa87f8cabca50cdcd462a0f500d79d883b965a498d0e49eea560b39be1f",
              "c35b2ac9428509f9c2906c1f32df64d5935114de968bc366f687ffaf6050a689"
            ]
          },
          "src": {
            "name": "org.fdroid.fdroid_1013050_src.tar.gz",
            "sha256": "c35b2ac9428509f9c2906c1f32df64d5935114de968bc366f687ffaf6050a689",
            "size": 3636
          }
        }
      }
    }
  }
}
```

## index diffs

* potential diff method and lib: https://github.com/OpenDataServices/json-merge-patch

### adding an app
```json
{
  "timestamp": 1641697189000,
  "packages": {
    "ws.xsoh.etar": {
      "metadata": {
        "categories": [
          "Time"
        ],
        "packageName": "ws.xsoh.etar",
        "changelog": "https://github.com/Etar-Group/Etar-Calendar/releases",
        "suggestedVersionCode": 28,
        "issueTracker": "https://github.com/Etar-Group/Etar-Calendar/issues",
        "license": "GPL-3.0-only",
        "sourceCode": "https://github.com/Etar-Group/Etar-Calendar",
        "translation": "https://hosted.weblate.org/projects/etar-calendar/strings/",
        "added": 1443830400000,
        "localized": {
          "en-US": {
            "description": "Features:\n- Month view.\n- Week, day and agenda view.\n- Uses Android calendar sync. Works with Google Calendar, Exchange, etc.\n- Material Design.\n- Dark and light theme.\n- Free, open source and absolutely no ads.\n- Support sharing calendars via ICS and the ability to import/export from SD card.\n- Multilingual UI.\n\nBugs and feedback: https://github.com/Etar-Group/Etar-Calendar/issues\nSource code: https://github.com/Etar-Group/Etar-Calendar\n",
            "featureGraphic": "featureGraphic_VR6kqRdJXrz_ZQbHQDDrk39071jRt4_A7LTz7UjYhUA=.png",
            "name": "Etar - OpenSource Calendar",
            "summary": "Etar is a material designed open source calendar, for everyone!",
            "screenshots": {
              "phone": [
                {
                  "name": "p1.png",
                  "sha256": "b1f27fa87f8cabca50cdcd462a0f500d79d883b965a498d0e49eea560b39be1f",
                  "size": 2445
                },
                {
                  "name": "p2.png",
                  "sha256": "9893a7da6d959b1a0024dfcbb4f515103471491d05596e5a138c639104d45b8a",
                  "size": 24
                }
              ],
              "tenInchScreenshots": [
                {
                  "name": "1.png",
                  "sha256": "b1f27fa87f8cabca50cdcd462a0f500d79d883b965a498d0e49eea560b39be1f",
                  "size": 345
                },
                {
                  "name": "2.png",
                  "sha256": "9893a7da6d959b1a0024dfcbb4f515103471491d05596e5a138c639104d45b8a",
                  "size": 35
                }
              ]
            }
          }
        }
      },
      "versions": {
        "28": {
          "added": 1626742335000,
          "packageName": "ws.xsoh.etar",
          "file": {
            "name": "ws.xsoh.etar_28.apk",
            "sha256": "6dfd11972653ab22a7d4a547420b1fc10a4c8f41c3d2571cc75957fcb4f168e3",
            "size": 5870473
          },
          "minSdkVersion": 21,
          "src": {
            "name": "ws.xsoh.etar_28_src.tar.gz",
            "sha256": "6dfd11972653ab22a7d4a547420b1fc10a4c8f41c3d2571cc75957fcb4f168e3",
            "size": 23
          },
          "targetSdkVersion": 30,
          "permissions": {
            "0": [
              "android.permission.WRITE_EXTERNAL_STORAGE",
              "android.permission.GET_ACCOUNTS",
              "android.permission.VIBRATE",
              "android.permission.READ_CONTACTS",
              "android.permission.READ_CALENDAR",
              "android.permission.WRITE_CALENDAR",
              "android.permission.WAKE_LOCK",
              "android.permission.READ_SYNC_SETTINGS",
              "android.permission.FOREGROUND_SERVICE",
              "android.permission.READ_EXTERNAL_STORAGE"
            ]
          },
          "versionName": "1.0.26",
          "versionCode": 28
        }
      }
    }
  }
}
```

### adding a new version
```json
{
  "packages": {
    "ws.xsoh.etar": {
      "metadata": {
        "suggestedVersionCode": 29
      },
      "versions": {
        "29": {
          "added": 1640708626000,
          "packageName": "ws.xsoh.etar",
          "file": {
            "name": "ws.xsoh.etar_29.apk",
            "sha256": "c48d7acfe5d923008e2674e6598f8aaed275ad91894133be931f228f0f02ede6",
            "size": 6103829
          },
          "minSdkVersion": 21,
          "src": {
            "name": "ws.xsoh.etar_29_src.tar.gz",
            "sha256": "c48d7acfe5d923008e2674e6598f8aaed275ad91894133be931f228f0f02ede6",
            "size": 74
          },
          "targetSdkVersion": 31,
          "permissions": {
            "0": [
              "android.permission.WRITE_EXTERNAL_STORAGE",
              "android.permission.GET_ACCOUNTS",
              "android.permission.VIBRATE",
              "android.permission.READ_CONTACTS",
              "android.permission.READ_CALENDAR",
              "android.permission.WRITE_CALENDAR",
              "android.permission.WAKE_LOCK",
              "android.permission.READ_SYNC_SETTINGS",
              "android.permission.FOREGROUND_SERVICE",
              "android.permission.READ_EXTERNAL_STORAGE"
            ]
          },
          "versionName": "1.0.27",
          "versionCode": 29
        }
      }
    }
  }
}
```

### add+archive version
```json
{
  "packages": {
    "ws.xsoh.etar": {
      "metadata": {
        "suggestedVersionCode": 30
      },
      "versions": {
        "28": null,
        "30": {
          "added": 1641342628000,
          "packageName": "ws.xsoh.etar",
          "file": {
            "name": "ws.xsoh.etar_30.apk",
            "sha256": "842937a439dd58df85b559f6b3f155415c217655264fb859b82a8b21efece9ff",
            "size": 6103829
          },
          "minSdkVersion": 21,
          "src": {
            "name": "ws.xsoh.etar_30_src.tar.gz",
            "sha256": "842937a439dd58df85b559f6b3f155415c217655264fb859b82a8b21efece9ff",
            "size": 356
          },
          "targetSdkVersion": 31,
          "permissions": {
            "0": [
              "android.permission.WRITE_EXTERNAL_STORAGE",
              "android.permission.GET_ACCOUNTS",
              "android.permission.VIBRATE",
              "android.permission.READ_CONTACTS",
              "android.permission.READ_CALENDAR",
              "android.permission.WRITE_CALENDAR",
              "android.permission.WAKE_LOCK",
              "android.permission.READ_SYNC_SETTINGS",
              "android.permission.FOREGROUND_SERVICE",
              "android.permission.READ_EXTERNAL_STORAGE"
            ]
          },
          "versionName": "1.0.28",
          "versionCode": 30,
          "whatsNew": {
            "en-US": "- Fix notifications on Android 12\n- Add DecSync and Birthday Adapter to \"known\" apps and don't crash\n"
          }
        }
      }
    }
  }
}
```

### archive app
```json
{
  "packages": {
    "org.fdroid.fdroid": null
  }
}
```

### test script
```python
import json
import json_merge_patch


def fix_index(old):
    new = {app["packageName"]: app for app in old["apps"]}
    for package, value in old["packages"].items():
        new[package]["versions"] = {v["versionCode"]: v for v in value}
    new["metadata"] = old["repo"]
    return new


old = json.load(open("old_index-v1.json"))
new = json.load(open("new_index-v1.json"))
old = fix_index(old)
new = fix_index(new)
j = json_merge_patch.create_patch(old, new)
json.dump(j, open("foo", "w"), indent=2)
```
