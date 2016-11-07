Version 0.2.0 (November 7, 2016) 
- **Backwards-incompatible changes, please upgrade your CLI with `git pull`!**
- Migrated last remaining APIs to the new REST API and retired the old XML-RPC API.
  - _CodaLab server administrators_: you no longer need to run `cl rest-server`.
  - Public documentation on the API will be made available soon.
- Removed remaining support for the connecting to `local` on the CLI. If you want to do CodaLab work entirely on your machine, you must [set up your own server processes](https://github.com/codalab/codalab-worksheets/wiki/Server-Setup).
- Faster upload through both the website ([#196](https://github.com/codalab/codalab-worksheets/issues/196)) and the CLI ([#599](https://github.com/codalab/codalab-cli/issues/599)).
- CLI notifies user when a new version is available.
- Bug fixes for [worksheets#216](https://github.com/codalab/codalab-worksheets/issues/216), [worksheets#211](https://github.com/codalab/codalab-worksheets/issues/211), [worksheets#164](https://github.com/codalab/codalab-worksheets/issues/164), [cli#326](https://github.com/codalab/codalab-cli/issues/326), [cli#575](https://github.com/codalab/codalab-cli/issues/575)

Version 0.1.11 (September 3, 2016)
- Automatically notify admins by email when unexpected errors occur
- Automatically refresh unfinished bundles (not 'ready' or 'failed')
- Bug fixes: run bundle button only loads bundles when they are drilled down

Version 0.1.10 (August 7, 2016)
- Add bundle contents API, with improvements to upload/download speed from the CLI
- Allow addition of tags to bundles with `cl edit --tags`
- New Docker image for Torch
- Add colors to setup script
- Bug fixes: login issues, public worksheets, `uedit` disk command

Version 0.1.9 (July 9, 2016)
- Simplify worksheets page, layout of the metadata, and default bundle fields.
- Consolidate setup/update into `./setup.sh` and simplify deployment process.
- Fix monitor script.

Version 0.1.8 (May 19, 2016)
 - Add ability to change passwords.
 - New worker system.
 - Fix copy and paste errors on Mac OS.
 - Speed-up loading of a worksheet

Version 0.1.7 (Apr. 21, 2016)
 - Bring back the My Account page and password reset link removed during the user table migration.
 - Refactoring of upload logic.
 - Log last login
 - Bug fix for private bundles
 - Bug fixes for [worksheets#130](https://github.com/codalab/codalab-worksheets/issues/130), [worksheets#133](https://github.com/codalab/codalab-worksheets/issues/133)

Version 0.1.6 (Mar. 16, 2016):
 - Migrate user tables from the Django DB to the Bundles DB, get rid of Django DB.
 - Migrate the REST APIs from DJANGO to the new REST server.
 - Refactoring of download logic.
 - New Run button
 - New Worksheet button
 - Chatbox backend
 - More sane handling of symbolic links

Version 0.1.5 (Mar. 4, 2016):
 - Implement saving bundles to multiple disks
 - Add a script to check bundle store sanity (scripts/sanity-check-bundlestore.py)
 - Add the --allow-failed-dependencies flag to the run command.
 - Bugfixes for [worksheets#103](https://github.com/codalab/codalab-worksheets/issues/103), [cli#345](https://github.com/codalab/codalab-cli/issues/345), 

Version 0.1.4 (Feb. 22, 2016):
- Bugfixes for [cli#339](https://github.com/codalab/codalab-cli/pull/341), [worksheets#85](https://github.com/codalab/codalab-worksheets/issues/85)

Version 0.1.3 (Feb. 20, 2016):
- Store bundles in directories named using the UUID, instead of data hash.
- Makeover of front page to make it easier for people to figure out what CodaLab is

Version 0.1.2 (Feb. 13, 2016):
- Worksheets officially decoupled from Competitions
- Improved performance on worksheets web client with precompiled JSX
- Web terminal now prints usage help
- Disabled autocomplete for `cl` command aliases
- Bugfixes for [cli#287](https://github.com/codalab/codalab-cli/issues/287), [cli#307](https://github.com/codalab/codalab-cli/issues/307), [cli#330](https://github.com/codalab/codalab-cli/pull/330), [worksheets#32](https://github.com/codalab/codalab-worksheets/issues/32), [worksheets#26](https://github.com/codalab/codalab-worksheets/issues/26)


Version 0.1.1 (Dec. 8, 2015):
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