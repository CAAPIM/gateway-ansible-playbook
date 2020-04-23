Import Gateway and OTK Database
======================

This role will import Gateway and OTK database dump from the Ansible controller to destination (gateway primary db). The mapping between the source and destination Gateway database is specified in the inventories/.../hosts.yml. This role also contains a task to automatically upgrade the Gateway database schema. Once the database schema has been upgraded, it cannot be upgraded again.

Requirements
------------
* Gateway database has to be created before running import script. 
To setup gateway database, please run playbook gateway-autoprovision-nodes.yml.

* Must be able to ssh from the Ansible controller to the  destination gateway as user root using encrypted password. 
    Specify source gateway's hostname/IP in the hosts file [gateway_mysql] section. source hostname/IP match [gateway_mysql_source]
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
    # otk database export and import is disabled by default. If OTK database share the same sever as gateway database,
    # please uncomment following 3 line and fill with OTK database name, user name and password
    #*otkdb_name: otk_db
    #otkdb_user: otk_user
    #otkdb_userpwd: '{{ vault_gateway_database_pass }}'
     
     ``` 
 

* command_mysql_create_otkuser - command to create new otk user
* command_mysql_grant_otkuser_privileges - command to grant new otk user privileges

Under role gateway_import_database/vars/main.yml
* database_pass - please use "steps to created vaulted db password" to encrypt gateway user's password
* database_admin_pass - please use "steps to created vaulted db password" to encrypt root user password if using option 2 above



Dependencies
------------

It depends on role gateway_processing_node to setup destination gateway and database,
gateway_export_database to store db dump zip file in local directory and 
gateway_common/stop_gateway to stop the gateway service

Example Playbook
------------
file: playbooks/getway-database-import.yml

    
    > ansible-playbook playbooks/gateway-database-import.yml -i inventories/test/hosts --vault-password-file vault-password-file.txt
     
file: playbooks/gateway-database-schema-upgrade.yml

    
    > ansible-playbook playbooks/gateway-database-schema-upgrade.yml -i inventories/test/hosts --vault-password-file vault-password-file.txt
    
