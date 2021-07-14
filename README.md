# Ansible

## What is Ansible

1. Ansible is an open-source configuration management and provisioning tool, similar to Chef, Puppet or Salt.
2. Ansible lets you control and configure nodes from a single machine.
3. It uses SSH to connect to servers and run the configuration task.

**Why Ansible?**

**Simple:** Ansible uses a simple syntax written in YAML format called playbook.

**Agent less:** There are no agent/software or additional firewall ports that you need to install on the client's systems or hosts which you want to automate.

**Powerful and Flexible:** Ansible has powerful features that enable you to model even the most complex IT workflow.

**Efficient:** No extra software on your server, means more resources for your applications.

## Ansible component

**Control node:** A central management of ansible and ansible code run on it and gather information about the managed node.

**Managed nodes:** Managed nodes are network devices that are managing by the control node.

**Tasks:** Tasks are actions that running by ansible.

**Playbooks:** Playbooks Contains a list of actions that can be in the playbook by order. The playbook file is written in YAML.

Ansible handles communication between control node and managed nodes through multiple protocols:

* network_cli by SSH
* netconf by SSH
* httpapi by HTTP/HTTPS

Network platforms supported by ansible are:

* Arista: eos
* Cisco: ios, iosxr, nxos
* Juniper: junos
* VyOS: vyos

### Installation

For installation, you can use ansible documentation [**website**](https://docs.ansible.com/ansible/2.9/installation_guide/index.html) and it supports various kind of platform.

#### To test your installation

> *ansible --version*

#### Adhoc Command

> *ansible all -i 192.168.52.71, -c network_cli -u admin -k -m ios_facts -e ansible_network_os=ios*

1. -i: list of managed nodes which they are separated by comma
2. -c: Connection type
3. -u: Username
4. -k: Ask for a password
5. -m: Module name
6. -e: Network platform
