We are creating a new index format to solve a lot of issue and improve things.  Track progress here:

* https://gitlab.com/groups/fdroid/-/issues?scope=all&state=opened&label_name[]=index-v2
* https://gitlab.com/groups/fdroid/-/merge_requests?scope=all&state=opened&label_name[]=index-v2

Weekly Meeting: Tuesdays, 1300 UTC on #fdroid-dev and https://meet.calyx.net/fdroid

## New index format
```json
{
  "repo": {
    "timestamp": 1641697189000,
    "version": 20001,
    "maxage": 14,
    "name": "F-Droid",
    "hashType": "sha256",
    "icon": "fdroid-icon.png",
    "icon-hash": "b1f27fa87f8cabca50cdcd462a0f500d79d883b965a498d0e49eea560b39be1f",
    "address": "https://f-droid.org/repo",
    "description": "\nThe official F-Droid Free Software repository.  Everything in this\nrepository is always built from the source code.\n",
    "mirrors": [
      "https://ftp.fau.de/fdroid/repo",
      "https://mirror.cyberbits.eu/fdroid/repo",
      "https://fdroid.tetaneutral.net/fdroid/repo",
      "https://ftp.lysator.liu.se/pub/fdroid/repo",
      "https://plug-mirror.rcac.purdue.edu/fdroid/repo"
    ],
    "antiFeatures": {
      "Advertising": {
        "icon": "advertising-icon.png",
        "icon-hash": "b1f27fa87f8cabca50cdcd462a0f500d79d883b965a498d0e49eea560b39be1f",
        "localized": {
          "en-US": "This Anti-Feature is applied to an app that contains advertising."
        }
      }
    }
  },
  "apps": {
    "org.fdroid.fdroid": {
      "metadata": {
        "categories": [
          "System"
        ],
        "changelog": "https://gitlab.com/fdroid/fdroidclient/raw/HEAD/CHANGELOG.md",
        "suggestedVersionCode": "1013051",
        "donate": "https://f-droid.org/donate",
        "flattrID": "343053",
        "issueTracker": "https://gitlab.com/fdroid/fdroidclient/issues",
        "liberapayID": "27859",
        "license": "GPL-3.0-or-later",
        "openCollective": "F-Droid-Euro",
        "sourceCode": "https://gitlab.com/fdroid/fdroidclient",
        "translation": "https://hosted.weblate.org/projects/f-droid/f-droid",
        "webSite": "https://f-droid.org",
        "added": 1295222400000,
        "icon": "org.fdroid.fdroid.1014003.png",
        "icon-hash": "b1f27fa87f8cabca50cdcd462a0f500d79d883b965a498d0e49eea560b39be1f",
        "localized": {
          "de": {
            "description": "F-Droid ist ein installierbarer Katalog mit Libre Software Android-Apps. \nDer F-Droid-Client erleichtert die App-Suche und -Installation \nund h\u00e4lt sie auf Ihrem Ger\u00e4t aktuell.\n\nEr verbindet sich mit jeder mit F-Droid kompatiblen Paketquelle. Die \nStandardquelle wird auf f-droid.org gehosted, die ausschlie\u00dfflich echte \nfreie und quelloffene Software enth\u00e4lt.\n\nAndroid selbst ist dahingehend offen, dass jeder die Wahl hat, woher \ner APKs installieren m\u00f6chte. Es gibt aber viele gute Gr\u00fcnde, F-Droid \nals App-Manager f\u00fcr freie Software zu verwenden:\n\n* Benachrichtigungen zu verf\u00fcgbaren Aktualisierungen erhalten \n* Aktualisierungen wahlweise automatisch herunterladen und installieren \n* Vorg\u00e4nger- und Beta-Versionen \u00fcberblicken \n* Mit dem Ger\u00e4t inkompatible Apps aussortieren\n* Apps nach Kategorien und durchsuchbaren Beschreibungen finden \n* Auf URLs zugreifen, die mit Quelltext, Spendenm\u00f6glichkeiten, usw. verkn\u00fcpft sind \n* Durch \u00dcberpr\u00fcfung von Quell-Signaturen und APK-Hashes gesch\u00fctzt bleiben\n",
            "name": "F-Droid",
            "summary": "Der App-Store, der Freiheit und Privatsph\u00e4re respektiert"
          },
          "en-US": {
            "description": "F-Droid is an installable catalogue of libre software\napps for Android. The F-Droid client app makes it\neasy to browse, install, and keep track of updates on your device.\n\nIt connects to any F-Droid compatible repositories. The default repo\nis hosted at f-droid.org, which contains only bona fide libre software.\n\nAndroid itself is open in the sense that you are free to install APKs\nfrom anywhere you wish, but there are many good reasons for using\nF-Droid as your libre software app manager:\n\n* Get notified when updates are available\n* Optionally download and install updates automatically\n* Keep track of older and beta versions\n* Filter out apps incompatible with the device\n* Find apps via categories and searchable descriptions\n* Access associated URLs for donations, source code etc.\n* Stay safe by checking repo index signatures and APK hashes\n",
            "featureGraphic": "featureGraphic_PTun9TO4cMFOeiqbvQSrkdcxNUcOFQCymMIaj9UJOAY=.jpg",
            "name": "F-Droid",
            "phoneScreenshots": {
              "screenshot-app-details.png": "b1f27fa87f8cabca50cdcd462a0f500d79d883b965a498d0e49eea560b39be1f",
              "screenshot-dark-details.png": "9893a7da6d959b1a0024dfcbb4f515103471491d05596e5a138c639104d45b8a"
            },
            "summary": "The app store that respects freedom and privacy"
          }
        }
      },
      "versions": {
        "1013051": {
          "added": 1628567668000,
          "apkName": "org.fdroid.fdroid_1013051.apk",
          "apkName-hash": "9893a7da6d959b1a0024dfcbb4f515103471491d05596e5a138c639104d45b8a",
          "minSdkVersion": 22,
          "size": 8010257,
          "srcname": "org.fdroid.fdroid_1013051_src.tar.gz",
          "srcname-hash": "9893a7da6d959b1a0024dfcbb4f515103471491d05596e5a138c639104d45b8a",
          "targetSdkVersion": 25,
          "permission": {
            "0": [
              "android.permission.INTERNET",
              "android.permission.ACCESS_NETWORK_STATE",
              "android.permission.ACCESS_WIFI_STATE"
            ],
            "22": [
              "android.permission.USB_PERMISSION"
            ],
            "23": [
              "android.permission.ACCESS_COARSE_LOCATION"
            ]
          },
          "versionName": "1.13.1"
        },
        "1013050": {
          "added": 1625887690000,
          "apkName": "org.fdroid.fdroid_1013050.apk",
          "apkName-hash": "c35b2ac9428509f9c2906c1f32df64d5935114de968bc366f687ffaf6050a689",
          "minSdkVersion": 22,
          "size": 8014353,
          "srcname": "org.fdroid.fdroid_1013050_src.tar.gz",
          "srcname-hash": "c35b2ac9428509f9c2906c1f32df64d5935114de968bc366f687ffaf6050a689",
          "targetSdkVersion": 25,
          "permission": {
            "0": [
              "android.permission.INTERNET",
              "android.permission.ACCESS_NETWORK_STATE",
              "android.permission.ACCESS_WIFI_STATE",
              "android.permission.FOREGROUND_SERVICE"
            ],
            "22": [
              "android.permission.USB_PERMISSION"
            ],
            "23": [
              "android.permission.ACCESS_COARSE_LOCATION"
            ]
          },
          "versionName": "1.13"
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
  "apps": {
    "ws.xsoh.etar": {
      "metadata": {
        "categories": [
          "Time"
        ],
        "changelog": "https://github.com/Etar-Group/Etar-Calendar/releases",
        "suggestedVersionCode": "28",
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
            "phoneScreenshots": [
              "p1.png",
              "p2.png",
              "p3.png",
              "p4.png",
              "p5.png",
              "t1.png",
              "t2.png",
              "t3.png",
              "t4.png",
              "t5.png"
            ],
            "summary": "Etar is a material designed open source calendar, for everyone!",
            "tenInchScreenshots": [
              "1.png",
              "2.png",
              "3.png",
              "4.png",
              "5.png"
            ],
            "whatsNew": "- Fix notifications on Android 12\n- Add DecSync and Birthday Adapter to \"known\" apps and don't crash\n"
          }
        }
      },
      "versions": {
        "28": {
          "added": 1626742335000,
          "apkName": "ws.xsoh.etar_28.apk",
          "hash": "6dfd11972653ab22a7d4a547420b1fc10a4c8f41c3d2571cc75957fcb4f168e3",
          "minSdkVersion": 21,
          "size": 5870473,
          "srcname": "ws.xsoh.etar_28_src.tar.gz",
          "targetSdkVersion": 30,
          "permission": {
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
          "versionName": "1.0.26"
        }
      }
    }
  }
}
```

