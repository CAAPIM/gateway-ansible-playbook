Update Hostnames used by OTK
============================

This role updates the hostnames used by OTK using the values configured under this role's `vars/main.yml`:
- Gateway cluster.hostname cluster property
- OTK's JDBC connection URLs
- Callback URLs

Requirements
------------

Before running this role:
- Back up the database.
- Configure the Group & Role Variables in the following sections.
- OTK should already be migrated to the destination database, which is the internal MySQL database running on a Gateway.
- Grant sufficient privileges to your Ansible controller for modifying the OTK database.
  - *For example*
  ```
  CREATE USER 'otk_user'@'localhost'; 
  CREATE USER 'otk_user'@'localhost' IDENTIFIED BY 'otk_userpwd';
  GRANT SELECT, INSERT, DELETE, UPDATE ON 'otkdb_name'.* TO 'otk_user'@'localhost';
  ```

After running this role:
- **Clean up OTK database privileges for the Ansible controller.**
  - *For example*
    ```
    DROP USER 'otk_user'@'localhost';
    ```
- Update the hostname for the Gateway default SSL certificate by following [Set a Default SSL or CA Private Key](https://techdocs.broadcom.com/content/broadcom/techdocs/us/en/ca-enterprise-software/layer7-api-management/api-gateway/10-0/security-configuration-in-policy-manager/tasks-menu-security-options/manage-private-keys/set-a-default-ssl-or-ca-private-key.html).

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
    - gateway_update_otk_hostnames
```
