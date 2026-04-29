# Ansible Automation Platform Configuration as Code examples template

This is a combination of all the Red Hat CoP Config as Code collections to deploy and configure AAP. This is built for multi environment (meaning multiple AAP instances/clusters). If you want an object across all environments put it in the correct file/list under the `config/all/` directory. If there is a specific object for only one environment then put it under that environment's folder (e.g., `config/dev/`, `config/qa/`, `config/prod/`)[^1].

[^1]: If you only have/want one environment you could delete dev/qa/prod folders in the config directory and remove all the environment suffixes (e.g., `_dev`, `_qa`, `_prod`) from vars, keeping only `_all`. Also if you want to have each team/group maintain their own org/code in their own repo, see the repo_per_org branch.

The main branch is built for 2.5+ AAP if you are running 2.4 or lower make sure to copy the template branch aap2.4

## Secrets Management

This repository uses `secrets.yml` files with inline encrypted vault strings instead of separate vault files. Each environment has its own secrets file located at `config/<env>/secrets.yml` (e.g., `config/dev/secrets.yml`, `config/qa/secrets.yml`, `config/prod/secrets.yml`).

You will need to replace the encrypted values in your environment's `secrets.yml` file with your own vault-encrypted strings containing these variables:

```yaml
---
console_token: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          ...your encrypted string...

redhat_api_token: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          ...your encrypted string...

rh_username: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          ...your encrypted string...

rh_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          ...your encrypted string...

root_machine_pass: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          ...your encrypted string...

hub_api_user_pass: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          ...your encrypted string...

controller_api_user_pass: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          ...your encrypted string...

aap_pass: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          ...your encrypted string...

hub_token: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          ...your encrypted string...

vault_pass: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          ...your encrypted string...
...
```

### Variable Descriptions

- `console_token`: Token from console.redhat.com for Red Hat Console access
- `redhat_api_token`: API token for Red Hat API (see link below)
- `rh_username`: Red Hat user login (used to attach subscriptions to controller)
- `rh_password`: Password for Red Hat account
- `root_machine_pass`: Password for root user on builder (if not root user, more changes will need to be made)
- `hub_api_user_pass`: Password for Hub API user (will be created and used, can be generated)
- `controller_api_user_pass`: Password for Controller API user (will be created and used, can be generated)
- `aap_pass`: Admin account password for gateway (defaults to Password1234! if not provided)
- `hub_token`: Hub token to pull collections (recommended to save in vault for more reliable usage vs generating on the fly)
- `vault_pass`: Password for vault credential type

### Creating Encrypted Strings

To create vault-encrypted strings for your `secrets.yml` file:

```bash
# Encrypt a single value
ansible-vault encrypt_string 'your_secret_value' --name 'variable_name'

# Or interactively
ansible-vault encrypt_string --stdin-name 'variable_name'
```

**_NOTE:_** Do not forget to update your inventory files in `inventory/` directory, replacing the `HERE` lines with your actual FQDNs. If you do not have a builder server you can use hub for this. Also update `scm_url` in `config/all/controller_projects.yml` with your git URL.

## Getting Help

We are on the Ansible Forums and Matrix, if you want to discuss something, ask for help, or participate in the community, please use the #infra-config-as-code tag on the fourm, or post to the chat in Matrix.

[Ansible Forums](https://forum.ansible.com/tag/infra-config-as-code)

[Matrix Chat Room](https://matrix.to/#/#aap_config_as_code:ansible.com)

## Documentation

[For documentation and examples, please visit us on Galaxy]([https://console.redhat.com/ui/repo/published/infra/aap_configuration/](https://console.redhat.com/ansible/automation-hub/repo/validated/infra/aap_configuration/docs/))

## Requirements

The supported collections that contains the modules are required for this collection to work, you can copy this requirements.yml file example.

```yaml
---
collections:
  - name: ansible.platform
  - name: ansible.hub
  - name: ansible.controller
    version: ">=4.6.0"
  - name: ansible.eda
  - name: infra.aap_configuration
...
```

## Links to Ansible Automation Platform Collections

|                                      Collection Name                                |            Purpose            |
|:-----------------------------------------------------------------------------------:|:-----------------------------:|
| [ansible.platform repo](https://github.com/ansible/ansible.platform)                | gateway/platform modules      |
| [ansible.hub repo](https://github.com/ansible-collections/ansible_hub)              | Automation hub modules        |
| [ansible.controller repo](https://github.com/ansible/awx/tree/devel/awx_collection) | Automation controller modules |
| [ansible.eda repo](https://github.com/ansible/event-driven-ansible)                 | Event Driven Ansible modules  |

## Links to other Validated Configuration Collections for Ansible Automation Platform

|                                      Collection Name                                                  |                      Purpose                      |
|:-----------------------------------------------------------------------------------------------------:|:-------------------------------------------------:|
| [AAP Configuration](https://github.com/redhat-cop/infra.aap_configuration)                            | Main collection                                   |
| [AAP Configuration Extended](https://github.com/redhat-cop/aap_configuration_extended)                | Where other useful roles that don't fit here live |
| [EE Utilities](https://github.com/redhat-cop/ee_utilities)                                            | Execution Environment creation utilities          |
| [AAP installation Utilities](https://github.com/redhat-cop/aap_utilities)                             | Ansible Automation Platform Utilities             |
| [Ansible Validated Gitlab Workflows](https://gitlab.com/redhat-cop/infra/ansible_validated_workflows) | Gitlab CI/CD Workflows for ansible content        |
| [Ansible Validated GitHub Workflows](https://github.com/redhat-cop/infra.ansible_validated_workflows) | GitHub CI/CD Workflows for ansible content        |

## AAP config

Configure an existing AAP installation with your configuration as code:

```bash
ansible-playbook -i inventory/inventory_dev.yml -l dev playbooks/aap_config.yml --ask-vault-pass
```

## custom ee

currently doesn't work in CLI, expected to be run in Controller

## custom collections

currently doesn't work in CLI, expected to be run in Controller

## aap utilities (aap installer)

Install AAP using the aap_utilities collection:

```bash
ansible-playbook -i inventory/inventory_dev.yml playbooks/install_aap.yml --ask-vault-pass
```

Acquire your token at [redhat api](https://access.redhat.com/management/api/) see [access article](https://access.redhat.com/articles/3626371)

## install and configure

Install AAP and configure it in one step:

```bash
ansible-playbook -i inventory/inventory_dev.yml -l dev playbooks/install_configure.yml --ask-vault-pass -e "env=dev"
```

Acquire your token at [redhat api](https://access.redhat.com/management/api/) see [access article](https://access.redhat.com/articles/3626371)
