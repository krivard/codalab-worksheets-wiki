Version 0.1.6:
 - Migrate user tables from the Django DB to the Bundles DB, get rid of Django DB.
 - Migrate the REST APIs from DJANGO to the new REST server.
 - Refactoring of download logic.
 - New Run button
 - Chatbox backend

Version 0.1.5:
 - Implement saving bundles to multiple disks
 - Add a script to check bundle store sanity (scripts/sanity-check-bundlestore.py)
 - Add the --allow-failed-dependencies flag to the run command.
 - Bugfixes for [worksheets#103](https://github.com/codalab/codalab-worksheets/issues/103), [cli#345](https://github.com/codalab/codalab-cli/issues/345), 

Version 0.1.4:
- Bugfixes for [cli#339](https://github.com/codalab/codalab-cli/pull/341), [worksheets#85](https://github.com/codalab/codalab-worksheets/issues/85)

Version 0.1.3:
- Store bundles in directories named using the UUID, instead of data hash.

Version 0.1.2:
- Worksheets officially decoupled from Competitions
- Improved performance on worksheets web client with precompiled JSX
- Web terminal now prints usage help
- Disabled autocomplete for `cl` command aliases
- Bugfixes for [cli#287](https://github.com/codalab/codalab-cli/issues/287), [cli#307](https://github.com/codalab/codalab-cli/issues/307), [cli#330](https://github.com/codalab/codalab-cli/pull/330), [worksheets#32](https://github.com/codalab/codalab-worksheets/issues/32), [worksheets#26](https://github.com/codalab/codalab-worksheets/issues/26)


Version 0.1.1:
- Improved behavior and robustness for editable fields on website
- Better support for long commands on web terminal
- Change worksheets on website without reloading application
- Uploading bundles on website no longer uses modal

Version 0.1.0:
- Support for user time/disk quotas (`cl status` to show quotas and `cl uedit` to change if you're an admin)
- Support for writing to files inside a running bundle (usage: `cl write <target> "string to write"`)
- Support for graphing (usage: `% display graph <path-to-tsv-file> xlabel=... ylabel=...`)
- Switched uploading/downloading of bundles to use .tar.gz instead of .zip so that they can be streamed.
- Much nicer worksheet editing interface with syntax highlighting and keeping tracking of the current location.
- Added support for executing Jupyter/IPython notebooks.
- Upload directly from GitHub (`cl upload https://github.com/percyliang/fig --git`)
- Press `u` and `a` to paste uuid and arguments of current bundle into the web terminal, to make it easy to re-execute jobs