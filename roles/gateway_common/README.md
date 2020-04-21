# Description
This role contains tasks common to all Gateways in a cluster.

## Update Expired Gateway OS User Passwords

The following tasks are run by the `gateway_common` role:
- initial-update-ssgconfig-password.yml
- initial-update-root-password.yml

- The task order matters: `ssgconfig` password must be updated before the `root` password.

When a new Gateway is deployed, the default OS user password must be changed during the first login for the `ssgconfig` and `root` users.

These tasks will update the expired password using the passwords provided in vault files (see Requirements).

## Other Tasks

#### Stop and Start Gateway
- stop_gateway.yml
- start_gateway.yml
- start_process_controller.yml
- restart_gateway_appliance.yml

Note: these tasks are not run by the `gateway_common` role. To use them, they may be imported into any play. See `Sample Usage` section.

#### Status Checks
- port_status.yml

Note: these tasks are not run by the `gateway_common` role. To use them, they may be imported into any play. See `Sample Usage` section.

## Requirements

### Playbook

This role must be run from the Ansible control node. 

### Setup Variables

Set Gateway OS user passwords in the `group_vars/all/vars` file:
```yaml
- vault_gateway_default_password
- vault_gateway_ssgconfig_password
- vault_gateway_root_password
```

## Dependencies:
None.

## Sample Usage
- gateway_common role
  ```yaml
  - name: Update expired passwords on new Gateways.
    hosts: gateway
    connection: local
    vars:
      ansible_python_interpreter: "{{ ansible_playbook_python }}"
    roles:
      - gateway_common
  ```

- restart_gateway_appliance.yml
  ```yaml
  - name: Restart all destination Gateways
    hosts: gateway_dest
    gather_facts: no
    tasks:
      - include_role:
          name: gateway_common
          tasks_from: restart_gateway_appliance
  ```
