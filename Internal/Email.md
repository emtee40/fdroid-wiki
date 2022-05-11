# For F-Droid Contributors

Main email management is done by @TheLastProject. If you're an F-Droid contributor and want access to team@f-droid.org, please create an issue on https://gitlab.com/fdroid/admin/-/issues and assign it to @TheLastProject. In case @TheLastProject is not reachable, @eighthave also has complete admin access (he is the backup contact).

F-Droid email addresses are username@f-droid.org, but if wanted aliases like firstname.lastname@f-droid.org and firstname@f-droid.org can be added.

In [SoGo](https://hosted.mailcow.de/SOGo/), you will be able to send emails from your address, all of your assigned aliases and team@.

You can set up your F-Droid email address in a mail client of your choice using [the documentation provided by ServerCow](https://cp.servercow.de/en/plugin/support_manager/knowledgebase/view/13/imap-pop3-and-smtp-access/). If you prefer to receive your F-Droid email on your own email account, go to `Preferences`, `Mail`, `Forward` in SoGo and set up a forward.

Please note that your account currently comes with 1GB of storage, but these things may change. Please try to be mindful of storage usage and use an Inbox Zero workflow if possible.

# For Mailcow admins

_In this example, we'll have an user whose GitLab username is exampleuser who always wants to have the example alias._

When adding a new user:
1. Create a new mailbox with their GitLab username, generate a random password and ask the user to change this password ASAP
2. Create every requested alias and forward it to the new mailbox
3. Edit the team@, anyone@ and bot@ aliases and add the new mailbox (exampleuser@f-droid.org) to the Goto addresses. Please keep this list alphabetical for easier maintenance
4. Go back to the new user's mailbox and check if "Allow to send as" contains team@ and the user's requested aliases