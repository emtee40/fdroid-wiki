[[_TOC_]]

---

# Current approach

* repos have implicit/hidden priorities
* repos added later have a higher priority than older repos
* list of repositories is sorted by time, i.e. older repos with lower priority first
* if app is in more than one repo, app details show metadata from repo with highest priority
* other app metadata like compatibility with device, anti-features and categories is also taken from repo with highest priority
* app details shows versions from all repos
* version with the highest version code gets installed, no matter which repo it is in
* updates can come from any repo as long as the update has the same signature as what is installed

# Problems with current approach

* it isn't transparent that one app is in more than one repository
* it is hard to find out where app information (name, summary, icon, other metadata) comes from
* it is hard to change where app information comes from (needs removing and re-adding of repos in a certain order)
* repos can silently "override" information from other repos, e.g. a rogue repo can copy K-9 Mail info from F-Droid and just change donation links or deliver malicious versions for new installs
* updates for reproducible apps get auto-installed from one repo (e.g. repo of upstream dev) even though app was installed from F-Droid repo bypassing potential checks of F-Droid

# Considered Improvements

## Remove multi-repo feature

We completely remove the ability to add other repositories. One F-Droid client app is giving data from exactly one repo. At the same time, we make it very easy to create white-label versions of the client app with a custom repository pre-installed. Single apps could also (via a library) update themselves similar to how Signal (download version) does it.

### Pros

* complex client code with lots of edge cases would get simplified a lot, making maintenance easier as well
* Android security model would be followed: one app = one source of apps
* an entire problem space would go away though part of it would still exist outside of F-Droid, e.g. which app-repo takes priority (Android 14 update ownership helps with this)
* one-repo apps could be simpler (based on Basic?) and use upstream branding/colors/images

### Cons

* people that use many repos now need many apps, duplicated code on people's phones
* hard to migrate existing multi-repo installs to that
* many people are used to add repos in one single app
* in addition to publishing a repo, the publisher also needs to maintain an app and keep it up to date

## Allow changing repo priorities

In the list of repos, we allow the user to drag and drop repos to other positions ([#1887](https://gitlab.com/fdroid/fdroidclient/-/issues/1887)). As the position is the priority, this would allow users to at least change repo priorities. The list items could have a little handle indicating that they can be moved around. There could also be some onboarding for this after a user adds their first repo.

In the app details, we could show where the displayed information is coming from.

### Pros

* previously hard-coded (by time of adding) priorities could now be changed
* transparent where app info comes from

### Cons

* feature is hard to discover and hard to understand
* many of the problems with the current approach (see above) would still exist:
  * repos can still override information, though a bit less silent
  * updates would still get installed from any repo
* changing repo priorities is a coarse all or nothing approach especially for repos with many apps, e.g. user wants to prefer izzy repo only for one app, but get all others from F-Droid still

## Allow changing repo priorities globally and per app

We allow the user to re-order repo priorities as above, but in addition provide way to set a preferred repo per app. Only if one app is available from more than one repo, its app details screen highlights this information. The user can then switch this app to another repo. The versions shown on that page (which are considered for install/update) only come from the currently preferred repo.

If a user has not set a preferred repo for an app, it is determined by the repo priorities. If a user *has* set a preferred repo, this takes precedence over the repo priorities.

New repos get added with a *lower* priority than existing ones to prevent them overriding existing info without explicit user opt-in. This way users can easily get new apps by adding repos without dealing with the hard questions or having to know about repo priorities and their reordering.

### Pros

* solves all of the problems identified above

### Cons

* may make the UI more overloaded and thus confusing (only to people using other repos)

### Open Questions

* app details UI, should we use
  * tabs to switch between repos with indicator (star?) for preferred one or
  * display current repo with button/dialog to switch to over repo
* Should installing an app from one repo auto-set that repo as the preferred one, so updates only come from it as well?
* should we show updates available from other repos? (bonus feature)
  * app details maybe add a badge to tab for the repo with an update
  * in updates tab, there could be a special hint
  * don't show system notification
* when showing lists of apps, should we
  * mix information from multiple repos? (e.g. `union` categories, `intersect` anti-features, `or` compatibility) or
  * strictly take what current preferred repo has (easier to do and closer to how things currently work)