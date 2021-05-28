# This document is under construction, but should be considered a valuable resource

The f-droid project's translation is currently managed on:
https://hosted.weblate.org/projects/f-droid/

It's recommended to first translate the glossaries. Only translate the normal glossary, don't translate the 'US glossary'.


# Translation and Localization

Every piece of F-Droid can be localized, including the Android app, the entire website, and the tools. That means that all of the texts can be translated into any language supported by Android. F-Droid will also use localized formats for numbers, currency, dates, etc. which happens automatically based on the language and country specified in the device’s settings. All of the F-Droid resources are available for translation on Weblate “components” that are each managed separately, based on technical requirements.

If an app’s source code repo includes app descriptions, then those will be automatically included as long as they use the supported layouts. If there are any issues with missing translations or broken localized formats, please file an issue so it can be fixed.

## Contributing

Is F-Droid not in your language, or the translation is incorrect or incomplete? Get involved in the translations on our Weblate.

All translation work is done in Weblate to keep the workflow easy. We recommend that translators use a GitLab account for logging into Weblate since that same account can be used on all of the F-Droid projects on GitLab. you can find other translators on the forum.

### Adding a new language

First and foremost, Android must already support the specific language and locale you want to add. We cannot work with languages that Android and the SDK do not support, the tools simply break down. Next, if you are considering adding a country-specific variant of a language (e.g. de-AT), first make sure that the main language is well maintained (e.g. de). Your contribution might be useful to more people if you contribute to the existing version of your language rather than the country-specific variant.

For fdroidclient, fdroidserver and Repomaker, anyone can create a new language via Weblate. For the rest of the translation “components” on Weblate, please file an admin issue to request a new language.

### When translations are included

The translation files are included from Weblate before each release. There are varying conditions for when the translations are included, based on the limitations of each platform.

#### Android app

As a general rule, a translation must be at least 70% complete for it to be included in a final release. For alpha releases, we try to include all translations, no matter how incomplete.

#### App summaries

f-droid.org makes the one line summaries for many apps available for translation. The goal is to make it possible to have sections where only a single language is show, like the Latest Tab or the website sidebar. These are imported on a rolling basis, at least once a month.

#### f-droid.org website

For the f-droid.org website, it is a much more complicated system. Since it cannot (yet?) support lots of languages, we have to put stricter requirements on it. The core strings must be fully translated to be included. They also must be maintained in order to stay included. The documentation and tutorials are very useful to have translated. The blog posts are the least important. Each page must be fully translated before the translated version will be shown, otherwise, the site will show the original English.

In order for a new language to be added to f-droid.org, it must have:

    Website component: 100% translated
    Website Pages component: 100% translated
    Data component: at least 10% translated

There is a nightly build of the website that includes all translations on Weblate, no matter how complete they are. This lets everyone see the progress, and means we can get new languages working well before launching them.

## The old “Languages” preference

There are cases where having English as the fallback language is not helpful. For example, much of the world would prefer to fall back to Chinese, Spanish, or Russian instead of English. On Android 6.0 and older, F-Droid provides a “Languages” setting inside the app so that users can switch F-Droid to a different language. Note that this might not handle localized format properly, just the language. Android 7.0 fixed this by allowing users to specify a list of language+country “locales”, ordered by importance to the user.


## F-droid client
The client application is available in many languages. Should yours not be among those, or be in need of updates or improvement, please create an account and use the [translation system](https://hosted.weblate.org/projects/f-droid/) make your changes.

## Communication
We have a special category at our forum for translation:
https://forum.f-droid.org/c/translation/21

You can also contact us with the matrix protocol(you can use the [element client](https://element.io)), the channels are #fdroid:matrix.org and #fdroid-dev:f-droid.org