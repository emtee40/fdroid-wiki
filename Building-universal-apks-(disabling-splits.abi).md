To enable [auto update](https://f-droid.org/en/docs/Build_Metadata_Reference/#AutoUpdateMode), it's necessary to only build a universal apk, rather than splitting ABIs. To do so, add

```yaml
prebuild:
  - echo "android { splits { abi { enable false } } }" >> build.gradle
```

Or add a `fdroid` build type [[1]](https://github.com/SagerNet/SagerNet/commit/0e65f439c76db91adf2239008c6570f9695e5063) [[2]](https://github.com/SagerNet/SagerNet/commit/7dfdc7ff622c443406d22a688689483a491066ac):

```groovy

def targetAbi = ""
def targetTask = ""
if (!gradle.startParameter.taskNames.isEmpty()) {
    if (gradle.startParameter.taskNames.size == 1) {
        targetTask = gradle.startParameter.taskNames[0].toLowerCase()
        if (targetTask.contains("arm64")) {
            targetAbi = "arm64"
        } else if (targetTask.contains("arm")) {
            targetAbi = "arm"
            verCode -= 1
        }
    }
}

android {
    splits {
        abi {
            if (targetTask.startsWith("Fdroid")) {
                enable = false
            } else {
                enable = true
                universalApk = false
            }
        }
    }

    buildTypes {
        fdroid
    }
}
```