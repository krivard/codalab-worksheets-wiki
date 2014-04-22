# Groups and Permissions Discussion

## Data model

**Groups**
Groups are identified by a "group spec" in a manner similar to bundles and worksheets. Groups are either user-defined or system-defined. System-defined groups cannot be modified. An example of a system-defined group is the *Public* group which denotes all users. Non system-defined group are associated with a user (the creator of the group) via `owner_id`.

```
group = Table(
  'group',
  db_metadata,
  Column('id', Integer, primary_key=True, nullable=False),
  Column('uuid', String(63), nullable=False),
  Column('name', String(255), nullable=False),
  Column('user_defined', Boolean),
  Column('owner_id', Integer, nullable=True),
  UniqueConstraint('uuid', name='uix_1'),
  Index('group_name_index', 'name'),
  Index('group_owner_id_index', 'owner_id'),
  sqlite_autoincrement=True,
)

group.insert().values(name="Public", uuid=..., user_defined=False)
```

The *Public* group is intended to represent all users, including users who are not authenticated. If the need arise, a system-defined *Authenticated* group can be added to represent all authenticated users.

**Group membership**

The `user_group` table associates a user with a set of groups. A user is identified by the unique user ID provided by the OAuth authentication server.

```
user_group = Table(
  'user_group',
  db_metadata,
  Column('id', Integer, primary_key=True, nullable=False),
  Column('group_uuid', String(63), ForeignKey(group.c.uuid), nullable=False),
  Column('user_id', String(63), nullable=False),
  Column('is_admin', Boolean),
  Index('group_uuid_index', 'group_uuid'),
  Index('user_id_index', 'user_id'),
  sqlite_autoincrement=True,
)
```

Column `is_admin` exists because a user may be added to a group and given admin privileges for the group. By default, the creator of the group is always an admin.

The implementation will have to be aware of special groups like *Public* since users will not be explicitly added to such groups.

**Permissions**

The set of possible permissions will be small and defined using symbolic constants. Basic permissions are: None, Read and All.

Permissions are granted to a group on an object (a bundle or a worksheet).

```
group_object_permission = Table(
  'group_object_permission',
  db_metadata,
  Column('id', Integer, primary_key=True, nullable=False),
  Column('group_uuid', String(63), ForeignKey(group.c.uuid), nullable=False),
  # Reference to a worksheet or bundle object
  Column('object_uuid', String(63), nullable=True),
  # Permissions encoded as integer. 'Read' (0x01) or 'All' (0x11)
  Column('permission', Integer, nullable=False),
  sqlite_autoincrement=True,
)
```

Table `group_object_permission` only knows about groups; it does not know about users directly. All objects (bundles and worksheets) will have an additional column to keep track of the user who created the object. The so-called object owner always has full rights on the object.

#CLI commands
To manage groups:
```
add-group <group_spec>
del-group <group_spec>
add-user <user_spec> <group_spec>
del-user <user_spec> <group_spec>
#List my groups
cl list -g 
#Show info (list members) of a group
cl info -g <group_spec>
```
To set permissions, in a initial discussion, we had noted:
```
set-perm <group_spec> <none|r(ead)|a(ll)> <bundle_spec>
set-perm -w <group_spec> <none|r(ead)|a(ll)> <worksheet_spec>
set-perm -g <group_spec> <none|r(ead)|a(ll)> <group_spec>
```
The above design uses an additional command switch to specify the type of object (similar to `cl clist` vs. `cl list -w`). An alternative would be to have command named after the object type:
```
set-bundle-perm <group_spec> <none|r(ead)|a(ll)> <bundle_spec>
set-worksheet-perm <group_spec> <none|r(ead)|a(ll)> <worksheet_spec>
set-group-perm <group_spec> <none|r(ead)|a(ll)> <group_spec>
```
Another option would be to have a single command for all objects but that may increase the number of times that a user is forced to use the full UUID as opposed to using the friendly name of the object:
```
set-perm <group_spec> <none|r(ead)|a(ll)> <object_spec>
```
#Bundle service API
TODO: How do existing service API change to satisfy the need of clients. A primary client is the web site.
```
list_worksheets(auth_token, owner/group, search text, bundle referenced, ..., offset/limit)
list_bundles(auth_token, owner/group, search text, bundle dependencies, ..., offset/limit)
```

#Sample scenarios

##User uploads a bundle

1. When a user uploads a bundle, the bundle server authenticates the user and obtains the unique user ID, `user_id`, from the OAuth authentication server.

2. The bundle server queries the `user_group` table to find a system-defined group associated with `user_id`. If such a group does not exist, then it is created. The end result is a group representing the individual user. Let `user_g_id` denote the UUID of the group.

3. The bundle is created so we know its UUID, `bundle_uuid`.

4. The association between the user (indirectly represented by group `user_g_id`) and the bundle (`bundle_uuid`) are recorded in the `group_object_permission` table. The user is granted full control.

##User shares a bundle with a team.

1. From the CLI, user creates a group for the team:
   ```
   cl add-group myteam
   ```
   As a result of the command, the bundle server creates a user-defined group named "myteam". The user (who issued the command) is automatically added to the group and is granted all permissions on the group.

2. From the CLI, user adds a team member to the group:
   ```
   cl add-user member1 myteam
   ```
   As a result of the command, the bundle server calls the OAuth authentication server to resolve the name "member1" into a unique user ID, `member1_user_id`. The bundle server also resolves "myteam" into a group UUID. If successful, the bundle server verifies that the issuer of the CLI command has permissions to edit the group, then adds "member1" to the group.

   Other team members are added one-by-one.

3. From the CLI, user sets permission on the bundle:
   ```
   cl set-perm myteam read mybundle
   ```

##Sharing a worsheet with private bundles

Let's say that a user creates a worsheet which references bundles which are private to that user. Now the user attempts to make the worksheet public to all. It's probably reasonable to assume that the CLI and/or the bundle service would warn the user and would refuse to make the worksheet public until the bundles are also public.

