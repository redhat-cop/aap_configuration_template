# tower_setup

This set of playbooks sets up Tower to a base configuration. At present this is just an install and addition of certs.

## Running the playbooks

The playbooks should be run using `ansible-playbook tower_setup/install.yml -i <inventory>`. Two example inventories should be used as templates for different types of Tower deployments.

## Customisation

If you do not want to add certificates to the cluster, you must run the playbook with `--skip-tags install_certs`
