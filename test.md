Dammit: For an overly quick (and senseless) test, I created this wiki page, only to discover, that wiki pages cannot be deleted per the Gitlab web-frontend.

O.K. thinking "I am computer savvy, even at 4 a.m.", I looked up [the documentation for deleting a wiki page](https://docs.gitlab.com/ee/api/wikis.html#delete-a-wiki-page) and tried<br />
`curl --request DELETE --header "PRIVATE-TOKEN: <my-freshly-generated-token>" "https://gitlab.com/api/v4/fdroid/wiki/-/wikis/test"`, which results in a `404 - not found`.

Alright, "tweaking the URL a bit should do the job":<br />
`curl --request DELETE --header "PRIVATE-TOKEN: <my-freshly-generated-token>" "https://gitlab.com/fdroid/wiki/-/wikis/test"` results in a proper HTML-response, containing
  ```
  <div class="container">
    <h3>The change you requested was rejected.</h3>
    <hr />
    <p>Make sure you have access to the thing you tried to change.</p>
    <p>Please contact your GitLab administrator if you think this is a mistake.</p>
    <a href="javascript:history.back()" class="js-go-back go-back">Go back</a>
  </div>
  ```
Sorry, I spoilt it: Created a nonsensical test page, which I fail to remove now.

Will continue to try fixing this (i.e., to delete this wiki page) the upcoming weekend.

* Friday evening<br />
  So I learnt, that Gitlab's access tokens have different scopes, [which can be set when creating them](https://gitlab.com/-/profile/personal_access_tokens).   The default scope is "all API accesses", which may not include what is needed for above non-404 operation.<br />
  Thus I created another access token with all four "via http" rights (read_repository, 
write_repository, read_registry, write_registry) and tried again: Exactly the same result as before!<br />
  One can also see in the [access token table](https://gitlab.com/-/profile/personal_access_tokens), when a token has been "Last Used".  Interestingly my original  "all API accesses" token saw accesses from trying before, while my new "via http" token does not ("Never", even though I used it ~ 15 minutes ago).<br />
  Giving up for today.