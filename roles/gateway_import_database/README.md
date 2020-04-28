Import Gateway and OTK Database
======================

This role will import Gateway and OTK database dump from ansible controller to destination (remote gateway primary node). 
It also contains task to upgrade gateway database schema. Once the database schema has been upgraded, it cannot be upgraded again.

Requirements
------------
* Gateway database has to be created before running import script. 
To setup gateway database automatically, please run playbook gateway-autoprovision-nodes.yml.

* ssh connect to remote destination gateway as user root using encrypted password. 
    Specify source gateway's hostname/IP in the hosts file [gateway_mysql_dest] section. source hostname/IP match [gateway_mysql_source]
    ```
    [gateway_mysql_dest]
    10.175.244.yyy  source=10.175.245.xxx
    xxx.placeholder.com source=yyy.placeholder.com 
  
    
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
* ansible_user - use "root" as default
* ansible_password - encrypted password for 'root'
* database_name - gateway database name
* database_user - gateway as default
* database_admin_user - root as default

* otkdb_name - otk database name, please uncomment out this variable if OTK installed in the gateway server 
* otkdb_user - new otk user name, please uncomment out this variable if OTK installed in the gateway server
* otkdb_userpwd - new otk user password, please uncomment out this variable if OTK installed in the gateway server

    ```
    #### OTK Database Configurations (if OTK database is in the Gateway MySQL database, uncomment & set these properties)
    
    # OTK Database Name
    # otkdb_name: otk_db
    
    # OTK Database User
    # otkdb_user: otk_user
    
    # Password for OTK Database User
    # otkdb_userpwd: "{{ vault_otk_database_pass }}"
    ```
 

* command_mysql_create_otkuser - command to create new otk user
* command_mysql_grant_otkuser_privileges - command to grant new otk user privileges

Under role gateway_import_database/vars/main.yml
* database_pass - please use "steps to created vaulted db password" to encrypt gateway user's password
* database_admin_pass - please use "steps to created vaulted db password" to encrypt root user password if using option 2 above



Dependencies
------------

It depends on role gateway_configure_processing_node to setup destination gateway and database,
gateway_export_database to store db dump zip file in local directory and 
gateway_common/stop_gateway to stop the gateway service

Example Playbook
------------
file: playbooks/getway-database-import.yml

    
    > ansible-playbook playbooks/gateway-database-import.yml -i inventories/test/hosts --vault-password-file vault-password-file.txt
     
file: playbooks/gateway-database-schema-upgrade.yml

    
    > ansible-playbook playbooks/gateway-database-schema-upgrade.yml -i inventories/test/hosts --vault-password-file vault-password-file.txt
    
