This is an attempt by @uniqx to model trust in F-Droid and figure out how to drive decentralization while keeping everything that makes F-Droid great.


## Status quo

Currently when it comes to installing apps available in multiple repositories
F-Droids behavior isn't well communicated to users. It's based on repository
priority, with additional rules for reproducible apps. It feels
organically grown, not designed. With some competing assumptions, most notably:

 * F-Droid only ships trustworthy and reviewed apps guaranteed to be fully FOSS
 * Everyone can take a couple of APKs from the web and start their own F-Droid repo/app store


## Trust perception - app vs. repository

F-Droids branding doesn't reflect the border between the repository on
f-droid.org/fdroiddata and the F-Droid app. So F-Droid can really mean 2
things: A de-central app store where everyone can host repositories without any
controlling instance or central platform where the FOSS community publishes
Android apps. These two things have fundamentally different security/safety
properties.

From personal experience I think F-Droid's growth mostly relies on these
channels:
* FOSS and privacy activists (Crypto-party, Free your Android, ...)
* Word of mouth
* Media coverage
All these channels I take it are primarily informed by their own
experiences/reviews of the Software and Services provided by F-Droid and
maybe also by F-Droids official announcements. I think to keep this dynamic up
F-Droid merely needs to stay true to it's core values. (FOSS and privacy by
design).


## Trust perception - up-/down-stream

Since since FOSS software distribution is hardly a topic of broad interest,
I'm assuming most users are not familiar with the terms up- and down-stream.
These terms describe the two main roles in FOSS supply chains. Up-stream are
the owners/authors/maintainers of a specific software project, who publish
releases in the form of source code under a FOSS license. Down-stream
on the other hand catalogs/curates all project up-streams, builds actually
use-able binaries for the source code and distributes those binaries to users
in a secure and easy to consume way with some additional guarantees.

The interests of Up-stream/App-Devs are not always identical to user interests,
especially when it comes to monetization of apps. In the context of the
f-droid.org repository of apps, these conflicts manifest in the form of
Anti-Feature warnings, or even the refusal to package apps because of inclusion
policy violations. So down-streams are not only a distribution channel
but can also serve as a player fending for user rights. (Debian and F-Droid
examples for this, but there are also counter examples like Windows Store,
Google Play or Apples App Store, which are primarily interested in monetizing
users.)


## Trust perception - reproducible builds

The concept of repositories and reproducible builds is not easy to understand
for people without technical background. My observation is that they tend to
perceive apps/tech mostly in terms of brands. I.e. they trust or distrust
Apple, Google, Microsoft, Android, Chromium, Samusung, Facebook, Twitter, etc.
instead of actual artifacts, like a specific version of an Android app running
on their phone.

In the context of F-Droid reproducible builds are a form of proof that an APK
corresponds to publicly available source code of that app. 


## Trust Model

So based on my experience and considerations I'll now try come up with a model
for trust and group all factors that are key to trust decisions of users into
dimensions.


### Involved parties

users - people interested in using/installing/updating apps. Should be assumed
to have little to no technical background.

up-stream - individuals, companies, communities, non-profits, etc. who
develop/publish/maintain one or more apps. In case of apps in the f-droid.org
repository they also who provide a source-code up-stream.

down-stream - individuals, companies, communities, non-profits, etc. who publish
one or more apps. The technical artifact produced by down-stream is referred to
as repository containing apps from more than one up-stream and they also can be
seen as platforms, with all the pro and cons of platforms.


### trust dimension 1: app listing

Selecting an app over other competing apps which offer the same or very similar
use cases is the first leap of trusts users have to take.

Right now F-Droid aids this trust decision by providing some key information:

* developer supplied representation (icons, store-listing texts, etc.)
* technical aspects (permissions, update frequency, known vulnerabilities, privacy properties, etc.)
* down-stream review results (anti-feature warnings)
* legal framework (license)

Of course there are also other aspects which are not part of F-Droids UX,
contributing to trust decisions of users. E.g. brand reputation, expert/user
reviews and ratings, advertisement, legality of content/features of an app,
privacy policy, terms of service, etc.


### trust dimension 2: repository

