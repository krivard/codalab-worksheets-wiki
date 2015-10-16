## Code design

The main components of the CodaLab CLI are as follows:

- Command-line interface (`bundle_cli.py`): main entry point, talks to a `BundleClient`.
  This should be the only code that prints to stdout and is command-line specific.

- `LocalBundleClient` and `RemoteBundleClient`.  All the functionality here
  should be used by the CLI and the website.  `LocalBundleClient` does most of the work;
  `RemoteBundleClient` mostly forwards its requests to the `LocalBundleClient`
  with a few exceptions which require more involved access to CodaLab
  (uploading/downloading files).

- `bundle_model.py`: in charge of updates to the database

- `bundle_store.py`: in charge of updates to the filesystem

- `work_manager.py`: manages workers/execution

[TODO]

Bundle hierarchy:

    Bundle
      NamedBundle
        UploadedBundle
          ProgramBundle
          DatasetBundle
        MakeBundle [DerivedBundle]
        RunBundle [DerivedBundle]
