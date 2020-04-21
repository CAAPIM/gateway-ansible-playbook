Export Gateway and OTK Database
======================

This role will export Gateway and OTK database from source gateway and store in local directory of Ansible Controller. 

Requirements
------------
* ssh connect to remote source gateway as user ssgconfig using encrypted password. 
    Specify source gateway's hostname/ip in the hosts file [gateway_mysql_source] section.
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
    # otk database export and import is disabled by default. If OTK database share the same sever as gateway database,
    # please uncomment following 3 line and fill with OTK database name, user name and password
    #*otkdb_name: otk_db
    #otkdb_user: otk_user
    #otkdb_userpwd: '{{ vault_gateway_database_pass }}'
     
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

run : ansible-playbook playbooks/gateway-database-export.yml -i inventories/test/hosts --vault-password-file vault-password-file.txt

Steps to create vaulted db password
--------------------------------

1. run following command and enter vault password key first, then actual password (eg: dbpasswod) as plaintext input and ctrl-d twice
    ```
    > ansible-vault encrypt_string
    
    New Vault password:
    Confirm New Vault password:
    Reading plaintext input from stdin. (ctrl-d to end input) 
    dbpasswd!vault |
              $ANSIBLE_VAULT;1.1;AES256
              38306433306465626632343561306363346233313239306562316561363639393461373562636533
              6264666237666130643264303939316634663037643965340a363736643131363865396637313735
              63646431366439386239303064303936303164336630313963386234353530663235326531666566
              3939653836613938620a343466643861323663346534656466303830383133356363383735643432
              3966
    Encryption successful 

    ```


2. copy from !vault till "Encryption successful" as encrypted password and assign it to db_password: in roles/gateway-database-export/vars/main.yml
Note: be careful about the end of line and indentation.

3. store vault password key in file vault-password.txt and do not check in for safety reason