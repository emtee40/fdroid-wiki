NB: assumes signed APK from upstream named `upstream-release.apk`, and unsigned
APK from F-Droid CI named `fdroiddata-ci.apk`.

NB: also assumes a working directory in which it is okay to create temporary
files & directories.

## Links

* https://f-droid.org/docs/Reproducible_Builds/#potential-sources-of-unreproducible-builds
* https://reproducible-builds.org/docs/

## Prerequisites

### Tools

```bash
$ apt install aapt apksigner bat dexdump dos2unix unzip
```

Install [reproducible-apk-tools](https://github.com/obfusk/reproducible-apk-tools);
you can either use the scripts directly from the git repo (e.g. `zipinfo.py`) or
install the `repro-apk` Python package and use them as subcommands (e.g.
`repro-apk zipinfo`).

Install [`apksigcopier`](https://github.com/obfusk/apksigcopier).

Download [`fix-dexdump.sh`](https://gist.github.com/obfusk/c361c25d18c62dda2636615852af65c8).

### bash functions

```bash
bdiff() {
  # use bat for coloured output
  diff -Naur "$@" | bat -p -l diff
}
diff2c() {
  # diff running 2 different commands on the same file
  cmd_a="$1" cmd_b="$2" file="$3"
  diff -Naur <( $cmd_a "$file" ) <( $cmd_b "$file" ) | bat -p -l diff
}
diff2f() {
  # diff running the same command on 2 different files
  cmd="$1" file_a="$2" file_b="$3"
  diff -Naur <( $cmd "$file_a" ) <( $cmd "$file_b" ) | bat -p -l diff
}
```

## Preliminary investigation

### apksigcopier

```bash
# if this prints OK and does not show an error, we're good :)
$ apksigcopier compare upstream-release.apk --unsigned fdroiddata-ci.apk && echo OK
```

### zipinfo.py

NB: it is expected for only upstream's signed APK to have v1 (JAR) signature
files: `META-INF/MANIFEST.MF`, `META-INF/*.SF`, and `META-INF/*.RSA` (or
`.DSA`/`.EC`).

```bash
$ diff2f 'zipinfo.py -e' upstream-release.apk fdroiddata-ci.apk
[...]
```

NB: if upstream's APK has a file named `META-INF/BNDLTOOL.RSA`, it was almost
certainly built from an AAB by `bundletool`, which will not work with
reproducible builds; ensure upstream directly builds an APK, not an app bundle.

NB: we're using `zipinfo.py -e` here because, unlike the original `zipinfo`, it
also shows the CRC32, thus allowing us to see when a file differs in contents
only.

### diff-zip-meta.py

If the ZIP contents are equal, you can diff the ZIP metadata using
[`diff-zip-meta.py`](https://github.com/obfusk/reproducible-apk-tools#diff-zip-metapy).

### Unpacking the APKs

NB: creates directories `x` and `y`.

```bash
$ unzip -q -d x upstream-release.apk
$ unzip -q -d y fdroiddata-ci.apk
```

## ZIP ordering differences

Solution (upstream): either build using the CLI (not Android Studio) or use
Android Gradle plugin `7.1.X` or later.

Link: [Bug: Android Studio builds have non-deterministic ZIP ordering](https://f-droid.org/docs/Reproducible_Builds/#bug-android-studio-builds-have-non-deterministic-zip-ordering).

Google Issue Tracker: [non-deterministic order of ZIP entries in APK makes builds not reproducible](https://issuetracker.google.com/issues/265653160).

## Differences in specific files

**NB: first of all: please ensure the same commit is used for both builds.**

### Differing AndroidManifest.xml files (fix first)

[App Manifest](https://developer.android.com/guide/topics/manifest/manifest-intro)
compiled to Android binary XML.

NB: if these files are not the same, something is definitely wrong; are the APKs
really built from the same commit?

```bash
$ diff2f 'dump-axml.py' x/AndroidManifest.xml y/AndroidManifest.xml
[...]
```

### Differing .xml files in res/ (fix first)

[App resources](https://developer.android.com/guide/topics/resources/providing-resources)
compiled to Android binary XML.

NB: if these files are not the same, something is definitely wrong; are the APKs
really built from the same commit?

```bash
$ diff2f 'dump-axml.py' x/res/foo.xml y/res/foo.xml
[...]
```

### Differing resources.arsc (fix first)

Android package resource table.

NB: if these files are not the same (and you're using Android Gradle plugin
`3.4.X` or later), something is definitely wrong; are the APKs really built from
the same commit?

```bash
$ diff2f 'dump-arsc.py' x/resources.arsc y/resources.arsc
[...]
```

Solution for ordering differences (upstream): use Android Gradle plugin `3.4.X`
or later.

Link: [Reproducible APK tools](https://f-droid.org/docs/Reproducible_Builds/#reproducible-apk-tools).

Google Issue Tracker: [resources.arsc built with non-determism, prevents reproducible APK builds](https://issuetracker.google.com/issues/110237303).

### Differing .dex files (can be hard to fix)

Java/Kotlin classes compiled to Android bytecode.

NB: these differences can be hard to fix, depending on what caused them, so
please don't spend a lot of time trying to make `.dex` files equal when there
are e.g. differences in `.xml` files or `resources.arsc`, as the build will
never be reproducible if those are not fixed.

```bash
# repeat as needed for classes2.dex etc.
$ dexdump -a -d -f -h x/classes.dex > x/classes.dex.dump
$ dexdump -a -d -f -h y/classes.dex > y/classes.dex.dump
# make the diff a lot smaller :)
$ fix-dexdump.sh x/*.dex.dump y/*.dex.dump
```

```bash
# repeat as needed for classes2.dex etc.
$ bdiff x/classes.dex.dump y/classes.dex.dump
[...]
```

Ensure that the same JDK (usually that means OpenJDK 11) is used for both
builds.

Sometimes this works (but we're not sure why):

```diff
--- a/app/build.gradle
+++ b/app/build.gradle
     compileOptions {
-        sourceCompatibility JavaVersion.VERSION_1_8
-        targetCompatibility JavaVersion.VERSION_1_8
+        sourceCompatibility JavaVersion.VERSION_11
+        targetCompatibility JavaVersion.VERSION_11
     }
     kotlinOptions {
-        jvmTarget = '1.8'
+        jvmTarget = '11'
     }
```

Links:

* [Concurrency: reproducibility can depend on the number of CPUs/cores](https://f-droid.org/docs/Reproducible_Builds/#concurrency-reproducibility-can-depend-on-the-number-of-cpuscores);
* [Bug: coreLibraryDesugaring not deterministic](https://f-droid.org/docs/Reproducible_Builds/#bug-corelibrarydesugaring-not-deterministic).

Google Issue Tracker:

* [unneeded DEX code differences based on number of CPUs used in build process](https://issuetracker.google.com/issues/269181868);
* [Desugaring and reproducible builds](https://issuetracker.google.com/issues/195968520).

### Differing .so files (can be hard to fix)

Compiled native code.

NB: these can be some of the hardest differences to fix, so please don't spend a
lot of time trying to e.g. make build paths equal when there are differences in
`.xml` files or `resources.arsc`, as the build will never be reproducible if
those are not fixed.

Links:

* [Concurrency: reproducibility can depend on the number of CPUs/cores](https://f-droid.org/docs/Reproducible_Builds/#concurrency-reproducibility-can-depend-on-the-number-of-cpuscores);
* [Embedded build paths](https://f-droid.org/docs/Reproducible_Builds/#embedded-build-paths);
* [Native library stripping](https://f-droid.org/docs/Reproducible_Builds/#native-library-stripping);
* [NDK build-id](https://f-droid.org/docs/Reproducible_Builds/#ndk-build-id);
* [R8 Optimizer](https://f-droid.org/docs/Reproducible_Builds/#r8-optimizer).

### Differing assets/dexopt/baseline.prof (caused by .dex)

Compiled [baseline profile](https://developer.android.com/topic/performance/baselineprofiles/overview).

NB: these should only differ when the `.dex` files do (as a result of the
`.prof` file containing a checksum of the corresponding `.dex` files); any
differences in these files should disappear when the `.dex` files are made
equal.

```bash
$ diff2f 'dump-baseline.py' x/assets/dexopt/baseline.prof y/assets/dexopt/baseline.prof
[...]
```

### Differing assets/dexopt/baseline.profm (easy to fix)

Compiled [baseline profile](https://developer.android.com/topic/performance/baselineprofiles/overview) metadata.

NB: these may also differ as a result of `.dex` file differences, so please make
sure those are equal first.

```bash
$ diff2f 'dump-baseline.py' x/assets/dexopt/baseline.profm y/assets/dexopt/baseline.profm
[...]
```

Solution (upstream): [sort baseline.profm in build.gradle using com.android.tools.profgen](https://gist.github.com/obfusk/eb82a810ed6aad266dab19977b18cee6).

Link: [Bug: baseline.profm not deterministic](https://gist.github.com/obfusk/61046e09cee352ae6dd109911534b12e).

Google Issue Tracker: [Non-stable `assets/dexopt/baseline.profm` when rerun with --rerun-tasks](https://issuetracker.google.com/issues/231837768).

### Differences caused by LF vs CRLF (easy to fix)

NB: this most commonly affects `META-INF/services/*` files, but can affect other
files as well, e.g. `.css`/`.html`/`.js`/`.txt`.

These can have line ending differences if e.g. upstream's APK was built on
Windows.  If the diff of a pair of these files looks like this, it's an LF vs
CRLF issue:

```bash
$ bdiff x/META-INF/services/c6.l y/META-INF/services/c6.l
-y5.a
+y5.a
```

In which case using `unix2dos` on the file from the APK built on Linux should
fix it:

```bash
# diff should be empty now
$ bdiff x/META-INF/services/c6.l <( unix2dos < y/META-INF/services/c6.l )
```

Solution (fdroiddata):

```yaml
srclibs:
  - reproducible-apk-tools@v0.2.3
postbuild:
  - $$reproducible-apk-tools$$/inplace-fix.py --zipalign fix-newlines $$OUT$$
    'META-INF/services/*'
```

Google Issue Tracker: [newline differences between building on Windows vs Linux make builds not reproducible](https://issuetracker.google.com/issues/266109851).

### Differing .json file from the AboutLibraries Gradle plugin (easy to fix)

Link: [Embedded timestamps: AboutLibraries Gradle plugin](https://f-droid.org/docs/Reproducible_Builds/#aboutlibraries-gradle-plugin).

### Differing .png files (easy to fix)

PNG optimisation/generation is often not reproducible.

Links:

* [PNG Crush/Crunch](https://f-droid.org/docs/Reproducible_Builds/#png-crushcrunch);
* [PNGs generated from vector drawables](https://f-droid.org/docs/Reproducible_Builds/#pngs-generated-from-vector-drawables).

## If all else fails, try diffoscope

https://try.diffoscope.org/

```bash
$ apt install diffoscope
$ diffoscope --text diff.txt --text-color always upstream-release.apk fdroiddata-ci.apk
$ less -R diff.txt
```
