# Settings

The package django-configurations is employed to provide a class-based approach to Django settings. The manage.py and wsgi.py ( and perhaps a fcgi script at a later time ) have been modified to support the extra capability. 

The location of the settings are in codalab/codalab/settings/. There is a base and default set of settings. One can create a local.py which can override settings and then for deployment one would create a deploy.py file. To see how it is used the docs for configurations is here: https://django-configurations.readthedocs.org/en/latest/#usage-patterns 

There is a sample configuration file in settings/ named local_configuration_examples.py which demonstrates how to update the settings as needed for a particular environment. The base.py contains the majority of settings and would be updated when functionality is added and core packages need updating. If one wishes to test using other third-party Django apps it is recommended that a local.py file is created to add the apps so that the base.py file does not need to be updated.

The local.py and deploy.py files are never checked into the repository. For server deployments, the files need to be created with the appropriate settings and deployed separately from the repository.

# Configuration

A package named config_gen is used to templatize configuration files needed for deployment and testing. codalab/config/templates contains the templates for the configuration files. Some of the may be used, some of them may not be. One runs:

`python manage.py config_gen`

The templates expand settings that are defined in the settings files to build them. The location of the templates and the generated files is configurable with the settings: CONFIG_GEN_TEMPLATES_DIR and CONFIG_GEN_GENERATED_DIR

***

## Azure
A number of services such a Blob storage, SQL Azure and ServiceBus can be used with Django.

### SQL Azure 
SQL Azure is Microsoft SQL Server, but has some slightly different options for connecting to the DB.

#### Windows
Requirements:
* [Windows SQL Server driver](http://www.microsoft.com/en-us/download/details.aspx?id=36434)
* [PyODBC package](http://code.google.com/p/pyodbc/)
* [PyWin32](http://starship.python.net/crew/mhammond/win32/Downloads.html)
From the Azure management console (https://manage.windowsazure.com/) A SQL Database will need to be configured if one does not exist. The settings such as the server name, database name and user will be needed. Be mindful of the user name, which needs to be in the format, username@server (servername, not the fully qualified host name).

In the case of Windows Server 2012, select Tools->ODBC Datasources from the Tools tab. A driver needs to be setup if the SQL Server driver does not appear, then a Datasource, using one of the drivers, needs to be setup with the settings and credentials from setting up the database server, or the settings provided by a DBA managing the databases.

If a database has already been provisioned, these settings will need to be setup in a deploy.py or local.py configuration in codalab/codalab/settings/ with examples in codalab/codalab/settings/local_configuration_examples.py