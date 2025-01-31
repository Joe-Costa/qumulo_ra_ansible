# Ansible Playbooks for creating Run-Anywhere Qumulo Clusters

The Ansible playbooks listed here will allow for simpler intallation of Qumulo Core on RHEL, Rocky and Ubuntu clients.

This tooling is meant for on-prem or virtual cluster installs and is not a replacement for Terraform for provisioning Cloud
Native Qumulo clusters.

**Important:**

Please review our Installing Qumulo Core Package documentation first.

https://docs.qumulo.com/administrator-guide/getting-started/installing-product-package.html


**Currently RHEL and Rocky Linux only are supported!  Ubuntu support is in beta!**


## Directions:

- Ensure your local machine has `ansible` installed:

    `pip install ansible`

Windows users: Ansible requires Windows Subsystem for Linux (WSL)

- Create a new directory for this Ansible project
- Clone this repo into this new directory and cd into the newly cloned directory
- Log into https://nexus.qumulo.com and download the correct `qumulo-core` install package from `Downloads > Cloud Native Qumulo Downloads`
    - qumulo-core.rpm for RHEL and Rocky
    - qumulo-core.deb to Ubuntu and Debian
- **Optional** - Copy `qumulo-core.rpm/.deb` direclty to each future node's `/tmp` directory.
    - This might be a desirable option if the connection between the Ansible controller and the nodes is slow.
    - You may be able to modify the playbook `gravycopy.yml` to help you with this.
- Ensure this file is in the same directory as this project
- Create your Ansible `hosts` file (Lost into the sample `hosts` file provided)
    - IPs or FQDNs can be used
    - `hosts` files can contain many sections, in this case we will be using `[nodes]`
- Edit the `remote_user` in the `qnode-setup.yml` file to match the remote Linux user for your hosts
- Copy your SSH public key to all your remote hosts

Example:

    `ssh-copy-id -i ~/.ssh/id_rsa.pub yourlinuxuser@1.2.3.4`

- Run the playbook:

    `ansible-playbook rhel-qnode-setup.yml -i hosts -K`

`-i` points to your hosts file, if ommiting this then Ansible will use the default `/etc/ansible/hosts` file

`-K` will prompt you for your remote Linux user's password