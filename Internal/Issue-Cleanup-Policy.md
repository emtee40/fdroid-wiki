* [General practises](#general-practises)
* [Closing duplicate issues](#closing-duplicate-issues)
* [Closing issues / MRs without feedback](#closing-issues-mrs-without-feedback)
* [Closing issues / MRs without upstream activity](#closing-issues-mrs-without-upstream-activity)

# General practises

* Use the gitlab threads or reply to button wherever possible to keep the discussions organised. See this for how to use [Reply to comment](https://docs.gitlab.com/ee/user/discussions/#start-a-thread-by-replying-to-a-standard-comment) button. Basically, for new comments, this button is available in the top right corner. For existing threads, use the thread's comment field to reply.
* The gitlab actions can be activated via [quick actions](https://docs.gitlab.com/ee/user/project/quick_actions.html#gitlab-quick-actions) too. These are called on commenting with `/command parameters`. Each quick action has to be **in a separate new line**. It can used/called be in a new comment too. These actions can be taken only by the memebers of the corresponding project/group. \
  Examples: `/close` , `/due in 4 weeks` , `/duplicate #451` etc.
* This page lists the comment format alongwith the text commands for the related actions. Some of these are given separate for better readability, but can be used within single comment too.

# Closing duplicate issues

* If you find an issue that is duplicate of other original issues, add the following comment:

```markdown
This issue is a duplicate issue of the <!-- link to original-issue --> . We request you to please abide by the instructions given in the issue template and search for the existing issues first. It consumes the team member's time to handle the duplicate issues.

/duplicate link-to-original-issue
```

# Closing issues / MRs without feedback

* When an issue needs further feedback to be processed further the commands `/label ~waiting-on-response` and `/due in 4 weeks` should be added. Additionally, in a comment, summarise who should provide a response.
* 4 weeks after the [waiting-on-response](/fdroid/wiki/-/issues?label_name=waiting-on-response) label was added (and there was no further activity), the following given commands should be called. Additionally, a comment in the following form should be added:

```markdown
Hi @author/@person-the-last-relevant-question-was-asked, 
currently, we're not able to get this issue done, as following questions aren't answered yet:

- <!-- Summary of the questions -->

Please help us through answering them.

If there is no activity on this issue in the next 4 weeks, we will close this issue to reduce the number of open issues.
On the [wiki](https://gitlab.com/fdroid/wiki/-/wikis/Internal/Issue-Cleanup-Policy) you can see more details about this process.

Thank you for contributing to F-Droid!

/label ~"candidate-to-close"
/due in 4 weeks
```

* If the questions are answered, or the issue gains otherwise activity, the [candidate-to-close](/fdroid/wiki/-/issues?label_name=candidate-to-close) label should be removed. The same goes, if a contributor has objections against the close of the issue. He can also remove the label, but should add a comment, on why this issue shouldn't be closed.
* After another 4 weeks without activity, the issue can be closed with the following statement:

```markdown
As mentioned above we close this issue due to missing activity.

If you think this issue is still relevant, please reopen it or create a new one (with the link to this one for history).

/close
```

# Closing issues / MRs without upstream activity

A similar approach goes for [waiting-for-upstream](/fdroid/wiki/-/issues?label_name=waiting-for-upstream):

* When an issue needs an upstream fix, the commands `/label ~waiting-for-upstream` and `/due in 4 weeks` should be called. Additionally, a comment with the upstream issue link should be added.
* 4 weeks after the label was added (and no activity in our and the upstream issue occurred), the following commands should be called. Additionally, a comment in the following form should be added:

```markdown
Hi @author/@person-who-needs-to-react, 
currently, we're not able to get this issue done, as the following issues aren't done yet:
- <!-- Link to upstream issue -->

If there is no activity on this or the upstream issue in the next 4 weeks, we will close this issue to reduce the number of open issues.
On the [wiki](https://gitlab.com/fdroid/wiki/-/wikis/Internal/Issue-Cleanup-Policy) you can see more details about this process.

Thank you for contributing to F-Droid!

/label ~"candidate-to-close"
/due in 4 weeks
```

* In the upstream issue a comment in the following form should be added:

```markdown
Hello <!-- optional @mention, suited to the project rules -->,

currently we cannot get <!-- link to our issue --> done, as this issue here isn't done yet.

If there is no activity here or in the F-Droid issue in the next 4 weeks, we will close our issue to reduce the number of open issues.
In our [wiki](https://gitlab.com/fdroid/wiki/-/wikis/Internal/Issue-Cleanup-Policy) you can see more details about this process.

Please contact us when you need help.

Thank you for helping us spreading FOSS apps in the world :)
```

* If the upstream issue gets fixed, or the issue (ours or upstream) gains otherwise activity, the [candidate-to-close](/fdroid/wiki/-/issues?label_name=candidate-to-close) label should be removed. The same goes, if a contributor has objections against the close of the issue. He can also remove the label, but should add a comment, on why this issue shouldn't be closed.
* After another 4 weeks without activity (here and upstream), the issue can be closed with the following statement:

```markdown
As mentioned above we close this issue due to missing activity.

If you think this issue is still relevant, please reopen it or create a new one (with the link to this one for history).

/close
```

* In the upstream issue a comment in the following form should be added:

```markdown
Hello,

we've just closed <!-- link to our issue --> as mentioned above.

We keep this issue here open, as it is still relevant. Please reopen our issue, or open a new one, if you want, after this issue here is done.

Of course you can contact us if you need help.

Thank you for helping us spreading FOSS apps in the world :)
```