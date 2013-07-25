# Branches
In the [CodaLab Github Repo](http://github.com/codalab/codalab) there will be many branches. We only want to be careful about two specific branches to ensure the quality of the codebase stays high.

1. **production** - This is the gold standard codebase in production at any given time. It should be well tested and deployable to the production site http://www.codalab.org/ at any and all times.

2. **master** - This is the integration branch. This is the branch that has all of the work that is thought to have been completed but has not yet been tested and/or reviewed for deployment to production.

**Other assumptions:**

* Developers are responsible for ensuring they pull and merge from master into their working branches so that merges back into master are not disruptive.
* Developers should ask for code reviews of their code when they feel like it's getting ready to pull into master. This is easily done by adding github id's to the message on the pull request (mentioning them sends a notification).
* If a change is going to affect the user experience, it should include code reviews/discussions (again through pull requests mentions) because this is a broad platform, intended for use by many diverse communities.
* Merges to master and deployment to production will be done by the project QA/Release Engineer.