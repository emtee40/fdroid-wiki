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

## Drop multi-repo feature

We completely remove the ability to add other repositories. One F-Droid client app is giving data from exactly one repo. At the same time, we make it very easy to create white-label versions of the client app with a custom repository pre-installed.

### Pros

* complex client code with lots of edge cases would get simplified a lot, making maintenance easier as well
* Android security model would be followed: one app = one source of apps
* an entire problem space would go away
* one-repo apps could be simpler (based on Basic?) and use upstream branding/colors/images

### Cons

* people that use many repos now need many apps, duplicated code on people's phones
* hard to migrate existing multi-repo installs to that
* many people are used to add repos in one single app
* in addition to publishing a repo, the publisher also needs to maintain an app and keep it up to date


