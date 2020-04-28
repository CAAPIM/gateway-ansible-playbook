Export Gateway and OTK Database
======================

This role will export Gateway and OTK database from source gateway and store in local directory of Ansible Controller. 

Requirements
------------
* Purge audit events manually from the source Gateway. For more information, see "Delete Audit Events" in [View Gateway Audit Events](https://techdocs.broadcom.com/content/broadcom/techdocs/us/en/ca-enterprise-software/layer7-api-management/api-gateway/10-0/security-configuration-in-policy-manager/tasks-menu-security-options/view-gateway-audit-events.html) in TechDocs. 
    ```
    Main Steps:
    1) Login to Policy Manager
    2) From Policy Manager window, select [View] -> Gateway AuditEvents
    3) From the Gateway Audit Events window, select [File] > Delete Old Audit Events
    4) Click [Delete Events] when prompted to confirm
  
     ```  
* Must be able to ssh from the Ansible controller to the source gateway as user ssgconfig. 
* Specify source gateway's hostname/ip in the hosts.yml file [gateway_mysql] section.
    ``` 
    [gateway_mysql_source]
    10.175.245.xxx
    yyy.placeholder.com
     
     ```  

Role Variables
--------------
Under group_vars/all/vars
* controller_dir_db_backup_location - local directory to store the db dump zip file. The zip file is stored in a directory with name of the hostname/IP under local_db_dest

Under group_vars/all/vars
* remote_db_temp_dir - remote directory to store DB dump files.
* db_dump_zip_file - zipped db dump file without filename extension

* ansible_connection - use "ssh" as default
* ansible_user - use "ssgconfig" as default
* ansible_password - encrypted password for 'ssgconfig'

* database_name - gateway database name
* database_admin_user - root as default
* database_user - gateway as default

* otkdb_name - otk database name, It's commented out by default. Please uncomment out this variable if OTK installed in the gateway server. 

    ```
    #### OTK Database Configurations (if OTK database is in the Gateway MySQL database, uncomment & set these properties)
    
    # OTK Database Name
    # otkdb_name: otk_db
    
    # OTK Database User
    # otkdb_user: otk_user
    
    # Password for OTK Database User
    # otkdb_userpwd: "{{ vault_otk_database_pass }}"
    ```
 
Under role gatway_export_database/vars/main.yml
* database_admin_pass - please use "steps to created vaulted db password" to encrypt mysql root user's password if using option 2 above
* database_pass - please use "steps to created vaulted db password" to encrypt mysql gateway user's password

Dependencies
------------
There are no dependencies upon other roles.

Example Playbook
------------
file: playbooks/gateway-database-export.yml
    - name: Execute mysql dump and store
      hosts: mysql_source 
      serial: 1
      strategy: free
      roles:
        - gateway_export_database 

run : ansible-playbook playbooks/gateway-database-export.yml -i inventories/sample/hosts --vault-password-file vault-password-file.txt
