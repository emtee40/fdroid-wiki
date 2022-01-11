# F-Droid Client tasks

A list of tasks an F-Droid client has to perform. These tasks could serve as a basis for factoring them out into dedicated modules/libraries with clear separation of concerns and interfaces, making them more testable and maintainable in the future.

## Low-level back-end tasks

### Downloading Index and other files like APKs and icons (with Mirroring)

   * IndexV1Updater
   * .net.`*`Http`*`

### Parsing/Representing Index

   * IndexV1Updater
   * .data.App
   * .data.Apk
   * .data.Repo **?**

### Storing Index and app info

   * .data.DbHelper
   * .data.RepoPersister
   * .data.`*`Provider
   * .data.Query`*`

Is there a reason why data is exposed as content providers? They are for internal use only and we don't need multi-process, so access could be simplified a lot, right?

## Higher-level tasks

### Install app

   * installer session
   * via priv ext
   * android 12 silent update

### Add a new repo

...more...?