Well maintained Down-stream repositories come with policies that regulate
what the apps they're curating/platforming. These policies add additional
checks and balances on top of the governance of up-stream. For users this
can add a very convenient way to quickly establish meaningful trust, based
on the vetting procedures, track record, etc. of a down-stream. So basically
down-stream is building their brand in large part based on their
trustworthiness. Down-stream repositories might drop some apps because
up-stream insists on problematic changes, went proprietary, stopped
maintenance, etc. 

On the flip side repository maintainers have absolute control over all the
information in the app listing. They can leave out, spin or falsify key
information for trust decisions deliberately, negligently, for lack of
resources, etc. So here is where having an app shipped by more than on
repository and comparing the repository listings for differences is a good way
for users to assess trustworthiness of up- and down-streams.

When an app is signed by down-stream, down-stream maintainers may also alter an
app before shipping it. Additionally up-stream may provide a custom build
flavor for a specific down-stream, eg. to comply with down-stream policies.
This in turn means that a specific version of an app can have very different
properties depending on the distribution channel.

On the main f-droid.org repository down-stream changes and build flavors are
usually about removing or disabling undesired features, dependencies and fixing
security issues. F-Droid client currently abstracts this trust choice by
establishing a priority of repositories and signing keys and always preferring
to install from the repository with the highest priority and withing that
repository prefer apps signed by up-stream.


### trust dimension 3: multi-build support (signing key)

Repositories support holding multiple variations of one specific version of an
app. When this feature is used in the main f-droid.org repository these
variations typically are built reproducible and their only difference is the
signature on the APK file. This allows shipping updates for a version of an app
signed by both up- and down-stream maintainers. (Technically this also allows
unrelated 3rd parties to re-build apps and become a witness of whether an APK
file in fact corresponds to the public available source code, in a
crypto-graphically verify-able manner.)

Right now for the main f-droid.org repository this is setup in a way that if
multiple variations are available, F-Droid Client will always automatically
pick those signed with the up-stream maintainers signing key.


## Design goals

Now I'll try to come up with some UX and architecture requirements for aiding
trust decisions of users and implementing trust in a decentralized app store.

But before I do that, I'd like to outline an additional design requirement that
I think is important and often left out in the broader FOSS community.

* Provide contextual information whenever users need to make a choice.
* Stick to platform specific UI patterns. It should possible for non-technical
  users to predict the consequences of their interaction with the UI, based
  on their experience with platform specific UI patterns and the information
  they've picked up on the way to the screen they're currently looking at.
* Never require users to understand, technicalities for making choices. At
  least minimize them as much as possible.
* Design in the interest of users, don't nudge, don't do dark patterns.
* Be transparent and open, don't omit technical details when the could be
  interesting to a subset of users. Just make sure they're never in the way of
  non-technical users getting their optimal UX.

To get into the right frame of mind for this kind of work I like to assume
designing for non-technical users. And I think every Software Developer should
acknowledge that non-technical people are not just too stupid to use software.
To exemplify this I'd like to to consider the following: Physicians are usually
very capable, well trained, intelligent and I guess privileged persons. We
literally rely on their profession in life and death situations. When they
struggle to use your app, it's not them, it's probably your app. When you think
about it, it's not really limited to this profession.


### app listing UX requirements

I do think F-Droid is doing fairly well on representing apps. However enabling
down-stream maintainers to displaying some additional information could ease
trust decisions for users:

* Provide a link (or maybe even a privacy friendly reader) for allowing users
  to study privacy policies of apps before installing them.
* group permissions, by whether they are automatically granted or require user
  permission (dangerous permissions).
* Provide additionl of down-stream maintainer notes, which are less severe then
  anti-feature warnings. e.g. when an app contains risks or newsenses like:
  paid features, ToS violation (which could get your Google, Facebook, ...
  account suspended), legal risks (like file sharing), high resouce consumption
  of apps, etc.


### repository/down-stream UX requirements

I think F-Droid could do better on representing Repositories. Currently they
are hidden away in settings like config options.

* re-brand the main f-droid.org repository to make it easy to distinguish
  between the F-Droid app and the repository. (doesn't have to be a huge
  rebrand, it's just important to make it easy to refer to the main
  f-droid.org repository of apps e.g. F-Droid Central, F-Droid Downstream,
  etc.)
