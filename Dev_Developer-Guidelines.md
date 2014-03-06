# Branches
In the [CodaLab Github Repo](http://github.com/codalab/codalab) there may be many branches. We want to be very careful about two specific branches to ensure the quality of the codebase stays high.

1. **production** - This is the gold standard codebase in production at any given time. It should be well tested and deployable to the production site http://www.codalab.org/ at any and all times.

2. **master** - This is the integration branch. This is the branch that has all of the work that is thought to have been completed but has not yet undergone final testing before deployment to production. master is where automated builds and QA happens.

The typical workflow for a contributor follows:

1. Go to https://github.com/codalab/codalab.
1. Fork the project using the button in the top right of the page and select your own GitHub account as the target of the fork.
1. Clone your forked repository:
```git clone git@github.com:<yourname>/codalab```
1. Develop on your branch, committing your changes:
```git commit -a -m "A meaningful description of my commit."```
1. Push your local changes to your fork:
```git push```

When the code in your GitHub fork is ready to make its way into the master branch of the CodaLab GitHub account, you should [submit a pull request](https://help.github.com/articles/using-pull-requests#initiating-the-pull-request). Be detailed in your description of the changes.

When you submit the pull request, GitHub will tell if the request cannot be merged automatically. This GitHub page explains [how to merge a pull request](https://help.github.com/articles/merging-a-pull-request).

**Other assumptions:**

* Committers will review pull requests and fold them into the master branch of the CodaLab account.
* Developers should ask for code reviews of their code when they feel like it's getting ready to pull into master. This is easily done by adding github id's to the message on the pull request (mentioning them sends a notification).
* If a change is going to affect the user experience, it should include code reviews/discussions (again through pull requests mentions) because this is a broad platform, intended for use by many diverse communities.
* Merges to master and deployment to production will be done by the project QA/Release Engineer (currently: Levert Banks)
* Secure data is being stored in a shared dropbox location. 

# Best practices

* DO read and follow [the process](https://github.com/codalab/codalab/wiki/Dev:-Issue-tracking) for managing tasks and bugs.
* DO associate your commit with the issue that you are fixing. The mechanism for doing so is explained in this [GitHub Blog entry](https://github.com/blog/831-issues-2-0-the-next-generation) and in this [StackOverflow question](http://stackoverflow.com/questions/1687262/link-to-github-issue-number-with-commit-message).
* DO ensure that the development environment runs on Windows and Linux. We want contributions from many developers irrespective of their favorite development platform.
* DO make sure to add unit tests to the `tests.py` file as you add new features.
* DO make sure to add unit tests to the `tests.py` file as you fix bugs that could have been fixed by unit testing.
* DO use PyLint (as documented at https://github.com/codalab/codalab/wiki/Dev:-Code-checkers) to validate your code matches this project's expectation of coding standards.