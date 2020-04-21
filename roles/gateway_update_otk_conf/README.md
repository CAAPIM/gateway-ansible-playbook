Update Hostnames used by OTK
============================

This role updates the hostnames used by OTK using the values configured under this role's `vars/main.yml`:
- Gateway cluster.hostname cluster property
- OTK's JDBC connection URLs
- Callback URLs

Requirements
------------

Before running this role:
- OTK should already be migrated to the destination database, which is the internal MySQL database running on a Gateway.
- Ensure the Ansible controller can modify the OTK database
  - Note: otk variables correspond to the value of the otkdb_name, otkdb_user and otkdb_userpwd variables, respectively (see Group Variables section)
  - e.g. CREATE USER <otk user>@'localhost' IDENTIFIED BY '<otk user pass>';
         GRANT SELECT, INSERT, DELETE, UPDATE ON <otk db name>.* TO <otk user>@'localhost';

After running this role:
- **Clean up grants after this role is executed.**
- Follow the steps for [Set a Default SSL or CA Private Key](https://techdocs.broadcom.com/content/broadcom/techdocs/us/en/ca-enterprise-software/layer7-api-management/api-gateway/10-0/security-configuration-in-policy-manager/tasks-menu-security-options/manage-private-keys/set-a-default-ssl-or-ca-private-key.html) for the updated hostname.

Group Variables
---------------

Ensure the following group variables are set in your inventory group vars for the `gateway` host group:
- otkdb_name
- otkdb_user
- otkdb_userpwd

Role Variables
--------------

Ensure the following role variables are set in this role's [vars/main.yml](vars/main.yml):
- new_cluster_hostname
- old_otk_jdbc_hostname
- new_otk_jdbc_hostname
- old_callback_hostname
- new_callback_hostname

Dependencies
------------
n/a

Example Playbook
----------------
```yaml
- name: Update hostnames used by OTK after expedited upgrade involving hostname changes.
  hosts: gateway_primary_db
  roles:
    - gateway_update_otk_conf
```
