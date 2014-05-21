# Install and run the CLI tool on Windows
These instructions show you how to set up the bundle service and run the CodaLab CLI tool on Windows.

## Install the prerequisites
**Note:** If you have already taken the steps outlined in [Getting Started on Windows](https://github.com/codalab/codalab/wiki/Dev_Getting-Started-on-Windows), the only prerequsite you need is SQL Alchemy.

1. Install [Python 2.7](http://www.python.org/download/). Be sure to select the appropriate installer for your architecture. This typically will install to C:\Python27. The rest of these instructions will assume that you have installed to this path.

1. To Install pip you should first install distribute. Download the file at the following URL:
   http://python-distribute.org/distribute_setup.py and run it from a shell from the directory into which it was downloaded:

    ```
    C:\Python27\python.exe distribute_setup.py
    ```

    This will put easy_install.exe in the Scripts directory of your Python installation. Then, you're ready to install pip from a command prompt:
    ```
    C:\Python27\Scripts\easy_install.exe pip
    ```

1. Next install [virtualenv](http://www.virtualenv.org/)

    ```
    cd C:\Python27\Scripts
    pip install virtualenv
    ```
1. Activate virtual env:
    
    ```
    virtualenv codalab_env
    .\codalab_env\Scripts\activate
    ```

1. Use the following command to install MySql-Python.

    `easy_install mysql-python`

1. Install SQL Alchemy:
    
    `pip install sqlalchemy`

## Get the source code
1. Install the CodaLab CLI.

    `pip install codalab-cli`

## Local Bundle Server Testing
These are some rough notes since the process is kind of disjointed.

### Launching scripts
1. Make sure you have this: C:\Users\v-wfren\Documents\GitHub\codalab-cli\codalab\bin\cl.bat

    ```
    @echo off
    set CODALAB="C:\Users\v-wfren\Documents\GitHub\codalab-cli"
    
    python %CODALAB%\codalab\bin\codalab_client.py %CODALAB%\codalab\config\mysql_client_config.json "%*"
    ```

1. Create a PowerShell alias:

    ```
    Set-Alias -Name cl -Value "C:\Users\v-wfren\Documents\GitHub\codalab-cli\codalab\bin\cl.bat" -Description "Launch CodaLab CLI"
    ```
### Environment variables
**Note:** I added these commands to my virtual environment powershell helper script.
1. Set PYTHONPATH environment variable:
    [Environment]::SetEnvironmentVariable("PYTHONPATH", "C:\Users\v-wfren\Documents\GitHub\codalab-cli")

1. Set PYTHON_EGG_CACHE environment variable:
    [Environment]::SetEnvironmentVariable("PYTHON_EGG_CACHE", "C:\PEC")

### Configuration files
1. Add this to C:\Users\v-wfren\Documents\GitHub\codalab\codalab\codalab\settings\base.py:

```
# Add these to the top.
from pkgutil import extend_path
import codalab

    # This is already there, just update the URL.
    BUNDLE_SERVICE_URL = "http://localhost:2800"
    
    BUNDLE_SERVICE_CODE_PATH = "..\\..\\..\\..\\codalab-cli" # path to codalab-cli relative to this file
    if len(BUNDLE_SERVICE_CODE_PATH) > 0:
        sys.path.append(join(dirname(abspath(__file__)), BUNDLE_SERVICE_CODE_PATH))
        codalab.__path__ = extend_path(codalab.__path__, codalab.__name__)
```

1. Create codalab-cli\codalab\bin\bundle_server_config.json and add the following JSON:
```
{
  "home": "~/.codalab",
  "client": {
    "class": "LocalBundleClient"
  },
  "model": {
    "class": "MySQLModel",
    "username": "will",
    "password": "prax",
    "address": "localhost",
    "database": "MySQL_DevDB"
  },
  "server": {
    "address": ["localhost", 2800]
  }
}
```

1. Activate the `codalab_env` virtual environment.
1. Start the local bundle service:

    `python codalab_server.py bundle_server_config.json`

### Notes
- If you get an error message when accessing `http://127.0.0.1:8000/worksheets/` (access denied), make sure the bundle service is running.

- Mod for CLI tool on Windows:
C:\Users\v-wfren\Documents\GitHub\codalab-cli\codalab\lib\bundle_store.py, line 80:

    data_size = int(path_util.get_size(temp_path, dirs_and_files))

I just added the int function here, it seems to work now.
