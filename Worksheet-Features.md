Version 0.1.0 (master):
- Support for user time/disk quotas (`cl status` to show quotas and `cl uedit` to change if you're an admin)
- Support for writing to files inside a running bundle (usage: `cl write <target> "string to write"`)
- Support for graphing (usage: `% display graph <path-to-tsv-file> xlabel=... ylabel=...`)
- Switched uploading/downloading of bundles to use .tar.gz instead of .zip so that they can be streamed.
- Much nicer worksheet editing interface with syntax highlighting and keeping tracking of the current location.
- Added support for executing Jupyter/IPython notebooks.
- Upload directly from GitHub (`cl upload https://github.com/percyliang/fig --git`)
- Press `u` and `a` to paste uuid and arguments of current bundle into the web terminal, to make it easy to re-execute jobs