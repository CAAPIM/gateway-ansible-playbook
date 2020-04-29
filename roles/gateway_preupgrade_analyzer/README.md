Preupgrade Analyzer
======================

This role will generate pre-upgrade analyzer reports for each `src` Gateway in the inventory `gateway` group. The reports are stored in `[project root]/preupgrade-analyzer-reports/[hostname]` directories (1 report per source Gateway).

*Note: re-running this role will overwrite existing reports so back them up as needed.*

Please review the pre-upgrade analyzer reports before upgrading Gateways.

Requirements
------------
* ssh connect to remote source gateway as user ssgconfig using encrypted password. 
    Specify source gateway's hostname/ip in the [gateway] section of the inventories/<xxx>/hosts file, e.g., inventories/sample/hosts.
    
* In order to run MySQL 8 compatibility checker, login to source gateway manually (defined in hosts file [gateway_primary_db] and [gateway_failover_db] section) as database admin user and run the following command:
    ```
    GRANT ALL PRIVILEGES ON *.* TO 'gateway'@'%'; flush privileges;
    ```
  Make sure to revoke the privileges after the tasks is completed
    ```
    REVOKE ALL PRIVILEGES ON *.* from 'gateway'@'%'; flush privileges;
    ```

Role Variables
--------------
Under inventories/xxx/group_vars/all/vars
* database_user - Database Username default as gateway
* database_pass -  Database Password
* database_name - Gateway Database Name
* database_port - Database Port default as 3306

Under roles/gateway_preupgrade_analyzer/vars/main.yml
* remote_report_temp_dir - remote directory to store report files.
* gateway_modular_assertions_dir - target gateway modular assertions directory use '/opt/SecureSpan/Gateway/runtime/modules/assertions'
* gateway_custom_assertions_dir - target gateway custom assertions directory use '/opt/SecureSpan/Gateway/runtime/modules/lib'
* gateway_external_libs_dir - target gateway external libs directory use '/opt/SecureSpan/Gateway/runtime/lib/ext'


Dependencies
------------
There are no dependencies upon other roles.

Example Playbook
------------------
file: playbooks/gateway-preupgrade-analyzer.yml

run command: ansible-playbook playbooks/gateway-preupgrade-analyzer.yml -i inventories/sample/hosts --vault-password-file vault-password-file.txt

How to read the report
-----------------------
The report contains following contents:

* list of assertions and server module files installed on the gateway under section [Modular Assertions], [Custom Assertions] and [Server Module Files]. All will be migrated automatically.
* list of external libraries installed on the gateway under section [External Libraries]. All will be migrated automatically.
* list of rpm installed on the gateway under section [RPM Results].  It reminds previously customer installed RPM that customer may need to migrate themselves.
* MySQL 8 compatibility checker results under section [Mysql8 Compatibility Checker Results]. Please make sure there is no errors at end of the section.
    ```
        Errors:   0
        Warnings: 442
        Notices:  0
    ```
                                                 