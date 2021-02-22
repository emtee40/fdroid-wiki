# Closing issues / MRs without feedback

- When an issue needs further feedback to be processed further the `waiting-on-response` label should be added to an issue. Additionally, a comment should be added, which summarizes who should provide a response.
- 4 weeks after the `waiting-on-response` label was added (and there was no further activity), the `candidate-to-close` label should be added. Additionally, a comment in the following form should be added:
```
Hi @author/@person-the-last-relevant-question-was-asked, 
currently, we're not able to get this issue done, as following questions aren't answered yet:
- <Summary of the questions>
Please help us through answering them.

If there is no activity on this issue in the next 4 weeks, we will close this issue to reduce the number of open issues.
On the [wiki](https://gitlab.com/fdroid/wiki/-/wikis/Internal/Issue-Cleanup-Policy) you can see more details about this process.

Thank you for contributing to F-Droid!
```
- If the questions are answered, or the issue gains otherwise activity, the `candidate-to-close` label should be removed. The same goes, if a contributor has objections against the close of the issue. He can also remove the label, but should add a comment, on why this issue shouldn't be closed.
- After another 4 weeks without activity, the issue can be closed with the following statement:
```
As mentioned above we close this issue due to missing activity.

If you think this issue is still relevant, please reopen it or create a new one (with the link to this one for history).
```

## Closing issues / MRs without upstream activity

A similar approach goes for `wait-for-upstream`:

- When an issue needs an upstream fix, the `wait-for-upstream` label should be added. Additionally, a comment with the upstream issue link should be added.
- 4 weeks after the label was added (and no activity in our and the upstream issue occurred), the `candidate-to-close` label should be added. Additionally, a comment in the following form should be added:
 
```
Hi @author/@person-who-needs-to-react, 
currently, we're not able to get this issue done, as the following issues aren't done yet:
- <Link to upstream issue>

If there is no activity on this or the upstream issue in the next 4 weeks, we will close this issue to reduce the number of open issues.
On the [wiki](https://gitlab.com/fdroid/wiki/-/wikis/Internal/Issue-Cleanup-Policy) you can see more details about this process.

Thank you for contributing to F-Droid!
```
- In the upstream issue a comment in the following form should be added:
```
Hello <optional @mention, suited to the project rules>,

currently we cannot get <link to our issue> done, as this issue here isn't done yet.

If there is no activity here or in the F-Droid issue in the next 4 weeks, we will close our issue to reduce the number of open issues.
In our [wiki](https://gitlab.com/fdroid/wiki/-/wikis/Internal/Issue-Cleanup-Policy) you can see more details about this process.

Please contact us when you need help.

Thank you for helping us spreading FOSS apps in the world :)
```

- If the upstream issue gets fixed, or the issue (ours or upstream) gains otherwise activity, the `candidate-to-close` label should be removed. The same goes, if a contributor has objections against the close of the issue. He can also remove the label, but should add a comment, on why this issue shouldn't be closed.
- After another 4 weeks without activity (here and upstream), the issue can be closed with the following statement:
```
As mentioned above we close this issue due to missing activity.

If you think this issue is still relevant, please reopen it or create a new one (with the link to this one for history).
```
- In the upstream issue a comment in the following form should be added:
```
Hello,

we've just closed <link to our issue> as mentioned above.

We keep this issue here open, as it is still relevant. Please reopen our issue, or open a new one, if you want, after this issue here is done.

Of course you can contact us if you need help.

Thank you for helping us spreading FOSS apps in the world :)
```