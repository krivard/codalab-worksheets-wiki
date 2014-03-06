The CodaLab project uses South ([South Docs](http://south.readthedocs.org/en/latest/index.html)) for migrations.

When changes are made to the models a migration will need to be created to capture the change and allow it to be applied. In the simplest case, such as adding fields and tables:

`./manage schemamigration APPNAME --auto`

To apply the migration:

`./manage migrate`

The migration created is a python script which is executed when migrate is run. It can be edited if necessary. The structure is not complex, but some skill with SQL may be required if the migration represents invasive changes to a schema which might add or move data between tables, for instance.

### Testing Migrations

The safest option is to make a copy/clone/snapshot of the database representing the state of the models before changes are made, making the changes and testing the application of the migration.

*IMPORTANT*: It is the responsibility of both testers and developers to test and validate a migration as the nature of what they are can destroy data permanently. 
