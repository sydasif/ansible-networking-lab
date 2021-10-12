# [Ansible for Network Automation](https://docs.ansible.com/ansible/2.9/network/index.html) 

## What is Ansible

1. Ansible is an open-source configuration management and provisioning tool, similar to Chef, Puppet or Salt.
2. Ansible lets you control and configure nodes from a single machine.
3. It uses SSH and Paramiko to connect to network devices and run the configuration task.

**Why Ansible?**

**Simple:** Ansible uses a simple syntax written in YAML format called playbook.

**Agent less:** There are no agent/software or additional firewall ports that you need to install on the client's systems or hosts which you want to automate.

**Powerful and Flexible:** Ansible has powerful features that enable you to model even the most complex IT workflow.

**Efficient:** No extra software on your server, means more resources for your applications.

## Getting Started with Ansible for Network Automation

Ansible modules support a wide range of vendors, device types, and actions, so you can manage your entire network with a single automation tool.

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

```console
ansible --version
ansible all -m ping
```

### Execution on the Control Node

Unlike most Ansible modules, network modules do not run on the managed nodes. Behind the scenes, however, network modules use a different methodology than the other (Linux/Unix and Windows) modules use. Ansible is written and executed in Python. Because the majority of network devices can not run Python, the Ansible network modules are executed on the Ansible control node. Network modules also use the control node as a destination for backup files. Network modules write backup files on the control node, usually in the backup directory under the playbook root directory.

### Networking Device Inv/Vars 

```terminal
[network]
R1 ansible_host=192.168.52.1 
S1 ansible_host=192.168.52.2

[network:vars]
ansible_user=admin
ansible_password=cisco 
ansible_network_os=ios 
ansible_connection=network_cli
```
