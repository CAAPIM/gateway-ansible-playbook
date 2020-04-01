# Description
This role will configure replication for the primary gateway MySQL 8 database to provide failover.
This role requires root access to call add_slave_user.sh and create_user.sh which come with gateway version 10 default image.

### Requirements
- The role must be executed with the Ansible user having root privilege.
   root access to run /opt/SecureSpan/Appliance/bin/add_slave_user.sh and create_user.sh
   root access to update /etc/my.cnf
-  The network of the target gateways must be pre-configured.
-  The MySQL 8 server must be started

### Dependencies:
There are no dependencies upon other roles.

### Sample Usage
Modify the hosts file under `gateway`
  - define a primary gateway in gateway_primary_db group
  - define a failover gateway in gateway_failover_db group
  
`ansible-playbook gateway-database-replication.yml -i hosts --vault-password-file vault-password.txt`
