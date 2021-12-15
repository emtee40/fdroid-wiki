When an app requires Android SDK 31+, it may fail:

```shell

> Failed to find Platform SDK with path: platforms;android-31
```

Or

```shell
Exception in thread "main" java.lang.NoClassDefFoundError: javax/xml/bind/annotation/XmlSchema
	at com.android.repository.api.SchemaModule$SchemaModuleVersion.<init>(SchemaModule.java:156)
	at com.android.repository.api.SchemaModule.<init>(SchemaModule.java:75)
	at com.android.sdklib.repository.AndroidSdkHandler.<clinit>(AndroidSdkHandler.java:81)
	at com.android.sdklib.tool.sdkmanager.SdkManagerCli.main(SdkManagerCli.java:73)
	at com.android.sdklib.tool.sdkmanager.SdkManagerCli.main(SdkManagerCli.java:48)
Caused by: java.lang.ClassNotFoundException: javax.xml.bind.annotation.XmlSchema
	at java.base/jdk.internal.loader.BuiltinClassLoader.loadClass(BuiltinClassLoader.java:581)
	at java.base/jdk.internal.loader.ClassLoaders$AppClassLoader.loadClass(ClassLoaders.java:178)
	at java.base/java.lang.ClassLoader.loadClass(ClassLoader.java:521)
	... 5 more
```

That's due to we are [still using the legacy Android SDK tool](https://gitlab.com/fdroid/ci-images-base/-/blob/master/Dockerfile#L75). To use the new command line tools in the app recipe, add these lines

```yaml
      - wget -O /tmp/cmdline-tools.zip -t 5 "https://dl.google.com/android/repository/commandlinetools-linux-7583922_latest.zip"
      - echo "124f2d5115eee365df6cf3228ffbca6fc3911d16f8025bebd5b1c6e2fcfa7faf /tmp/cmdline-tools.zip"  |
        sha256sum -c -
      - unzip -q -o /tmp/cmdline-tools.zip -d /tmp/cmdline-tools
      - rm /tmp/cmdline-tools.zip
      - mkdir -p ${ANDROID_SDK_ROOT}/cmdline-tools/tools
      - mv /tmp/cmdline-tools/cmdline-tools/* ${ANDROID_SDK_ROOT}/cmdline-tools/tools
        || true
      - export PATH=${ANDROID_SDK_ROOT}/cmdline-tools/tools/bin:${PATH}
      - yes | sdkmanager --licenses
      - sdkmanager "build-tools;30.0.3" "build-tools;31.0.0" "platforms;android-31"
```

This issue will persist before we switch to the new build container image.