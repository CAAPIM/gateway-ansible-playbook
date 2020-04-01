Gateway Primary Database Node
======================

This role runs Gateway's auto-provision functionality using values configured under `group_vars/gateway.yml`. This sets up the onboard Gateway database.

Requirements
------------
This role should be run from the Ansible controller.

Group Variables
---------------
file: `group_vars/gateway.yml`.

Configure the variables under the "Node Configurations" section.
Defaults are provided for most of them. Note: defaults assume that the `cluster_host` is the Gateway in the inventory group `gateway_primary_db_node`, and that the `database_host` is the same as the `cluster_host`.

Role Variables
--------------
n/a

Dependencies
------------
n/a

Example Playbook
------------
```yaml
- name: Setup MySQL database on the destination (primary database node) Gateway. 
  hosts: gateway_primary_db
  connection: local
  vars:
    ansible_python_interpreter: "{{ ansible_playbook_python }}"
  roles:
    - gateway_primary_db_node
```