### adding a new version
```json
{
  "apps": {
    "ws.xsoh.etar": {
      "metadata": {
        "suggestedVersionCode": "30"
      },
      "versions": {
        "29": {
          "added": 1640708626000,
          "apkName": "ws.xsoh.etar_29.apk",
          "hash": "c48d7acfe5d923008e2674e6598f8aaed275ad91894133be931f228f0f02ede6",
          "minSdkVersion": 21,
          "size": 6103829,
          "srcname": "ws.xsoh.etar_29_src.tar.gz",
          "targetSdkVersion": 31,
          "permission": {
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
          "versionName": "1.0.27"
        }
      }
    }
  }
}
```

### add+archive
```json
{
  "apps": {
    "ws.xsoh.etar": {
      "versions": {
        "28": null,
        "30": {
          "added": 1641342628000,
          "apkName": "ws.xsoh.etar_30.apk",
          "hash": "842937a439dd58df85b559f6b3f155415c217655264fb859b82a8b21efece9ff",
          "minSdkVersion": 21,
          "size": 6103829,
          "srcname": "ws.xsoh.etar_30_src.tar.gz",
          "targetSdkVersion": 31,
          "permission": {
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
          "versionName": "1.0.28"
        }
      }
    }
  }
}
```

### archive app
```json
{
  "apps": {
    "org.fdroid.fdroid": null
  }
}
```
