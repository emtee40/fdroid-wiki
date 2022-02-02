# With Docker/Podman

1. Get `fdroiddata` & `fdroidserver`
   ```shell
   git clone https://gitlab.com/fdroid/fdroiddata.git --depth=1
   git clone https://gitlab.com/fdroid/fdroidserver.git --depth=1
   ```
2. Run and attach to the container

   ```shell
   docker run --rm -i -t --entrypoint "/bin/bash" -v ${ANDROID_SDK_ROOT}:/opt/android-sdk:Z -v $(pwd)/fdroidserver:/home/vagrant/fdroidserver:Z -v $(pwd)/fdroiddata:/build:z registry.gitlab.com/fdroid/fdroidserver:buildserver
   source /etc/profile.d/bsenv.sh
   export GRADLE_USER_HOME=$home_vagrant/.gradle
   export fdroid="sudo --preserve-env --user vagrant
          env PATH=$fdroidserver:$PATH
          env PYTHONPATH=$fdroidserver:$fdroidserver/examples
          env PYTHONUNBUFFERED=true
          env TERM=$TERM
          env HOME=$home_vagrant
          fdroid"
   ```

3. Run the `fdroid` command you want. e.g.
   ```shell
   cd build
   export build=com.standardnotes
   $fdroid fetchsrclibs $build --verbose
   $fdroid rewritemeta $build
   $fdroid build --verbose --latest --scan-binary --on-server --no-tarball $build
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
