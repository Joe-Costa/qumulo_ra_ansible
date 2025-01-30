# Ansible Playbooks for creating Run-Anywhere Qumulo Clusters

The Ansible playbooks listed here will allow for simpler intallation of Qumulo Core on RHEL, Rocky and Ubuntu clients.

**Currently RHEL and Rocky Linux only are supported!  Ubuntu support will be available soon!**

## Directions:

- Ensure your local machine has `ansible` installed:

    `pip install ansible`

- Create a new directory for this Ansible project
- Clone this repo into this new directory
- Log into https://nexus.qumulo.com and download the correct `qumulo-core` install package from `Downloads > Cloud Native Qumulo Downloads`
    - qumulo-core.rpm for RHEL and Rocky
    - qumulo-core.deb to Ubuntu and Debian
- Ensure this file is in the same directory as this project
- Create your Ansible `hosts` file (Lost into the sample `hosts` file provided)
    - IPs or FQDNs can be used
- Edit the `remote_user` in the `qnode-setup.yml` file to match the remote Linux user for your hosts
- Copy your SSH public key to all your remote hosts

Example:

    `ssh-copy-id -i ~/.ssh/id_rsa.pub yourlinuxuser@1.2.3.4`
- Run the playbook:

    `ansible-playbook qnode-setup.yml -i hosts -K`

`-i` points to your hosts file, if ommiting this then Ansible will use the default `/etc/ansible/hosts` file

`-K` will prompt you for your remote Linux user's password