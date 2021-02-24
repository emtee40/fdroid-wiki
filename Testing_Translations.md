

#### <tt>fdroidserver</tt> tools for repositories

It is possible to run a local, development copy of _fdroidserver_ in order to
test localizations while they are in progress.  This requires minimal fluency in
using the terminal, just like using _fdroidserver_ does.

```console
$ sudo apt-get install fdroidserver python3-babel
$ git clone https://gitlab.com/fdroid/fdroidserver.git
$ cd fdroidserver
$ ./setup.py compile_catalog
running compile_catalog
[..snip..]
compiling catalog locale/zh_Hant/LC_MESSAGES/fdroidserver.po to locale/zh_Hant/LC_MESSAGES/fdroidserver.mo
[..snip..]
$ export LC_ALL=zh_Hans.utf8  # optionally force the locale
$ ./fdroid signindex
2021-02-02 12:55:09,988 WARNING: No config.yml found, using defaults.
2021-02-02 12:55:09,990 CRITICAL: Missing output directory 'repo'
$ editor locale/zh_Hant/LC_MESSAGES/fdroidserver.po
$ ./setup.py compile_catalog
running compile_catalog
[..snip..]
compiling catalog locale/zh_Hant/LC_MESSAGES/fdroidserver.po to locale/zh_Hant/LC_MESSAGES/fdroidserver.mo
[..snip..]
$ ./fdroid signindex
```
