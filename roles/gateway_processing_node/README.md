Gateway Processing Node
======================

This role runs Gateway's auto-provision functionality using values configured under `group_vars/all/vars`. It adds processing (non-database) Gateways to an existing cluster. 

Requirements
------------
This role should be run from the Ansible controller.

Group Variables
---------------
file: `group_vars/all/vars`.

Configure the variables under the "Node Configurations" section.
Defaults are provided for most of them. Note: defaults assume that the `cluster_host` is the Gateway in the inventory group `gateway_primary_db_node`, and that the `database_host` is the same as the `cluster_host`.

Role Variables
--------------
n/a

Dependencies
------------
- `gateway_primary_db_node` role has been run to setup a Gateway with database

Example Playbook
------------
```yaml
- name: Add processing nodes to the new cluster with the destination (primary database node) Gateway.
  hosts: gateway_processing_node
  connection: local
  vars:
    ansible_python_interpreter: "{{ ansible_playbook_python }}"
  roles:
    - gateway_processing_node
```
