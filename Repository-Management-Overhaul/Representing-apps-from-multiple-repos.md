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
* Android security model would be followed (debatable): one app = one source of apps
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

We allow the user to re-order repo priorities as above, but in addition provide way to set a preferred repo per app. Only if one app is available from more than one repo, its app details screen highlights this information. The user can then switch this app to another repo and see the metadata provided there. The versions shown on that page (which are also considered for install/update) only come from the currently preferred repo, not from all repos as the case at the moment.

If a user has not set a preferred repo for an app, it is automatically determined by the repo priorities. If a user *has* set a preferred repo, this takes precedence over the repo priorities.

New repos get added with a *lower* priority than existing ones to prevent them overriding existing info without explicit user opt-in. This way users can easily get new apps by adding repos without dealing with the hard questions or having to know about repo priorities and their reordering.

### Pros

* solves all of the problems identified above:
  * made transparent that one app is in more than one repository
  * easy to find out where app information comes from
  * easy to change where app information comes from
  * new repos can not silently "override" information from other repos
  * updates for reproducible apps are installed only from the chosen repo (that was most likely used for install as well)

### Cons

* may make the UI more overloaded and thus confusing (only to people using other repos)

#### User selection flows

This is a list of all the things the user could do when working with repo priorities in the App Details screen:

| state | user action | result |
--------|-------------|--------|
| app not installed | user clicks install | prefer repo with highest global priority and install |
| app not installed | user selects repo then clicks install | prefer selected repo and install |
| app not installed | user selects repo then navigates away from App Details | reset to menu to preferred repo |
| installed with update available | user clicks update | use preferred repo and update |
| installed with update available | user selects repo then clicks update | prefer selected repo and update from it |
| installed with update available | user selects repo then navigates away from App Details | reset the menu to preferred repo |
| installed with no update available | user selects repo then clicks prefer | set preferred repo |
| installed with no update available | user selects repo then navigates away from App Details | reset to menu to preferred repo |

Other things to consider?
* App is updated in the background while user is on App Details for that app.
* Should the current state of the repo menu ever be stored?  Should it be force reset?

### Open Questions

* app details UI, should we use
  * tabs to switch between repos? Maybe have an indicator (star?) for preferred repo and a blue checkmark for the official F-Droid repo?
  * display current repo with its icon and add a button to switch to another repo which opens a dialog with the list of available repos for that app
  * use a dropdown to let users switch between different repos. then auto-set last selected as preferred?
* Should installing an app from one repo auto-set that repo as the preferred one, so updates only come from it as well?
* should we show (compatible) updates available from other repos? (separate bonus feature!)
  * in app details maybe add a badge to the tab for the repo with an update (may overload UI)
  * in updates tab, there could be a normal update item, but with special hint/text saying that this is not from the preferred repo
  * don't show system notification for updates in non-preferred repos
* when showing lists of apps, should we
  * mix information from multiple repos? (e.g. `intersect` anti-features and logical `or` compatibility) or
  * strictly take what current preferred repo has (easier to do and closer to how things currently work)
* when migrating repo priorities to the new earlier repo wins scheme, do we
  * leave existing priorities as they are, e.g. f-droid.org would stay with lowest priority for existing users, or
  * adapt existing repositories by reversing their priority, so repos added earlier will be prioritized