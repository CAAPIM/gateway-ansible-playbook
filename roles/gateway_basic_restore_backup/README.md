Gateway Basic Restore of Backup
=========

This task will restore Gateway and Linux configuration files and Gateway assertions archived 
by ssgbackup. No database files will be restored. The backup files used to restore from are located
on the controller and the path on the controller is specified by the group_vars variable **backup_location_on_controller**.
This task expects the backup file in which to restore from is in the format: **ansible_basic_ssgbackup_<source_hostname>.zip**. 
The source_hostname is the hostname where the backupfile was created.  

The mapping used to determine which backup file is used for which destination host is stored in the inventory host file.  
The destination and source are linked via a custom attribute called **upgrade_source** in the gateway group which
specifies the name of the hosts that are being upgraded to. This custom attribute must be specified for each host 
in the group in order to run this task.

The type of data to restore can be configured.  Please see **ssgrestore_parameters** and **restore_os_config**
in the vars folder for more information.

Requirements
------------

It is assumed that the **gateway_basic_backup** role was used previously to backup the files from the source Gateway.
The **upgrade_source** custom attribute must be specified for each host in the target gateway group of the inventory host file.
The task requires an Ansible user with root priviledges in order to execute.  The task executes ssgrestore and
handles files which requires root or layer7 priviledges.

Note: This role assumes that the network has already been configured and thus by default this role will not overwrite the
operating system files such as etc/sysconfig including etc/sysconfig/network-scripts. This role will not upgrade
the network configuration files from the older CentOS/RHEL6 style to newer CentOS/RHEL7 style.

Group_vars Variables
--------------

The directory on the controller which stores the backups of each node.
```
controller_dir_backup_location: /var/local/ssgbackup/
```

Role Variables
--------------

Use this configuration to specify whether Gateway configuration files, custom assertions, module assertions, and/or binaries from the ext folder should be
restored from backup. The following are ssgrestore parameters:
 
 ```
# -config: Gateway property files such as node.properties, ssglog.properties, and system.properties
# -ca: Custom assertions in /opt/SecureSpan/Gateway/runtime/modules/lib. Also includes telemetry.properties and
#      syslog.properties.
# -ma: Assertions in the /opt/SecureSpan/Gateway/runtime/modules/assertions. Modular assertions including tactical assertions. Does not include server modular files.      
# -ext: Files in the /opt/SecureSpan/Gateway/runtime/lib/ext.
ssgrestore_parameters: -config -ca -ma -ext
```

Use this configuration to specify which of the operating system files should be restored from backup.
restore_os_config: 
```
  sysconfig_folder: false # Note: this should not be enabled for upgrade from RHEL6/CentOS6 to 7+ as the networking is done differently in 7+.
  snmp_folder: true
  ntp_folder: false
  hosts: false
  ntp_conf: false
  resolv_conf: false
 ```
  
The directory on the target host where to store temporary files used to restore operating system files from backup.
```
temp_folder: /tmp/ssgrestore
```



Dependencies
------------

Requires the gateway_basic_backup role to have been executed before this role is used.

Example Playbook
----------------
```
---
- hosts: target_gateway
  gather_facts: no
  become: true
                          
  tasks:
    - include_role:
        name: gateway_basic_restore_backup     
```
