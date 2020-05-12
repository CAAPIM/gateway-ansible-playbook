Configure Gateway Node
======================

This role runs Gateway's auto-provision functionality using values configured under `group_vars/all/vars`. This role is used by the the following roles:
 
 * gateway_configure_primary_node
 * gateway_configure_processing_node 

Requirements
------------
This role should be run from the Ansible controller.

Group Variables
---------------
file: `group_vars/all/vars`.

Configure the variables under the "Node Configurations" and "Database configurations" section.
Defaults are provided for most of them. Note: defaults assume that the `cluster_host` is the Gateway in the inventory group `gateway_primary_db`, and that the `database_host` is the same as the `cluster_host`.

Role Variables
--------------
n/a

Dependencies
------------
n/a
