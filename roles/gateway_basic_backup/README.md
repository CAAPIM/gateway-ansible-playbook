Gateway Basic Backup Role
=======

This role will backup Gateway and Linux configuration files and Gateway assertions using ssgbackup. No database files will
be backed up. The role will rename the backup file to a standardized format: **ansible_basic_ssgbackup_<hostname>.zip** and copy the file to the controller in a location specified by the group_vars variable **controller_dir_backup_location**. The file can then 
be copied to a target server and restored using the **gateway_basic_restore_backup** role.
 
 Note: This role must be executed with root privileges as ssgbackup requires root or layer7 privilege.  The backup file created is stored  using layer7 permissions.  Also, ssgbackup.sh will fail if the node.properties is missing. ssgbackup.sh assumes the Gateway was
configured and running in the past.
	   
 Note2: Because of the limitation of the fetch Ansible module used by this role, only two nodes can be backed up at a time when the role is running using sudo otherwise the controller will run out of memory.

Requirements
------------

- The role must be executed with the Ansible user having root privilege.
- The Gateway nodes to backup must have the /opt/SecureSpan/Gateway/node/default/etc/conf/node.properties file. (This role can only be used
  to backup configured Gateways)
- The backup files including the assertions will be about 150MB in size.  The Ansible controller must have enough space to hold a cluster worth
  of backup files: ~1.2GB (8 * 150MB).

Group_vars Variables
--------------

```
# The location on the controller to store the backups of each node.
# The folder will be automatically be created on the controller if it does not exist.
controller_dir_backup_location: /var/local/ssgbackup/
```

Role Variables
--------------

```
# The type of files to backup.  The following are valid backup parameters:
# 
# -os: Files in /etc folder such has hosts, ntp.conf, resolv.conf, snmpd.conf, and sysconfig files.
# -config: Gateway property files such as node, ssglog, and system.properties
# -ca: Custom assertions in /opt/SecureSpan/Gateway/runtime/modules/lib.
# -ma: Modular assertions including tactical assertions. Any assertions /opt/SecureSpan/Gateway/runtime/modules/assertions. Does not include server modular files.      
# -ext: Files in the /opt/SecureSpan/Gateway/runtime/lib/ext.
ssgbackup_parameters: -os -config -ca -ma -ext
```

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
               # the gateway_basic_backup role to run out of memory and fail. To mitigate this issue,
               # set serial variable below to a maximum value of 2. If ansible_user is root, can 
               # comment out the serial variable to improve performance.
  serial: 2 # If role fails when trying to copy backup files to controller, increase the memory on the
            # controller to at least 4GB and/or change the serial value to 1.
  gather_facts: false
  
  roles:
    - gateway_basic_backup
```
