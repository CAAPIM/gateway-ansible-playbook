# Gateway Ansible Playbooks

## For Upgrading to Gateway 10
This project serves as the starting point for automating upgrading to Gateway 10 with Ansible roles.

## Get Started

### Prerequisites
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

### Development Tools
* [Visual Studio Code](https://code.visualstudio.com/)
* [VS Code Ansible Extension](https://marketplace.visualstudio.com/items?itemName=vscoss.vscode-ansible)


## Inventories
To use ansible, your inventories must be configured. 
Inventories are defined [here](/inventories), and the [test inventory](/inventories/test) is a good starting point. Several Environment templates have been created.
To add machine to the inventories, edit the `hosts` file under each directory.

To view a inventory
```bash
ansible-inventory -i ./inventories/test --list // list view
ansible-inventory -i ./inventories/test --graph //graph view
```

For detailed usage on how to use `ansible-inventory` command, please take a look at [here](https://docs.ansible.com/ansible/latest/cli/ansible-inventory.html)

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
ansible-playbook -i ./inventories/test gateway-autoprovision-nodes.yml
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
ansible-playbook -i ./inventories/test gateway-autoprovision-nodes.yml -vvvv
```
* Role task level:
To keep sensitive values such as decrypted password out of your logs, tasks that expose them are marked with the no_log: true attribute.
For debugging purpose, you can change setting to "no_log: false " temporarily to enable Ansible output on your terminal. 
Remember to set back to true when debugging is done.
```
- name: Dump gateway source database
  no_log: true # don't log decrypted passwords in responses
  shell: 
    cmd: mysqldump {{ database_name }} --routines -u{{ database_user}}  -p{{ database_pass }} > {{remote_db_temp_dir}}/ssg.sql
```
* For Playbook Debugger please refer to [this guide](https://docs.ansible.com/ansible/latest/user_guide/playbooks_debugger.html)   
