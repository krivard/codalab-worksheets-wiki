Version 0.1.2 (master):
- Worksheets officially decoupled from Competitions
- Improved performance on worksheets web client with precompiled JSX
- Web terminal now prints usage help
- Disabled autocomplete for `cl` command aliases
- Bugfixes for [cli#287](https://github.com/codalab/codalab-cli/issues/287), [cli#307](https://github.com/codalab/codalab-cli/issues/307), [cli#330](https://github.com/codalab/codalab-cli/pull/330), [worksheets#32](https://github.com/codalab/codalab-worksheets/pull/32)


Version 0.1.1 (master):
- Improved behavior and robustness for editable fields on website
- Better support for long commands on web terminal
- Change worksheets on website without reloading application
- Uploading bundles on website no longer uses modal

Version 0.1.0 (master):
- Support for user time/disk quotas (`cl status` to show quotas and `cl uedit` to change if you're an admin)
- Support for writing to files inside a running bundle (usage: `cl write <target> "string to write"`)
- Support for graphing (usage: `% display graph <path-to-tsv-file> xlabel=... ylabel=...`)
- Switched uploading/downloading of bundles to use .tar.gz instead of .zip so that they can be streamed.
- Much nicer worksheet editing interface with syntax highlighting and keeping tracking of the current location.
- Added support for executing Jupyter/IPython notebooks.
- Upload directly from GitHub (`cl upload https://github.com/percyliang/fig --git`)
- Press `u` and `a` to paste uuid and arguments of current bundle into the web terminal, to make it easy to re-execute jobs