# Description
This role will configure Gateway's MySQL database replication.

### Requirements

This role requires Gateways to have SSH enabled for the root user with password login.
Pre-configuration of Gateway network and hostnames is recommended.
MySQL should be running on the Gateways under inventory groups `gateway_primary_db` and `gateway_failover_db`.

Group Variables
---------------

Configure the following group variables:
- gateway_root_password
- database_repl_user
- database_repl_pass
- database_admin_user
- database_admin_pass

Role Variables
--------------

n/a

Dependencies
------------

n/a

Example Playbook
----------------

```yml
- name: Configure Gateway MySQL Database Replication
  hosts: gateway_primary_db
  roles:
    - gateway_configure_db_replication
```