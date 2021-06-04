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
  So I learnt, that Gitlab's access tokens have different scopes, [which can be set when creating them](https://gitlab.com/-/profile/personal_access_tokens).  The default scope is "all API accesses", which may not include what is needed for above non-404 operation.<br />
  Thus I created another access token with all four "via http" rights (read_repository, 
write_repository, read_registry, write_registry) and tried again: Exactly the same result as before!<br />
  One can also see in the [access token table](https://gitlab.com/-/profile/personal_access_tokens), when a token has been "Last Used".  Interestingly my original  "all API accesses" token saw accesses from trying before, while my new "via http" token does not ("Never", even though I used it ~ 15 minutes ago).  *Edit (Saturday): [These values are updated every 24 hours](https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html#view-the-last-time-a-token-was-used).*<br />
  Giving up for today.
* Saturday<br />
  Browsing the Gitlab documentation for something helpful, I found [the API syntax for addressing project resources](https://docs.gitlab.com/ee/api/api_resources.html#project-resources), [how to read and set a Gitlab wiki's settings](https://docs.gitlab.com/ee/administration/wikis/index.html), [how to change Gitlab's application settings](https://docs.gitlab.com/ee/api/settings.html#change-application-settings), and reread [how to delete a wiki page](https://docs.gitlab.com/ee/api/wikis.html#delete-a-wiki-page).<br />
  So I came up with `curl --request DELETE --header "PRIVATE-TOKEN: <my-new-http-token>" "https://gitlab.com/api/v4/projects/fdroid/wikis/test"`, which resulted in `{"error":"insufficient_scope","error_description":"The request requires higher privileges than provided by the access token.","scope":"api read_api"}`!<br />
  Egerly I executed a `curl --request DELETE --header "PRIVATE-TOKEN: <my-old-freshly-generated-token>" "https://gitlab.com/api/v4/projects/fdroid/wikis/test"`, but that just returns a `{"error":"404 Not Found"`, again (as before).<br />
  Interestingly I receive a `{"message":"404 Project Not Found"}`, when retrying this command!?!<br />
  Giving up for today.