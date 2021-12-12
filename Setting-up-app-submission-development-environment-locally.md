# With Docker/Podman

1. Get `fdroiddata`
   ```shell
   git clone https://gitlab.com/fdroid/fdroiddata.git --depth=1
   ```
2. Run and attach to the container

   ```shell
   cd fdroiddata
   docker run --rm -i -t --entrypoint "/bin/bash" -v ${ANDROID_ROOT}:/opt/android-sdk:Z -v $(pwd):/build:z -e ANDROID_HOME:/opt/android-sdk registry.gitlab.com/proletarius101/ci-images-client:fdroid-build
   ```

3. Run the `fdroid` command you want. e.g.
   ```shell
   cd build
   $VAGRANT_HOME/fdroidserver/fdroid rewritemeta com.standardnotes
   ```

# Traditional way

1. Install Python 3 and Java 8/11 (depending on your app's requirement)
2. Install [cmdlinetools](https://developer.android.com/studio#command-tools) and Android SDKs (optional, only necessary when you need to run `fdroid build`
3. Get the code
   ```shell
   cd ~/code
   git clone https://gitlab.com/fdroid/fdroidserver.git --depth=1
   git clone https://gitlab.com/fdroid/fdroiddata.git --depth=1000
   ```
4. Install dependencies of `fdroidserver`
   ```shell
   pushd fdroidserver
   python3 setup.py install --user
   popd
   ```
5. Run the `fdroid` command you want. e.g.
   ```shell
   cd fdroiddata
   ../fdroidserver/fdroid rewritemeta com.standardnotes
   ```
