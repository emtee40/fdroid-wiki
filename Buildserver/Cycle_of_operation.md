
This process is split between two entirely isolated bare metal machines:

* BUILD is the _buildserver_ host machine where `build`, `update` and `deploy` are run
* PUBLISH is the signing server where `publish`, `gpgsign`, and `signindex` are run.


0. Generate _buildserver_ VM on BUILD (_~/control/makebs.sh_)
1. Run build cycle on BUILD (_~/control/build.sh_)
2. Sync all relevant files from BUILD to PUBLISH
3. Run `update` on BUILD to generate index (_~/control/postpublish.sh_)
4. Sync all relevant files PUBLISH back to BUILD
5. Push all updated files to webservers _~/control/postsignindex.sh_)
6. Repeat

This is maintained in:
https://gitlab.com/fdroid/fdroid-bootstrap-buildserver

