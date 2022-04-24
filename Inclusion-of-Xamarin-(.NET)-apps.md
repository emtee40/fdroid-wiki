Due to technical limitations of building the whole toolchain from source by fdroid.org itself, it's unlikely that Xamarin apps would be able to included in f-droid.org. See https://gitlab.com/fdroid/fdroiddata/-/issues/1529.

## Workaround
You can publish a custom fdroid repo on GitHub pages, GitLab pages, or any static assets hosting platforms. Example: https://github.com/bitwarden/mobile/blob/master/.github/workflows/release.yml#L93...L175.

You may guide the user to add your repo to their fdroidclient with one tap. Example: https://mobileapp.bitwarden.com/fdroid/