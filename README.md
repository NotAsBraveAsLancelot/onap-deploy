# onap-deploy
This is deployment mechanism follows the procedure outlined in the ONAP documentation [here](https://docs.onap.org/en/dublin/submodules/oom.git/docs/oom_setup_kubernetes_rancher.html).

Openstack is the targeted cloud provisioner for the following suite, however, the ansible playbooks will for the most part apply in multiple environments, I just have not tested any others.



# User Guide

##  Prerequisites 

- Python 3  installed
- `python-openstackclient` and `python-heatclient` installed, for more information see [here](https://docs.openstack.org/newton/user-guide/common/cli-install-openstack-command-line-clients.html)
- `ansible` installed, for more information see [here](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
- The Openstack rc file available
- Your current working directory is the base directory of this project
- Required Flavors available in Openstack
- An Ubuntu 18.04 image available in Openstack
- An existing key available in Openstack
- Sufficient privileges to create instances & volumes in Openstack

## Instance Provisioning

1. Source your Openstack rc file
2. Create the stack: `openstack create --template ./onap-heat.yaml <stack_name>` 
3. Wait for the stack to be created, `openstack stack list` - status should show 'CREATION_COMPLETED'

## Ansible Hosts Generation

1. Add the executable right to the stack_parse script `chmod +x stack_parse`

2. Pipe the stack outputs into the stack_parser, providing the remote instance user, and path to the ssh key file locally

   ```shell
   openstack stack output show <stack_name> --all  -f json | ./stack_parse host <user> <path_to_key>
   ```

   

   ## Environment Configuration

1. Run the main playbook, pointing ansible to the hosts file generated in the previous step, specifying if you want all of ONAP installed (`deploy_all=true`) or just built and ready to be installed with your own custom values files (`deploy_all=false` or not present)

   ```shell
   ansible-playbook playbooks/main.yaml -i hosts --extra-vars "deploy_all=true"
   ```

   

