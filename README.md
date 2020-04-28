# Gateway Ansible Playbooks

## For Upgrading to Gateway 10
This project serves as the starting point for automating upgrading to Gateway 10 with Ansible roles.

## Get Started

### Prerequisites
The following software must be installed on the Ansible Controller:
* Python 2.7.+ or 3.3+
* pip
> For most linux distro please refer to [this guide](https://www.tecmint.com/install-pip-in-linux/)

> You can also use the following command.

> `curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py` 

> `python get-pip.py --user`

* ansible
> `pip install ansible --upgrade`
* pyvmomi
> `pip install pyvmomi --upgrade`
* pexpect
> `pip install pexpect`

* sshpass
> Linux: use preferred package manager to install sshpass, ie. `yum install sshpass`

> Mac: use [Homebrew](https://brew.sh/) to install, ie. `brew install http://git.io/sshpass.rb`

* MySQL shell
> download from https://dev.mysql.com/downloads/shell/ select Operating System Linux/macOS based on the Ansible control node
> Linux: ie. `rpm -ivh mysql-shell-8.0.19-1.el6.x86_64.rpm`


### Development Tools
The following tools can be used to create/modify Ansible roles and playbooks.
* [Visual Studio Code](https://code.visualstudio.com/)
* [VS Code Ansible Extension](https://marketplace.visualstudio.com/items?itemName=vscoss.vscode-ansible)


## Inventories
To use Ansible, your inventories must be configured. 
Inventories are defined [here](/inventories), and the [sample inventory](/inventories/sample) is a good starting point. Several Environment templates have been created.
To add machine to the inventories, edit the `hosts` file under each directory.

To view a inventory
```bash
ansible-inventory -i ./inventories/sample --list // list view
ansible-inventory -i ./inventories/sample --graph // graph view
```

For detailed usage on how to use `ansible-inventory` command, please take a look at [here](https://docs.ansible.com/ansible/latest/cli/ansible-inventory.html)

## Gateway Credentials
For Ansible to upgrade the Gateway, the Gateway credentials must be specified.  Please [see](/inventories/sample/group_vars/all)
 for the sample vars and vault files. 
Remember: ** NEVER CHECK IN PASSWORD FILES **.

Once the password vault file has been completed, secure it using Ansible Vault. Please see
the Ansible Vault section below.

## Roles
Following roles has been created as building block for automation
* [Gateway Common](/roles/gateway_common)
* [Gateway Basic Backup](/roles/gateway_basic_backup)
* [Gateway Export Database](/roles/gateway_export_database)
* [Gateway Import Database](/roles/gateway_import_database)
* [Gateway Pre-upgrade Analyzer](/roles/gateway_preupgrade_analyzer)
* [Gateway Primary Database Node](/roles/gateway_primary_db_node)
* [Gateway Processing Node](/roles/gateway_processing_node)

## Playbooks
Playbooks can be found [here](/playbooks).

These playbook are created as reference implementation and can be used to upgrade gateway to version 10. 

To run each playbook, please run it under the *project root* directory
```bash
ansible-playbook -i ./inventories/sample gateway-autoprovision-nodes.yml
```

For detailed usage on how to use `ansible-playbook` command please take a look at [here](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)

## Ansible Vault
`ansible-vault` can help you store password securely on your control node. ** NEVER CHECK IN PASSWORD FILES **

For simpilicty, you can use `ansible-vault` to generate encrypted string and embed into yaml files, or encrypt entire files.

A [vault password file](https://docs.ansible.com/ansible/latest/user_guide/vault.html#providing-vault-passwords) can be created as well, so that the vault password does not need to be typed manually when executing playbooks or roles.

For example
```bash
ansible-vault encrypt_string --vault-password-file a_password_file 'foobar' --name 'the_secret'
```
## Troubleshooting
* To tell a playbook run is succeed or failed, please check "PLAY RECAP" section at the end of the output. Column "unreachable" and  "failed" have the information of the failed hosts
    
    Succeed example
    ```bash
    PLAY RECAP *************************************************************************************************************
    rel94.placeholder.com    : ok=8    changed=7    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   
    ```
    Failed example
    ```bash
    PLAY RECAP *************************************************************************************************************
    rel94.placeholder.com    : ok=0    changed=0    unreachable=1    failed=0    skipped=0    rescued=0    ignored=0   
    ```


* Playbook level:
Pass -v flag to the ansible-playbook command for verbose mode , Ansible will show the output on your terminal. 
For example: -vvv for more, -vvvv to enable connection debugging.
```bash
ansible-playbook -i ./inventories/sample gateway-autoprovision-nodes.yml -vvvv
```
* Role task level:
To keep sensitive values such as decrypted password out of your logs, tasks that expose them are marked with the no_log attribute and set to true by default.
For debugging purpose, you can override "no_log" default at the command line to enable Ansible output on your terminal temporarily. 
```
ansible-playbook playbooks/gateway-autoprovision-nodes.yml --extra-vars no_log_flag=false
```
* For Playbook Debugger please refer to [this guide](https://docs.ansible.com/ansible/latest/user_guide/playbooks_debugger.html) 

## Tutorial

A walkthrough for how to configure and run playbooks for an in-place Gateway upgrade scenario is available in the [TUTORIAL.md](TUTORIAL.md).

## Writing Your Own Playbooks & Roles

Writing custom playbooks and roles comes down to following the Ansible conventions followed by this project. To that end, 
the [Ansible documentation](https://docs.ansible.com/ansible/latest/user_guide/index.html) combined with the sample code in the [playbooks](playbooks/) and 
[roles](roles/) included in this project are the best references.

There are 3 major components to consider.

### 1. Inventory and Group Variables
Gateway configuration and passwords are specified for each environment under `inventories`. Existing properties and any custom
properties added under `group_vars/all/` apply to all hosts listed in the inventory (`hosts.yml` file). Ansible provides several 
mechanisms for overriding properties on individual or groups of hosts. 
  - [Controlling how Ansible behaves: precedence rules](https://docs.ansible.com/ansible/latest/reference_appendices/general_precedence.html#controlling-how-ansible-behaves-precedence-rules)
  - [Variable precedence: Where should I put a variable?](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable)

### 2. Roles

Roles are placed under the `roles` directory. Custom roles can be placed in this directory, or modify existing roles if required for your
Gateway environment. Roles can have role-level configuration, and can also consume Gateway configurations under `inventories/.../group_vars`, 
host variables, etc.
  - [Creating Roles](https://galaxy.ansible.com/docs/contributing/creating_role.html)
  - [Reusable Roles](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html)

### 3. Playbooks

Playbooks are placed under the `playbooks` directory. Custom playbooks can be placed in this directory.
  - [Working With Playbooks](https://docs.ansible.com/ansible/latest/user_guide/playbooks.html)

