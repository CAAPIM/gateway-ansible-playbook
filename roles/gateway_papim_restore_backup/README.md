Gateway PAPIM Restore of Backup
=========

This task will restore the PAPIM configuration files.
The backup files used to restore from are located on the controller and the path on the controller is specified by the group_vars variable **controller_dir_papim_backup_location**.

This task expects the backup file in which to restore from is in the format: **ansible_papim_ssgbackup_<source_hostname>.zip**. 
The source_hostname is the hostname where the backupfile was created.  

The mapping used to determine which backup file is used for which destination host is stored in the inventory host file.  
The destination and source are linked via a custom attribute called **upgrade_source** in the gateway group which
specifies the name of the hosts that are being upgraded to. This custom attribute must be specified for each host 
in the group in order to run this task.


Requirements
------------

It is assumed that the **gateway_papim_backup** role was used previously to backup the files from the source Gateway.
The **upgrade_source** custom attribute must be specified for each host in the target gateway group of the inventory host file.
The task requires an Ansible user with root priviledges in order to execute.

Group_vars Variables
--------------

The directory on the controller which has the backups of each node.
```
controller_dir_papim_backup_location: /var/local/papimbackup/
```
PAPIM specific variables to support restore can be found at test inventory (group_vars/gateway_papim.yml)

Role Variables
--------------
  
The directory on the target host where to store temporary files used to restore operating system files from backup.
```
temp_folder: /tmp/papimrestore
```

Dependencies
------------

Requires the gateway_papim_backup role to have been executed before this role is used.

Example Playbook
----------------
```
---
- hosts: gateway_dest
  gather_facts: no
  become: true
                            
  tasks:
  - include_role:
      name: gateway_papim_restore_backup  
```

