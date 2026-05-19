# Ansible Playbooks for creating Run-Anywhere Qumulo Clusters

The Ansible playbooks listed here will allow for simpler installation of Qumulo Core on RHEL, Rocky and Ubuntu hosts.

This tooling is meant for on-prem or virtual cluster installs and is not a replacement for Terraform for provisioning Cloud
Native Qumulo clusters.

**Important:**

Please review our Installing Qumulo Core Package documentation first.

https://docs.qumulo.com/administrator-guide/getting-started/installing-product-package.html


This process has been tested with:

- RHEL 9.4
- Rocky 9.3
- Ubuntu 22.04 and 24.04


## Playbooks

### Install - Qumulo-Managed Networking

These playbooks install Qumulo Core in Qumulo-managed network mode, where the Qumulo process in the container manages its own network interfaces (bond0). The playbooks prepare the host OS networking stack to avoid conflicts with the container's bond0 interface.

**Note:** Once the QFSD process starts in the container and takes over networking, the host will lose its original DHCP-assigned IP address and become unreachable from Ansible. The playbook may report errors for any tasks that run after this point. This is expected behavior -- the node will acquire a new IP via DHCP on the Qumulo-managed interface.

| Playbook | Description |
|---|---|
| `ubnt-qnode-q-managed-network.yml` | Ubuntu/Debian - Disables Netplan, writes a static resolv.conf, stops NetworkManager, and installs qumulo-core with `QUMULO_NETWORK_MANAGED_BY_QUMULO=true` |
| `rhel-qnode-q-managed-network.yml` | RHEL/Rocky - Disables NetworkManager, backs up NM connection profiles, writes a static resolv.conf, stops chronyd, and installs qumulo-core with `QUMULO_NETWORK_MANAGED_BY_QUMULO=true` |

### Install - Host-Managed Networking

These playbooks install Qumulo Core in host-managed network mode, where the host OS retains control of network interfaces via NetworkManager or Netplan.

| Playbook | Description |
|---|---|
| `ubnt-host-managed-qnode-setup.yml` | Ubuntu/Debian - Installs qumulo-core with `QUMULO_NETWORK_MANAGED_BY_HOST=true`, keeping Netplan/NetworkManager active |
| `rhel-host-managed-qnode-setup.yml` | RHEL/Rocky - Verifies NetworkManager 1.42+ (required for IPVLAN support), installs qumulo-core with `QUMULO_NETWORK_MANAGED_BY_HOST=true` |

### Removal

**WARNING: Running a remove playbook against a node will permanently destroy that node's membership in the Qumulo cluster. If run against all nodes in a cluster, all data stored on that cluster will be lost. These playbooks are irreversible and should only be used when you intend to fully decommission a node or cluster.**

| Playbook | Description |
|---|---|
| `ubnt-qnode-remove.yml` | Ubuntu/Debian - Stops qumulo-qcore and removes the qumulo-core package |
| `rhel-qnode-remove.yml` | RHEL/Rocky - Stops qumulo-qcore and removes the qumulo-core package |

### Utility

| Playbook | Description |
|---|---|
| `gravycopy.yml` | Internal utility to download qumulo-core packages directly to nodes (may be modified for other environments) |


## Directions

- Ensure your local machine has `ansible` installed:

    `pip install ansible`

Windows users: Ansible requires Windows Subsystem for Linux (WSL)

- Create a new directory for this Ansible project
- Clone this repo into this new directory and cd into the newly cloned directory
- Log into https://nexus.qumulo.com and download the correct `qumulo-core` install package from `Downloads > Cloud Native Qumulo Downloads`
    - qumulo-core.rpm for RHEL and Rocky
    - qumulo-core.deb for Ubuntu and Debian
- **Optional** - Copy `qumulo-core.rpm/.deb` directly to each future node's `/tmp` directory.
    - This might be a desirable option if the connection between the Ansible controller and the nodes is slow.
    - You may be able to modify the playbook `gravycopy.yml` to help you with this.
- Ensure this file is in the same directory as this project
- Create your Ansible `hosts` file (look at the sample `hosts` file provided)
    - IPs or FQDNs can be used
    - `hosts` files can contain many sections, in this case we will be using `[nodes]`
- Edit the `remote_user` in the playbook you intend to run to match the remote Linux user for your hosts
- Copy your SSH public key to all your remote hosts

Example:

    `ssh-copy-id -i ~/.ssh/id_rsa.pub yourlinuxuser@1.2.3.4`

- Run the playbook:

    `ansible-playbook <playbook-name>.yml -i hosts -K`

`-i` points to your hosts file, if omitting this then Ansible will use the default `/etc/ansible/hosts` file

`-K` will prompt you for your remote Linux user's password
