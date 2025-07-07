# Ansible Automation Platform Configuration as Code examples template

This is a combination of all the Red Hat CoP Config as Code collections to deploy and configure AAP. This is built for multi environment (meaning multiple AAP instances/clusters). If you want an object across all environments put it in the correct file/list under the all group. If there is a specific object for only one environment then put it under that environments folder[^1].

[^1]: If you only have/want one environment you could delete dev/test/prod folders in group_vars and remove all the _all added to vars in all group. Also if you want to have each team/group maintain their own org/code in their own repo, see the repo_per_org branch.

The main branch is built for 2.5+ AAP if you are running 2.4 or lower make sure to copy the template branch aap2.4

You will need to replace the vault files with your own with these variables:

```yaml
---
console_token: 'this is the one from console.redhat.com'
redhat_api_token: 'this is the one linked below about api token'
rh_username: 'redhat user login (this is used to attach your subs to controller)'
rh_password: 'password for redhat account'
root_machine_pass: 'password for root user on builder (if not root user more changes will need to be made)'
hub_api_user_pass: 'this will create and use this password can be generated'
controller_api_user_pass: 'this will create and use this password can be generated'
aap_pass: 'admin account pass for gateway, if none is given it will default to Password1234!'
hub_pass: 'hub admin account pass, if none is given it will default to Password1234!'
# hub_token: 'hub token to pull collections, it is best to save in vault for more reliable usage vs generating on the fly'
vault_pass: 'the password to decrypt this vault'
...
```

**_NOTE:_** Do not forget to update your inventory files replacing the `HERE` lines, if you do not have a `builder` server you can use `hub` for this. Also update `scm_url` in `group_vars/all/projects.yml` with your git URL.

## Getting Help

We are on the Ansible Forums and Matrix, if you want to discuss something, ask for help, or participate in the community, please use the #infra-config-as-code tag on the fourm, or post to the chat in Matrix.

[Ansible Forums](https://forum.ansible.com/tag/infra-config-as-code)

[Matrix Chat Room](https://matrix.to/#/#aap_config_as_code:ansible.com)

## Requirements

The supported collections that contains the modules are required for this collection to work, you can copy this requirements.yml file example.

```yaml
---
collections:
  - name: ansible.platform
  - name: ansible.hub
  - name: ansible.controller
  - name: ansible.eda
  - name: infra.aap_configuration
...
```


## Links to Ansible Automation Platform Collections

|                                      Collection Name                                |            Purpose            |
|:-----------------------------------------------------------------------------------:|:-----------------------------:|
| ansible.platform repo (no public repo for this collection)                          | gateway/platform modules      |
| [ansible.hub repo](https://github.com/ansible-collections/ansible_hub)              | Automation hub modules        |
| [ansible.controller repo](https://github.com/ansible/awx/tree/devel/awx_collection) | Automation controller modules |
| [ansible.eda repo](https://github.com/ansible/event-driven-ansible)                 | Event Driven Ansible modules  |

## Links to other Validated Configuration Collections for Ansible Automation Platform

|                                      Collection Name                                       |                      Purpose                      |
|:------------------------------------------------------------------------------------------:|:-------------------------------------------------:|
| [AAP Configuration Extended](https://github.com/redhat-cop/aap_configuration_extended)     | Where other useful roles that don't fit here live |
| [EE Utilities](https://github.com/redhat-cop/ee_utilities)                                 | Execution Environment creation utilities          |
| [AAP installation Utilities](https://github.com/redhat-cop/aap_utilities)                  | Ansible Automation Platform Utilities             |
| [AAP Configuration Template](https://github.com/redhat-cop/aap_configuration_template)     | Configuration Template for this suite             |

## AAP config

`ansible-playbook -i inventory_dev.yml -l dev playbooks/aap_config.yml --ask-vault-pass`

## custom ee

currently doesn't work in CLI, expected to be run in Controller

## custom collections

currently doesn't work in CLI, expected to be run in Controller

## aap utilities (aap installer)

`ansible-playbook -i inventory_dev.yml playbooks/install_aap.yml --ask-vault-pass`

Acquire your token at [redhat api](https://access.redhat.com/management/api/) see [access article](https://access.redhat.com/articles/3626371)

## install and configure

`ansible-playbook -i inventory_dev.yml -l dev playbooks/install_configure.yml --ask-vault-pass -e "env=dev"`

Acquire your token at [redhat api](https://access.redhat.com/management/api/) see [access article](https://access.redhat.com/articles/3626371)
