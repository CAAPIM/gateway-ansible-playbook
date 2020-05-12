# Gateway Ansible Playbooks

## Upgrading the Gateway
The Gateway Ansible playbooks can upgrade Gateways from 9.4 to 10.  It provides an upgrade path for virtual and hardware appliances. The main requirement for the in-place or expedited upgrades is that the new GW OVA or ISO must be installed on the target computer before the Ansible upgrade process takes place. The playbooks handle database clusters. Any data in the SSG database will be migrated.  This includes Solution Kits (MAG & OTK) and Server Module files (modular and custom assertions).  The components that were migrated along with the database may require further upgrade to work with Gateway 10.
	
Please note, the upgrade playbooks do not cover: Software Gateways, Docker, AMI, Azure Gateways. It also does not cover OTK installed on external databases.

## Background

Before start, it is important to have some basic knowledge on Ansible and how it works and how to setup Ansible control node. 

[How Ansible Works](https://www.ansible.com/overview/how-ansible-works)

[How to Install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

We recommend to use `pip` to install ansible.

To use `pip` if not installed on your machine. For most linux distro please refer to [this guide](https://www.tecmint.com/install-pip-in-linux/)

You can also use the following command.

```shell
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py

python get-pip.py --user
```
After `pip` is installed, simply run the following command to install Ansible.

`pip install ansible --upgrade`

## Get Started

### Prerequisites
After setup the Ansible control node, the following software must be installed on the Ansible control node to work with the playbooks in this repository.

* pexpect

>`pip install pexpect`

* sshpass
> Linux: use preferred package manager to install sshpass, ie. `yum install sshpass`

> Mac: use [Homebrew](https://brew.sh/) to install, ie. `brew install http://git.io/sshpass.rb`

* MySQL shell
> download from https://dev.mysql.com/downloads/shell/ select Operating System Linux/macOS based on the Ansible control node

> Linux: ie. `rpm -ivh mysql-shell-8.0.19-1.el6.x86_64.rpm`

* SSH connection to gateway nodes.
> By default, it is set to use `StrictHostKeyChecking=no`, so it will automatically accept new nodes. For more strict security, [this flag](/inventories/sample/group_vars/all/vars#L38) can be removed. 

### Development Tools
The following tools are recommended to use with Ansible development.

* [Visual Studio Code](https://code.visualstudio.com/)
* [VS Code Ansible Extension](https://marketplace.visualstudio.com/items?itemName=vscoss.vscode-ansible)


## Inventory
To use Ansible, your inventories must be configured. 
Inventories are defined [here](/inventories), feel free to use [sample inventory](/inventories/sample) as a base line to create your own environments.

To add machine to the inventories, edit the `hosts.yml` file under each directory.

It is recommend to add **all** your nodes in a cluster to the inventory before running any playbooks.

For detailed usage on how to use `ansible-inventory` command, please take a look at [here](https://docs.ansible.com/ansible/latest/cli/ansible-inventory.html)

## Gateway Credentials
**NEVER CHECK IN PASSWORD FILES**.

For Ansible to upgrade the Gateway, the Gateway credentials must be specified.  Please [see](/inventories/sample/group_vars/all) for the sample vars and vault files. 

Once the password vault file has been completed, secure it using Ansible Vault. Please see the [Ansible Vault](#ansible-vault) section below for more info.

#### Password Rules
[Tech Doc Reference](https://techdocs.broadcom.com/content/broadcom/techdocs/us/en/ca-enterprise-software/layer7-api-management/api-gateway/9-3/reference/troubleshoot/troubleshooting-password-issues.html#concept.dita_2883f3fbcd8d265ee830c858df47b242a9a158a0_PasswordRules)

By default, gateway password must adhere to the following rules:
- Minimum 9 characters in length
- Contains at least two upper and two lowercase characters
- Contains at least two digits
- Contains at least two special characters
- The new password must not be a repeat of any of the five most recent passwords and at least 24 hours must have elapsed since the last password change.

_If password rule failed, any new node cannot be provisioned._

## Roles
Roles are the key to create reusable playbooks and they can be found [here](/roles).

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

* Ansible permission problem. 
In case there is ansible permission problem, you can execute the following commands to resolve the issue.
`chmod 755 /user/bin/ansible*`

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

