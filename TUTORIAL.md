# Tutorial


This tutorial will walk through the basic workflow of using the Gateway Ansible playbooks and roles.

The exact steps for your upgrade are likely to differ, so the process needs to be adapted to your environment.

## Overview

Review the expedited upgrade documentation on TechDocs: 
- [Preparing for Expedited Upgrade](https://techdocs.broadcom.com/content/broadcom/techdocs/us/en/ca-enterprise-software/layer7-api-management/api-gateway/10-0/install-configure-upgrade/upgrade-the-gateway/upgrade-an-appliance-gateway/prep-for-expedited-upgrade.html)
- [Automating with Ansible](https://techdocs.broadcom.com/content/broadcom/techdocs/us/en/ca-enterprise-software/layer7-api-management/api-gateway/10-0/install-configure-upgrade/upgrade-the-gateway/upgrade-an-appliance-gateway/automated-expedited-upgrade/automating-with-ansible.html)
- [Automated Expedited Upgrade Procedure](https://techdocs.broadcom.com/content/broadcom/techdocs/us/en/ca-enterprise-software/layer7-api-management/api-gateway/10-0/install-configure-upgrade/upgrade-the-gateway/upgrade-an-appliance-gateway/automated-expedited-upgrade.html)
- [Post Expedited Upgrade Tasks](https://techdocs.broadcom.com/content/broadcom/techdocs/us/en/ca-enterprise-software/layer7-api-management/api-gateway/10-0/install-configure-upgrade/upgrade-the-gateway/upgrade-an-appliance-gateway/post-upgrade-tasks.html)

Outline of the upgrade steps to follow:

![Steps for in-place upgrade](https://techdocs.broadcom.com/content/dam/broadcom/techdocs/us/en/assets/docops/gateway/ansibleAutoReimage.png/_jcr_content/renditions/original)

## Scenario

For context, the upgrade scenario we will use is an expedited in-place upgrade for a Virtual Appliance Gateway cluster, which will re-use existing VM resources:
- Starting Point: a Gateway v9.4 cluster with 3 nodes, GW appliance's MySQL v5.7 is configured with failover, and OAuth Toolkit v4.3.1 is installed

- Upgraded Gateways: same as above but with v10.0 Gateways which run a MySQL v8 database.

## Setup Inventory 

Copy the [inventories/sample](/inventories/sample) directory to `inventories/tutorial`.

Update the `hosts` file in this new directory with your Gateway deployment information. Guidelines are [here](inventories/README.md).

Note: for this scenario, the upgraded and source Gateway hostnames are identical!

For example:
```yaml
---
all:
  hosts:
  children:
    gateway:
      children:
        gateway_mysql:
          children:
            gateway_primary_db:
              hosts:
                source-db-gw.placeholder.com:
                  src: source-db-gw.placeholder.com
            gateway_failover_db:
              hosts:
                source-failover-db-gw.placeholder.com:
                  src: source-failover-db-gw.placeholder.com
        gateway_processing_node:
          hosts:
            source-processing-gw.placeholder.com:
                src: source-processing-gw.placeholder.com
```

## Setup Passwords

Enter Gateway passwords in the inventory's `/group_vars/vault` file. 
Refer to [Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html).

The commands in this guide assume you are using a vault password file to encrypt the vault file, 
and that you have [configured the path](https://docs.ansible.com/ansible/latest/user_guide/playbooks_vault.html) 
in `ansible.cfg` so it does not need supplied as a command-line argument in every step.

## Run Pre-Upgrade Analyzer on Source Gateways

Follow the setup steps in the [README](roles/gateway_preupgrade_analyzer/README.md).

Run `ansible-playbook -i inventories/tutorial/hosts ./playbooks/gateway-preupgrade-analyzer.yml`

A report is generated, which shows output from a MySQL 8 upgrade checker utility, and lists items that you 
may need to manually move over to the upgraded Gateways at the end of this process. Review this report before proceeding. 

Note: If customized, the MySQL 5.7 `my.cnf` file will need to be manually backed up and migrated.

## Export Gateway Database

Follow the setup steps in the [README](roles/gateway_export_database/README.md)

Run `​ansible-playbook -i inventories/tutorial/hosts ./playbooks/gateway-database-export.yml`

This takes a database dump of the source Gateway database.

## Backup Gateway Configurations

Follow the setup steps in the [README](roles/gateway_basic_backup/README.md)

Run `​ansible-playbook -i inventories/tutorial/hosts ./playbooks/gateway-basic-backup.yml`

This will backup Gateway configuration files on the file system for each source Gateway.

## Re-Image Database Nodes to Gateway v10

At this point, we will need to manually re-image the two source Gateway VMs listed under inventory groups `gateway_primary_db` and `gateway_failover_db`.

Follow TechDocs instructions for [Virtual Machine Re-image](https://techdocs.broadcom.com/content/broadcom/techdocs/us/en/ca-enterprise-software/layer7-api-management/api-gateway/10-0/install-configure-upgrade/upgrade-the-gateway/virtual-machine-reimage.html).

## Configure Gateway Database Replication

Follow the setup steps in the [README](roles/gateway_replicate_database/README.md).

Run `​ansible-playbook -i inventories/tutorial/hosts playbooks/gateway-database-replication.yml`

This playbook will setup replication on the Gateways listed in the inventory under groups `gateway_primary_db` and `gateway_failover_db`.

## Configure Database-Node Gateways

Follow the setup steps in:
- [README](roles/gateway_primary_db_node/README.md)
- [README](roles/gateway_processing_node/README.md)

`ansible-playbook -i inventories/tutorial/hosts ./playbooks/gateway-autoprovision-nodes.yml`

The sample [playbooks/gateway-autoprovision-nodes.yml](playbooks/gateway-autoprovision-nodes.yml) will configure all Gateways.
However, we can only configure the two re-images nodes right now.

Create a new playbook `playbooks/gateway-autoprovision-database-nodes.yml` containing
```yml
- name: Autoprovision primary database Gateway node.
  hosts: gateway_primary_db
  vars:
    ansible_connection: local
    ansible_python_interpreter: "{{ ansible_playbook_python }}"
  roles:
    - gateway_primary_db_node

- name: Autoprovision failover database Gateway node.
  hosts: gateway_failover_db
  import_role:
    name: gateway_processing_node
  vars:
    ansible_connection: local
    ansible_python_interpreter: "{{ ansible_playbook_python }}"
    configure_db: true
```

Run `​ansible-playbook -i inventories/tutorial/hosts ./playbooks/gateway-autoprovision-database-nodes.yml`

This playbook will run a headless configuration of the Gateways.

## Import Gateway Database

Follow the setup steps in the [README](roles/gateway_import_database/README.md).

Run `​ansible-playbook -i inventories/tutorial/hosts ./playbooks/gateway-database-import.yml`

This will import the database dump from the source Gateway into the new v10 Gateway listed under the `gateway_primary_db` inventory group.

## Configure Remaining Processing Gateways 

Follow the setup steps in:
- [README](roles/gateway_processing_node/README.md)

The sample [playbooks/gateway-autoprovision-nodes.yml](playbooks/gateway-autoprovision-nodes.yml) will configure all Gateways.
However, only need to configure the remaining processing Gateways.

Create a new playbook `playbooks/gateway-autoprovision-processing-nodes.yml` containing
```yml
- name: Autoprovision processing Gateways.
  hosts: gateway_processing_node
  vars:
    ansible_connection: local
    ansible_python_interpreter: "{{ ansible_playbook_python }}"
  roles:
    - gateway_processing_node
```

Run `​ansible-playbook -i inventories/tutorial/hosts ./playbooks/gateway-autoprovision-processing-nodes.yml`

This playbook will run a headless configuration of the Gateways.

## Restore Gateway Configurations

Follow the setup steps in the [README](roles/gateway_basic_restore_backup/README.md).

Run `​ansible-playbook -i inventories/tutorial/hosts ./playbooks/gateway-database-import.yml`

This will restore the backup Gateway configuration files from the earlier backup step.

## Update Destination Gateway Schema

Follow the setup steps in the [README](roles/gateway_import_database/README.md).

Run `​ansible-playbook -i inventories/tutorial/hosts ./playbooks/gateway-database-upgrade.yml`

This will update the database schema for Gateway v10.

## Install License using Policy Manager

Follow the setup steps in the [README](roles/gateway_install_license/README.md).

Run `​ansible-playbook -i inventories/tutorial/hosts ./playbooks/gateway-install-license.yml`

This will install your license file onto the Gateway cluster. The existing v9 license is not removed, you will need
to manually remove it using Policy Manager.

## Update Hostnames used by OAuth Toolkit

** This step does not apply for this scenario! Gateway hostnames are not being changed. **

If Gateway hostnames were changed after re-imaging, then you would perform this step at this point in the upgrade. 

Follow the setup steps in the [README](roles/gateway_update_otk_hostnames/README.md).

Run `​ansible-playbook -i inventories/tutorial/hosts ./playbooks/gateway-update-otk-hostnames.yml`

This will update the Gateway hostnames used by OAuth Toolkit.

## Restart Gateways 

Run `​ansible-playbook -i inventories/tutorial/hosts ./playbooks/gateway-restart.yml`

This will restart all of the Gateways in the upgraded cluster. 

## Verify the Upgraded Gateways

Run your verification procedures to ensure the Gateways are running successfully.
