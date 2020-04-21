Gateway PAPIM Backup Role
=======

This role will backup only the PAPIM configuration files, solution kit files will be backed up by using the **gateway_export_database** role

The role will rename the backup file to a standardized format: **ansible_papim_ssgbackup_<hostname>.zip** and copy the file to the controller in a location specified by the variable **controller_dir_papim_backup_location**. The file can then be copied to a target server and restored using the **gateway_papim_restore_backup** role.
 

Requirements
------------

- The role must be executed with the Ansible user having root privilege.
- The Gateway nodes to backup must have the PAPIM installed.

Group_vars Variables
--------------

```
# The location on the controller to store the backups of each node.
# The folder will be automatically be created on the controller if it does not exist.
controller_dir_papim_backup_location: /var/local/papimbackup/
```
PAPIM specific variables to support backup can be found at test inventory (group_vars/gateway_papim.yml)

Dependencies
------------

n/a

Example Playbook
----------------
```
---
- hosts: gateway_source
  become: true # Note: only set to false if the ansible_user is root or has root privileges.
               # Setting true will also cause poor performance with the module_fetch and cause
               # the gateway_papim_backup role to run out of memory and fail. To mitigate this issue,
               # set the "serial" variable below to a maximum value of 2. If ansible_user is root, can 
               # comment out the serial variable to improve performance.
  serial: 2 # If role fails when trying to copy backup files to controller, increase the memory on the
            # controller to at least 4GB and/or change serial value to 1.
  gather_facts: false
  
  roles:
    - gateway_papim_backup
```
