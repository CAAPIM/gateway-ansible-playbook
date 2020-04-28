Set up HSM Luna Client On Gateway Appliance
------------
This role will automate the setup and configuration of a Luna client (ver7.2) on a Gateway Appliance

Please note Luna client is not provided in this repository. You can download Luna from vendor and place it under files directory.

Requirements
------------

Place the required files under the `files` directory:
- LunaClient_7.2.0-220_Linux.zip
- sshpass-1.06.tar.gz (if performing off-line setup)
- [get-pip.py](https://pip.pypa.io/en/stable/installing/) (if performing off-line setup)

* A configured gateway appliance and enable the root access.
* Specify source gateway's hostname/IP in the hosts file [gateway_hsm] section.

    ```
     [gateway_hsm]
     gw94-node3.placeholder.com
     10.xxx.xxx.xxx
     ```  
      
  Specify ssh connection type and user in role host_vars/"{{ gateway_appliance_IP_address/hostname}}".yml (Ex: host_vars/host_vars/gw94-node3.placeholder.com.yml  )
    ```
   ansible_connection: ssh
   ansible_user: root
   ansible_password:
     ```         
* Specify HSM Luna(the one you want to register with)'s hostname/ip in the hosts file [hsm:children] section.
    ```
  Ex: [hsm_luna7]
    luna7.placeholder.com
     ```            
    
  Specify ssh connection type and user in role and other related variables in host_vars/"{{ hsm_hostname/IP}}".yml (Ex: host_vars/luna7.placeholder.com.yml )

    ```
    ansible_connection: ssh
    ansible_user: admin
    ansible_password:
     ```       
  
* Also specify the HSM (the one you want to register with)'s hostname/ip: {{ hsm_hostname }}in the playbooks/gateway-auto-hsm-luna-configure.yml, as the parameter for the role.
    
       
       Ex: { role: gateway_configure_hsm, hsm_hostname: 'luna7.placeholder.com'}
  
             
Variables
------------
**Note**: These variables need to be set in the host_vars/luna*.placeholder.com.yml file. If your are using another version of luna client 
, please create a new host_vars file under the folder host_vars, and use the same name as your hostname.

Utilize Ansible Vault for these configurations.

* hsm_user: user name for SafeNet HSM server
* hsm_pass: password for SafeNet HSM server
* partition_name: The partition name that assigned to the client
* partition_pass: The partition password

Dependencies
------------
n/a

Notes
------------
If the gateway appliance cannot have internet access, users should comment out this line: '- import_tasks: pip_install_online.yml' and uncomment this line: '- import_tasks: pip_install_locally.yml';
Also, comment out this line:'- import_tasks: sshpass_install_online.yml' and uncomment this line: '- import_tasks: sshpass_install_locally.yml'

Sample usage
------------
`ansible-playbook -i ./inventories/test/ ./playbooks/gateway-auto-hsm-configure.yml -vvv`

or with vault password:

`ansible-playbook -i ./inventories/test/ ./playbooks/gateway-auto-hsm-configure.yml -vvv --vault-password-file vault-password-file.txt`


