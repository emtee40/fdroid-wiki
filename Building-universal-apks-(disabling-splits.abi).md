To enable [auto update](https://f-droid.org/en/docs/Build_Metadata_Reference/#AutoUpdateMode), it's necessary to only build a universal apk, rather than splitting ABIs. To do so, add

```yaml
prebuild:
  - echo "android { splits { abi { enable false } } }" >> build.gradle
```

Or add a `fdroid` build type [[1]](https://github.com/SagerNet/SagerNet/commit/0e65f439c76db91adf2239008c6570f9695e5063) [[2]](https://github.com/SagerNet/SagerNet/commit/7dfdc7ff622c443406d22a688689483a491066ac):

```groovy
android {
    splits {
        abi {
            if (requireFlavor().startsWith("Fdroid")) {
                isEnable = false
            } else {
                isEnable = true
                isUniversalApk = false
            }
        }
    }

    buildTypes {
        fdroid
    }
}
```