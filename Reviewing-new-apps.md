# Steps

- check whether the new app complies with the requirements set in the Merge Request template, excluding the optional fastlane metadata
- scan the apk via [VirusTotal](https://www.virustotal.com/) or similar suspicious file scanners to see if there are dangerous behaviors
- check whether the info in the metadata is correct
- if the app's repository contains fastlane metadata, remove `Summary` and `Description` from its F-Droid metadata since they will be pulled from the fastlane metadata

# Notes

- if the CI reports the following CI-specific issues, test the build in [a public buildserver instance](https://gitlab.com/fdroid/wiki/-/wikis/Buildserver/Public-buildserver-instances), or locally
  - No space on device
  - OOM