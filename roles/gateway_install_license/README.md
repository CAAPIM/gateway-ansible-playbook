Configure gateway license
======================

This role installs licenses on a Gateway cluster. It needs to be run on only one Gateway node (see Requirements).

Requirements
------------
* Place license (*.xml) files on the control node in the directory (default /tmp/license) defined in `controller_dir_license_location` (see Role Variables section). 
* Create this directory and copy all license files to this folder if it does not exist.
* User for running Ansible should have access to this directory and files within it.

* Specify gateway's hostname/IP in the hosts file [gateway_primary_db]] section.
    ```
    [gateway_primary_db]
    gateway.placeholder.com 
  
     ```  

Role Variables
--------------
Under group_vars/all/vars
* gateway_bootstrap_dir - directory to store license files.
* gateway_license_upload_dir - temp directory to store license files.
* gateway_license_install_dir - final directory to store license files.

Under group_vars/all/vars
* controller_dir_license_location - local directory to store the license file(s).
  * Default location: /tmp/license



Dependencies
------------
gateway is not running ( gateway process controller is running )


Example Playbook
------------
file: playbooks/getway-license-install.yml using ssgconfig as ansible_user
   
    > ansible-playbook playbooks/gateway-install-license.yml -i inventories/test/hosts --vault-password-file vault-password-file.txt

alternative playbook to install license using root as ansible_user using roles/gateway_install_license/tasks/install_license_as_root.yml
    
    
    # Configure license on primary gateway node
    - name: Execute gateway license installation in gateway
      hosts: gateway
      gather_facts: false
      
      vars:
        ansible_connection: ssh
        ansible_user: root
      tasks:
        - name: Stop the Gateway application
          import_role:
            name: gateway_common
            tasks_from: stop_gateway
       
        - name: Config gateway license
          import_role:
            name: gateway_install_license
            tasks_from: install_license_as_root
        
        - name: Start the Gateway application
          import_role:
            name: gateway_common
            tasks_from: start_gateway
    
        - name: cleanup
          file:
            path: "{{gateway_bootstrap_dir}}"
            state: absent
       
  
     
    