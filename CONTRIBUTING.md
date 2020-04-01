# Contribute to Gateway Ansible Playbook
Contributions are welcome and much appreciated. Just follow these easy steps to contribute.

## Code Standard and Guidelines
For consistency, we ask that you adhere to some basic code guidelines when contributing to the Gateway Playbook. See the [Code Standard and Guidelines](GUIDELINES.md) for details.

### Use ansible-lint
Setup ansible-lint with pre-commit:
- `pip install -r requirements.txt`
- `pre-commit install`
- Test it out by adding extra whitespace to one of the .yml files and try to commit the change. The linter scan should fail and prevent the dirty commit:
  - ```bash
    Ansible-lint.............................................................Failed
    - hook id: ansible-lint
    - exit code: 2
    
    [201] Trailing whitespace
    playbooks/update-expired-password-root.yml:4
        - name: Check if user password was not updated
    ```
