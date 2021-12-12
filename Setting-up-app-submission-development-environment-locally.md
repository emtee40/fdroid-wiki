1. Install Python 3 and Java 8/11 (depending on your app's requirement)
2. Install [cmdlinetools](https://developer.android.com/studio#command-tools) and Android SDKs (optional, only necessary when you need to run `fdroid build`
3. Get the code
   ```
   cd ~/code
   git clone https://gitlab.com/fdroid/fdroidserver.git
   git clone https://gitlab.com/fdroid/fdroiddata.git
   ```
4. Install dependencies of `fdroidserver`
   ```
   pushd fdroidserver
   python3 setup.py install --user
   popd
   ```
5. Run the `fdroid` command you want. e.g.
   ```
   cd fdroiddata
   ../fdroidserver/fdroid rewritemeta com.standardnotes
   ```
