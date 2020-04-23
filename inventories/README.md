Setting Up Gateway Inventory
=======
Currently gateway inventory file is using `yaml` style format. File extension can be either `yaml` or `yml`. It follows the *alternative folder structure* defined in [Ansible Best Practice](https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html#alternative-directory-layout)

## Host Machine Mapping
To use the same inventory for upgrading gateway, each target host machine will need to have `src` defined, like below.

```yml
gateway_primary_db:
  hosts:
    target-gw.placeholder.com:
      src: source-db-gw.placeholder.com
```
Without src defined, playbooks and role used for gateway upgrading will not function. ex. [Gateway Preupgrade Analyzer](../roles/gateway_preupgrade_analyzer)

By following this convention, new playbook for target machine can be used against the same inventory file without modification. Save time and effort on code maintainance.

## Host Groups
> gateway //master group contains all gateway subgroups

> gateway_mysql //master group contains all gateway mysql subgroups

> gateway_primary_db //group contains gateway master database node

> gateway_failover_db //group contains gateway failover database node

> gateway_processing_node //group contains gateway node process traffic only, no database running.

## Current Sample Inventory 

To view the current sample inventory in a tree view, `ansible-inventory` command can be used.

```bash
ansible-inventory --graph -i ./inventory/sample/hosts.yml
```
Output:
```
@all:
  |--@gateway:
  |  |--@gateway_hsm:
  |  |--@gateway_mysql:
  |  |  |--@gateway_failover_db:
  |  |  |  |--failover-gw.placeholder.com
  |  |  |--@gateway_primary_db:
  |  |  |  |--target-gw.placeholder.com
  |  |--@gateway_papim_install:
  |  |--@gateway_papim_source:
  |  |--@gateway_processing_node:
  |  |  |--target-processing-gw.placeholder.com
  |--@hsm:
  |  |--@hsm_luna7:
  |  |  |--luna7.placeholder.com
  |--@ungrouped:
```

For more detailed view, use option `--list`
```bash
ansible-inventory --list -i ./inventory/sample/hosts.yml
```
Output:
```
{
    "_meta": {
        "hostvars": {
            "failover-gw.placeholder.com": {
                "ansible_password": "{{ vault_gateway_ssgconfig_password }}", 
                "ansible_ssh_extra_args": "-o StrictHostKeyChecking=no", 
                "ansible_user": "ssgconfig", 
                "cluster_host": "{{ hostvars[groups['gateway_primary_db'][0]]['dest'] }}", 
                "cluster_pass": "{{ vault_gateway_cluster_pass }}", 
                "configure_db": false, 
                "configure_node": true, 
                "controller_dir_backup_location": "/var/local/ssgbackup/", 
                "controller_dir_db_backup_location": "/tmp/db_dump", 
                "controller_dir_license_location": "/tmp/license", 
                "database_admin_pass": "{{ vault_gateway_database_admin_pass }}", 
                "database_admin_user": "root", 
                "database_host": "{{ cluster_host }}", 
                "database_name": "ssg", 
                "database_pass": "{{ vault_gateway_database_pass }}", 
                "database_port": 3306, 
                "database_type": "mysql", 
                "database_user": "gateway", 
                "db_dump_zip_file": "{{ remote_db_temp_dir }}/mysql_result", 
                "gateway_bootstrap_dir": "/opt/SecureSpan/Gateway/node/default/etc/bootstrap", 
                "gateway_default_password": "{{ vault_gateway_default_password }}", 
                "gateway_license_install_dir": "{{ gateway_bootstrap_dir }}/license", 
                "gateway_license_upload_dir": "/home/ssgconfig/license", 
                "gateway_root_password": "{{ vault_gateway_root_password }}", 
                "gateway_shutdown_wait_timeout": "{{ gateway_startup_wait_timeout }}", 
                "gateway_ssgconfig_password": "{{ vault_gateway_ssgconfig_password }}", 
                "gateway_ssh_timeout": 20, 
                "gateway_startup_wait_timeout": 600, 
                "node_enable": true, 
                "node_props_dir": "node_properties", 
                "policy_manager_admin_pass": "{{ vault_policy_manager_admin_pass }}", 
                "policy_manager_admin_user": "admin", 
                "port_gateway_health_check": 8080, 
                "port_gateway_ssh": 22, 
                "remote_db_temp_dir": "{{ controller_dir_db_backup_location }}", 
                "src": "source-failover-db-gw.placeholder.com", 
                "vault_gateway_cluster_pass": "placeholder", 
                "vault_gateway_database_admin_pass": "placeholder", 
                "vault_gateway_database_pass": "placeholder", 
                "vault_gateway_database_repl_pass": "placeholder", 
                "vault_gateway_default_password": "placeholder", 
                "vault_gateway_root_password": "placeholder", 
                "vault_gateway_ssgconfig_password": "placeholder", 
                "vault_hsm_luna7_password": "placeholder", 
                "vault_policy_manager_admin_pass": "placeholder", 
                "vault_qa_manual_partition_password": "placeholder"
            }, 
            "luna7.placeholder.com": {
                "ansible_connection": "ssh", 
                "ansible_password": "{{ vault_gateway_ssgconfig_password }}", 
                "ansible_ssh_extra_args": "-o StrictHostKeyChecking=no", 
                "ansible_user": "admin", 
                "cluster_host": "{{ hostvars[groups['gateway_primary_db'][0]]['dest'] }}", 
                "cluster_pass": "{{ vault_gateway_cluster_pass }}", 
                "configure_db": false, 
                "configure_node": true, 
                "controller_dir_backup_location": "/var/local/ssgbackup/", 
                "controller_dir_db_backup_location": "/tmp/db_dump", 
                "controller_dir_license_location": "/tmp/license", 
                "database_admin_pass": "{{ vault_gateway_database_admin_pass }}", 
                "database_admin_user": "root", 
                "database_host": "{{ cluster_host }}", 
                "database_name": "ssg", 
                "database_pass": "{{ vault_gateway_database_pass }}", 
                "database_port": 3306, 
                "database_type": "mysql", 
                "database_user": "gateway", 
                "db_dump_zip_file": "{{ remote_db_temp_dir }}/mysql_result", 
                "gateway_bootstrap_dir": "/opt/SecureSpan/Gateway/node/default/etc/bootstrap", 
                "gateway_default_password": "{{ vault_gateway_default_password }}", 
                "gateway_license_install_dir": "{{ gateway_bootstrap_dir }}/license", 
                "gateway_license_upload_dir": "/home/ssgconfig/license", 
                "gateway_root_password": "{{ vault_gateway_root_password }}", 
                "gateway_shutdown_wait_timeout": "{{ gateway_startup_wait_timeout }}", 
                "gateway_ssgconfig_password": "{{ vault_gateway_ssgconfig_password }}", 
                "gateway_ssh_timeout": 20, 
                "gateway_startup_wait_timeout": 600, 
                "node_enable": true, 
                "node_props_dir": "node_properties", 
                "policy_manager_admin_pass": "{{ vault_policy_manager_admin_pass }}", 
                "policy_manager_admin_user": "admin", 
                "port_gateway_health_check": 8080, 
                "port_gateway_ssh": 22, 
                "remote_db_temp_dir": "{{ controller_dir_db_backup_location }}", 
                "vault_gateway_cluster_pass": "placeholder", 
                "vault_gateway_database_admin_pass": "placeholder", 
                "vault_gateway_database_pass": "placeholder", 
                "vault_gateway_database_repl_pass": "placeholder", 
                "vault_gateway_default_password": "placeholder", 
                "vault_gateway_root_password": "placeholder", 
                "vault_gateway_ssgconfig_password": "placeholder", 
                "vault_hsm_luna7_password": "placeholder", 
                "vault_policy_manager_admin_pass": "placeholder", 
                "vault_qa_manual_partition_password": "placeholder"
            }, 
            "target-gw.placeholder.com": {
                "ansible_password": "{{ vault_gateway_ssgconfig_password }}", 
                "ansible_ssh_extra_args": "-o StrictHostKeyChecking=no", 
                "ansible_user": "ssgconfig", 
                "cluster_host": "{{ hostvars[groups['gateway_primary_db'][0]]['dest'] }}", 
                "cluster_pass": "{{ vault_gateway_cluster_pass }}", 
                "configure_db": false, 
                "configure_node": true, 
                "controller_dir_backup_location": "/var/local/ssgbackup/", 
                "controller_dir_db_backup_location": "/tmp/db_dump", 
                "controller_dir_license_location": "/tmp/license", 
                "database_admin_pass": "{{ vault_gateway_database_admin_pass }}", 
                "database_admin_user": "root", 
                "database_host": "{{ cluster_host }}", 
                "database_name": "ssg", 
                "database_pass": "{{ vault_gateway_database_pass }}", 
                "database_port": 3306, 
                "database_type": "mysql", 
                "database_user": "gateway", 
                "db_dump_zip_file": "{{ remote_db_temp_dir }}/mysql_result", 
                "gateway_bootstrap_dir": "/opt/SecureSpan/Gateway/node/default/etc/bootstrap", 
                "gateway_default_password": "{{ vault_gateway_default_password }}", 
                "gateway_license_install_dir": "{{ gateway_bootstrap_dir }}/license", 
                "gateway_license_upload_dir": "/home/ssgconfig/license", 
                "gateway_root_password": "{{ vault_gateway_root_password }}", 
                "gateway_shutdown_wait_timeout": "{{ gateway_startup_wait_timeout }}", 
                "gateway_ssgconfig_password": "{{ vault_gateway_ssgconfig_password }}", 
                "gateway_ssh_timeout": 20, 
                "gateway_startup_wait_timeout": 600, 
                "node_enable": true, 
                "node_props_dir": "node_properties", 
                "policy_manager_admin_pass": "{{ vault_policy_manager_admin_pass }}", 
                "policy_manager_admin_user": "admin", 
                "port_gateway_health_check": 8080, 
                "port_gateway_ssh": 22, 
                "remote_db_temp_dir": "{{ controller_dir_db_backup_location }}", 
                "src": "source-db-gw.placeholder.com", 
                "vault_gateway_cluster_pass": "placeholder", 
                "vault_gateway_database_admin_pass": "placeholder", 
                "vault_gateway_database_pass": "placeholder", 
                "vault_gateway_database_repl_pass": "placeholder", 
                "vault_gateway_default_password": "placeholder", 
                "vault_gateway_root_password": "placeholder", 
                "vault_gateway_ssgconfig_password": "placeholder", 
                "vault_hsm_luna7_password": "placeholder", 
                "vault_policy_manager_admin_pass": "placeholder", 
                "vault_qa_manual_partition_password": "placeholder"
            }, 
            "target-processing-gw.placeholder.com": {
                "ansible_password": "{{ vault_gateway_ssgconfig_password }}", 
                "ansible_ssh_extra_args": "-o StrictHostKeyChecking=no", 
                "ansible_user": "ssgconfig", 
                "cluster_host": "{{ hostvars[groups['gateway_primary_db'][0]]['dest'] }}", 
                "cluster_pass": "{{ vault_gateway_cluster_pass }}", 
                "configure_db": false, 
                "configure_node": true, 
                "controller_dir_backup_location": "/var/local/ssgbackup/", 
                "controller_dir_db_backup_location": "/tmp/db_dump", 
                "controller_dir_license_location": "/tmp/license", 
                "database_admin_pass": "{{ vault_gateway_database_admin_pass }}", 
                "database_admin_user": "root", 
                "database_host": "{{ cluster_host }}", 
                "database_name": "ssg", 
                "database_pass": "{{ vault_gateway_database_pass }}", 
                "database_port": 3306, 
                "database_type": "mysql", 
                "database_user": "gateway", 
                "db_dump_zip_file": "{{ remote_db_temp_dir }}/mysql_result", 
                "gateway_bootstrap_dir": "/opt/SecureSpan/Gateway/node/default/etc/bootstrap", 
                "gateway_default_password": "{{ vault_gateway_default_password }}", 
                "gateway_license_install_dir": "{{ gateway_bootstrap_dir }}/license", 
                "gateway_license_upload_dir": "/home/ssgconfig/license", 
                "gateway_root_password": "{{ vault_gateway_root_password }}", 
                "gateway_shutdown_wait_timeout": "{{ gateway_startup_wait_timeout }}", 
                "gateway_ssgconfig_password": "{{ vault_gateway_ssgconfig_password }}", 
                "gateway_ssh_timeout": 20, 
                "gateway_startup_wait_timeout": 600, 
                "node_enable": true, 
                "node_props_dir": "node_properties", 
                "policy_manager_admin_pass": "{{ vault_policy_manager_admin_pass }}", 
                "policy_manager_admin_user": "admin", 
                "port_gateway_health_check": 8080, 
                "port_gateway_ssh": 22, 
                "remote_db_temp_dir": "{{ controller_dir_db_backup_location }}", 
                "src": "source-processing-gw.placeholder.com", 
                "vault_gateway_cluster_pass": "placeholder", 
                "vault_gateway_database_admin_pass": "placeholder", 
                "vault_gateway_database_pass": "placeholder", 
                "vault_gateway_database_repl_pass": "placeholder", 
                "vault_gateway_default_password": "placeholder", 
                "vault_gateway_root_password": "placeholder", 
                "vault_gateway_ssgconfig_password": "placeholder", 
                "vault_hsm_luna7_password": "placeholder", 
                "vault_policy_manager_admin_pass": "placeholder", 
                "vault_qa_manual_partition_password": "placeholder"
            }
        }
    }, 
    "all": {
        "children": [
            "gateway", 
            "hsm", 
            "ungrouped"
        ]
    }, 
    "gateway": {
        "children": [
            "gateway_hsm", 
            "gateway_mysql", 
            "gateway_papim_install", 
            "gateway_papim_source", 
            "gateway_processing_node"
        ]
    }, 
    "gateway_failover_db": {
        "hosts": [
            "failover-gw.placeholder.com"
        ]
    }, 
    "gateway_mysql": {
        "children": [
            "gateway_failover_db", 
            "gateway_primary_db"
        ]
    }, 
    "gateway_primary_db": {
        "hosts": [
            "target-gw.placeholder.com"
        ]
    }, 
    "gateway_processing_node": {
        "hosts": [
            "target-processing-gw.placeholder.com"
        ]
    }, 
    "hsm": {
        "children": [
            "hsm_luna7"
        ]
    }, 
    "hsm_luna7": {
        "hosts": [
            "luna7.placeholder.com"
        ]
    }
}
```