* The F-Droid app should present repositories in a similar fashion like it
  presents app-listings. Repository maintainers should get ample space for
  introducing themselves and their repository.
* display compact repository branding when looking at app details (maybe an
  material chip?) in the app details. Tapping it should allow users to learn
  more about a repository. eg. by opening the previously mention repo-listing
  screen.
* I think F-Droid should convey a basic understanding of what up- and
  down-streams are by merely by using our app. I think key to this is by
  keeping up good app descriptions, and also adding good descriptions/listings
  for repositories.
* Think when installing an app and there are multiple sources available F-Droid
  should let them pick which down-stream they want to trust. (When users aren't
  sure what they're choosing there and don't care, F-Droid could suggest a
  reasonable choice.)
* I think F-Droid client should make clear visual distinctions between
  representing up- and down-stream repositories. The main point here is to make
  it easy for users to grasp that there's a difference between these kind of
  repositories. This will hopefully give them a head-start when they have to
  consider putting trust into any of these.

On a side note, I think more visibility of down-stream brands would also help
3rd party repositories to build their brand. When they grow, the F-Droid
ecosystem grows too.


### update channel/reproducible builds UX

On Android, app-signing plays an important role for updates. In order to
install an update, it has to be signed by same signing-key as the currently
installed old version of the app.

* I think F-Droid should always prefer the signing keys of repository the user
  chose as trust source for this every specific install. This guarantees to
  most narrow scope of trust: up-stream is shipping updates in compliance with
  the policies of down-stream.

  I think F-Droids current approach to always prefer up-stream signatures when
  installing reproducible apps is not great. It effectively removes
  down-streams from the chain of trust. While this enables users to
  transparently upgrade to up-stream version of an app, as previously pointed
  out, up-stream and down-stream versions of apps can have very different trust
  properties. 
* visualize reproducible builds as a sort of notarization. (eg: This version of
  Wikipedia has been verified by 3 reproducible builds by these repositories:
  f-droid.org, wikipedia.org, example.com)

#### federated repository architecture

* enable fdroidserver to consume other fdroid repositories for discovering
  reproducible builds. (e.g. what's now known as the `Binaries:` metadata field
  could be converted into a reference to an f-droid repository) This actually
  shouldn't be a single entry, but a list of F-Droid repositories. If
  reproducible, fdroidserver should at the very least a archive a copy of
  all APK-signatures for every reproducible build. This allows users, researches,
  etc. to verify reproducibility claims.
* Provide a simple to install/run/maintain tool-chain for app-developers to run
  their own fdroid repo. (maybe even integrated with popular CI tool-chains) The
  fdroiddata team should recommend hosting their own F-Droid repos to up-stream
  devs. This will hopefully bootstrap a truly de-central ecosystem of up- and
  down-streams for doing reproducible builds.

#### federated repository UX

* The web-app running on fdroid.link and the F-Droid app should find have a 

* I'm not sure how to deal with the "Get it on F-Droid". I think it's important
  to make sure that we have a link format capable of navigating federated
  repositories. So it might make sense to also define a link format that can
  link to apps outside of the main f-droid.org repository. Alternatively
  F-Droid could also maintain an index of repositories and suggest to add
  repositories for apps that are not in the main f-droid.org repository.

#### repository safety UX

I think currently it's too easy to add malicious repositories to the F-Droid
app. I think currently F-Droids main safeguard is, that the user-base is to
small a target for bad actors.

* Always warn users that apps from 3rd party repositories are not checked for
  anti-features, trackers, malware, etc.
* I think F-Droid should start a registry of 3rd party repositories and comment
  (or omit warnings) on security/safety, user freedom, privacy, ... for 3rd party
  repositories (think anti-features, think TLS-CAs)

And while this might be might unpopular with the community and might dampen the
aspirations of decentralization, I do think it's very important for creating a
safe UX for all levels of users, to explicitly warn about the risks of using
3rd partiy repositories.


#### architecture and UX summary

All the requirements above combined will hopefully create a federated system of
up-/down-stream and maybe also independent notary/verification repositories
linked by reproducible builds. This system then will allow establishing way
stronger trust guarantees then currently available systems of software
distribution.