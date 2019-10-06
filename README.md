# Requirements

#### OS
Have Ubuntu 18.04 installed with following packages: virtualenv, python-pip and docker

#### HOST MACHINE
GCP n1-standard-2 machine type at minimum (Standard machine type with 2 vCPUs and 7.5 GB of RAM)

#### Routing/Connectivity
The host machine should have connectivity to the devices you want to manage. 

# Build environment using one of the options below

## (Option 1) Use virtualenv for executing ansible playbooks
```
# Clone Ansible repo
git clone --branch master git@github.com:andreistefanciprian/manage_network_devices_with_ansible.git
cd manage_network_devices_with_ansible

# Create virtual environment for running Ansible tasks
virtualenv .venv

# Activate ansible virtual environment
source .venv/bin/activate

# Install package requirements
pip install -r requirements.txt

# You might also need to install the Juniper role
ansible-galaxy install Juniper.junos
```
## (Option 2) Use the Ansible for Junos OS Docker Image
```
git clone --branch master git@github.com:andreistefanciprian/manage_network_devices_with_ansible.git
docker run -it --rm -v $PWD:/project juniper/pyez-ansible ash
cd /project
```

## (Option 3) Use docker-compose
```buildoutcfg
docker-compose run junos_ansible
```

# Take a backup of current config before executing RFC
```
ansible-playbook playbooks/junos/retrieve_config/retrieve_config.yml --ask-vault-pass
```

# Run RFC playbooks (configuration changes)

```
PLAYBOOK='playbooks/junos/change_config/change_config_and_commit.yml'

# Run ansible task with 5 parallel connections (default) at a time
ansible-playbook $PLAYBOOK --ask-vault-pass

# Run ansible task with 20 parallel connections at a time
ansible-playbook -f 20 $PLAYBOOK --ask-vault-pass

# Run ansible task with inventory specified from command line
ansible-playbook -i $PLAYBOOK --ask-vault-pass
```

# Send monitoring commands to device
```
ansible-playbook playbooks/junos/monitor_debug/show_commands.yml --ask-vault-pass
```

# Rollback device config to previous commit number
You'll be prompted for a commit number to revert device config to!
```
ansible-playbook playbooks/junos/rollback/rollback.yml --ask-vault-pass
```

# Deativate/Reactivate virtual environment
```
# deactivate env
deactivate

# activate env
source .venv/bin/activate
```

# Ansible Cheat Sheet
```
# Run playbook against all devices
ansible-playbook playbooks/junos_local_cert_check.yml

# Run playbook against a subset of devices
ansible-playbook playbooks/junos_local_cert_check.yml --limit=10.170.254.128
ansible-playbook playbooks/junos_local_cert_check.yml --limit=10.170.254.128,10.170.255.240

# Run playbook against a subset of devices using wildcards
ansible-playbook playbooks/junos_local_cert_check.yml --limit='10*'

# Display which devices will be managed, but don't actually run the playbook
ansible-playbook playbooks/junos_local_cert_check.yml --limit='10*' --list-hosts

# Run playbook using vault encrypted credentials
ansible-playbook playbooks/junos/vault_auth_sample/check_os_version.yml --ask-vault-pass

```

# Manage secrets with ansible-vault
```
# View credentials
ansible-vault view playbooks/junos/junos-vault.yml

# Edit credentials
ansible-vault edit playbooks/junos/junos-vault.yml

More details at:
https://docs.ansible.com/ansible/latest/user_guide/vault.html
```

# Juniper debug/monitoring commands
```
# Check last commit (operational mode)
show system commit

# Verify if there are hanging configs not committed (configuration mode)
commit check
show | compare

# Convert text junos command in xlm format (operational mode)
show system uptime | display xml rpc
```