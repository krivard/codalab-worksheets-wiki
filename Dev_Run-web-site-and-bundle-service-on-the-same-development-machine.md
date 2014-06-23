This note will explain how to run the CodaLab Django web site and the CodaLab bundle service (cl server) side-by-side on a single machine. This setup is useful for people doing development, especially when working at the interface between the two systems.

As pre-requisite, we assume that:

- You can already run the Django web site on your local machine as described [on this page](https://github.com/codalab/codalab/wiki/Dev_Configure-Codalab-For-Development).

- You have also forked the [codalab-cli](https://github.com/codalab/codalab-cli) project and have gone through the steps listed in the [Readme](https://github.com/codalab/codalab-cli/blob/master/README.md).

With those assumptions in place:

1. Begin by enabling the Worksheet feature in your web site. In your Django settings (local.py), add:

   ```
    PREVIEW_WORKSHEETS = True
    BUNDLE_SERVICE_URL = "http://localhost:2800"
    # CODE_PATH points to local source code for bundles repo. Path is relative to this file.
    BUNDLE_SERVICE_CODE_PATH = "..\\..\\..\\..\\codalab-cli"
    if len(BUNDLE_SERVICE_CODE_PATH) > 0:
        sys.path.append(join(dirname(abspath(__file__)), BUNDLE_SERVICE_CODE_PATH))
        codalab.__path__ = extend_path(codalab.__path__, codalab.__name__)
   ```

   These additional elements say to enable Worksheets using the bundle service running at the given URL (in the next steps we'll cover how to start the bundle service with the `cl server` command). Finally, the code in `apps\web\bundles.py` will need to import modules from the `codalab-cli` project. Therefore, we extend the Python path by pointing to the CLI code on your machine. If you have repos `codalab` and `codalab-cli` checked in folders which are siblings, the relative `BUNDLE_SERVICE_CODE_PATH` given above should just work for your setup.

2. The Django web site is a client of the bundle service. For example, to get a list of worksheets, the Django web site makes a call to the `list_worksheets` API exposed by the bundle service. However, to handle authorization, the bundle service must be able to make calls back to the web site, which hosts the OAuth server. As a result, the bundle service is a trusted OAuth client of the web site. To setup this trusted relationship:

   a. Create a user on the web site. Typically, I call this user `codalab`. Treat it as an admin user even though the CodaLab site doesn't really have admin roles today.

   b. Run the script: `codalab\codalab\script\sample_cl_server_config.py`. The script will generate an output of the form:

      ```
      Checking that confidential client exists for user codalab

      Client already exists.

      Add the following server block to your CLI config:

      "server": {
          "auth": {
              "address": "http://localhost:8000",
              "app_id": "5m <snip> Le",
              "app_key": "_b <snip> !_",
              "class": "OAuthHandler"
          },
          "class": "SQLiteModel",
          "host": "localhost",
          "port": 2800
      }
      ```

   Take the "server" block in the output and insert it into your CLI config file (`.codalab\config.json` in your home directory).  

   c. Run the Django web site. Make sure it uses port 8000. Note for Visual Studio users: with the latest Python Tools for Visual Studio, you can set the port number in the Debug tab of the project properties.

   d. Run the bundle service: `cl server`.

