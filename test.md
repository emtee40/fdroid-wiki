Dammit: For an overly quick (and senseless) test, I crated this wiki page, only to discover, that wiki pages cannot be deleted per the Gitlab web-frontend.<br />
O.K. thinking "I am computer savvy, even at 4 a.m.", I looked up [the documentation for deleting a wiki page](https://docs.gitlab.com/ee/api/wikis.html#delete-a-wiki-page) and tried `curl --request DELETE --header "PRIVATE-TOKEN: abc123xyz" "https://gitlab.com/api/v4/fdroid/wiki/-/wikis/test"`, which results in a `404 - not found`.<br />
Alright, "tweaking the URL a bit should do the job": `url --request DELETE --header "PRIVATE-TOKEN: abc123xyz" "https://gitlab.com/fdroid/wiki/-/wikis/test"` results in a proper HTML-response, containing
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

Will continue to try fixing this the upcoming weekend.