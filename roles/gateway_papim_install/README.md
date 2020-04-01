Gateway PAPIM Installation
=========

This task will install the EPAgent in the target node, PAPIM solution kit installation is not covered as part of this.

If it is fresh setup user should install the solution kit via policy manager.
If setup already exists in another node, the user can make use of **gateway_export_database** and **gateway_import_database** roles to migrate the solution kit.


Requirements
------------

User should download and copy the installation file **APIM-PM-GW.tar.gz** to specified location (**controller_dir_papim_source_location**) in controller.
The task requires an Ansible user with root privileges in order to execute.

Group_vars Variables
--------------

Installation file **APIM-PM-GW.tar.gz** path on the controller
```
controller_dir_papim_source_location: /var/local/papiminstall/
```
PAPIM specific variables to support installation can be found at test inventory (group_vars/gateway_papim.yml)

Role Variables
--------------
  
The directory on the target host to extract installation files.
```
temp_folder: /tmp/papiminstall
```

Dependencies
------------
User should download and copy the installation file **APIM-PM-GW.tar.gz** to controller.

Example Playbook
----------------
```
---
- hosts: gateway_papim_install
  gather_facts: no
  become: true
                            
  tasks:
  - include_role:
      name: gateway_papim_install 
```

