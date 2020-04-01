Gateway Controller Cleanup
=========

Clean up any temporary files on the controller created by the migration automation. Should be executed after completing
the upgrade of one cluster before starting the migration of the next cluster to prevent the controller
from running out of disk space.

Requirements
------------

- The role must be executed with the Ansible user having root privilege to delete backup files created with layer7 permissions.
- Assumes gateway_basic_backup role was used previously.


Group_vars variables
--------------
```
# The location on the controller to store the backups of each node.
controller_dir_backup_location: /var/local/ssgbackup/

# The location on the controller to store the license files.
controller_dir_license_location: /tmp/license

# The location on the controller to store the database dump for database export and import roles.
controller_dir_db_backup_location: /tmp/db_dump

```

Role Variables
--------------

Dependencies
------------

n/a

Example Playbook
----------------

To clean up the backup files containing Gateway configuration files and assertions only, supply the tag: 
e.g.  **--tags cleanup_backup_config_assertion_files** when running this playbook.

```
---
- name: Delete temp files created by gateway-basic-backup role.
  hosts: localhost
  connection: local
  become: true
  roles:
    - gateway_controller_cleanup
  tags: cleanup_backup_config_assertion_files
```

License
-------
