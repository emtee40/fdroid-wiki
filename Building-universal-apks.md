To enable [auto update](https://f-droid.org/en/docs/Build_Metadata_Reference/#AutoUpdateMode), it's necessary to only build a universal apk, rather than splitting ABIs. To do so, add

```yaml
prebuild:
  - echo "android { splits { abi { enable false } } }" >> build.gradle
```

Or add a `fdroid` [build type](https://developer.android.com/studio/build/build-variants):

```yaml
android {
    buildTypes {
        fdroid {
            splits {
                abi {
                    enable false
                }
            }
        }
    }
}
```