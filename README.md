# What is Ansible

1. Ansible is an open-source configuration management and provisioning tool, similar to Chef, Puppet or Salt.
2. Ansible lets you control and configure nodes from a single machine.
3. It uses SSH and Paramiko to connect to devices and run the configuration task.

## Why Ansible?

**Simple:** Ansible uses a simple syntax written in YAML format called playbook.

**Agent less:** There are no agent/software or additional firewall ports that you need to install on the client's systems or hosts which you want to automate.

**Powerful and Flexible:** Ansible has powerful features that enable you to model even the most complex IT workflow.

**Efficient:** No extra software on your server, means more resources for your applications.

## Ansible component

**Control node:** A central management of ansible and ansible code run on it and gather information about the managed node.

**Managed nodes:** Managed nodes are network devices that are managing by the control node.

**Tasks:** Tasks are actions that running by ansible.

**Playbooks:** Playbooks Contains a list of actions that can be in the playbook by order. The playbook file is written in YAML.

## [Ansible for Network Automation](https://docs.ansible.com/ansible/2.9/network/index.html)

As with other automation tools, Ansible started out by managing servers before expanding its ability to manage networking equipment. For the most part, the modules and what Ansible refers to as the 'playbooks' are similar between server modules and network modules, with differences.

## Getting Started with Ansible for Network Automation

Ansible modules support a wide range of vendors, device types, and actions, so you can manage your entire network with a single automation tool. Ansible handles communication between control node and managed nodes through multiple protocols:

* network_cli by SSH
* netconf by SSH
* httpapi by HTTP/HTTPS

Network platforms supported by ansible are:

* Arista: eos
* Cisco: ios, iosxr, nxos
* Juniper: junos
* VyOS: vyos

### Execution on the Control Node

Unlike most Ansible modules, network modules do not run on the managed nodes. Behind the scenes, however, network modules use a different methodology than the other (Linux/Unix and Windows) modules use. Ansible is written and executed in Python. Because the majority of network devices can not run Python, the Ansible network modules are executed on the Ansible control node. Network modules also use the control node as a destination for backup files. Network modules write backup files on the control node, usually in the backup directory under the playbook root directory.

### Installation

For installation, you can use ansible documentation [**website**](https://docs.ansible.com/ansible/2.9/installation_guide/index.html), its supports various kind of platform.

### To test your installation

```console
ansible --version
ansible all -m ping
```

### Inventory

1. Inventory is the target host in our infrastructure which we want to managed by ansible. We put all the hosts information (IP Address/FQDN) into one file called inventory.
2. The default inventory file is /etc/ansible/hosts.
3. Inventory is a file in INI or YAML format.
4. The headings in brackets are group names, which are used in classifying hosts.
5. *server* group is group of group.
6. mail.example.com is not include in any group, but in a default group.
7. There are two default groups, *all* and *ungrouped*, all contains every host, ungrouped contains all hosts that do not have any other group aside from all.

```INI
[network]
R1 ansible_host=192.168.52.1 
S1 ansible_host=192.168.52.2

[network:vars]
ansible_user=admin
ansible_password=cisco 
ansible_network_os=ios 
ansible_connection=network_cli
```

#### Host Variables

You can easily assign a variable to a single host.

```ini
[targets]

localhost              ansible_connection=local
other1.example.com     ansible_connection=ssh        ansible_user=admin
other2.example.com     ansible_connection=ssh        ansible_user=user
```

#### Group Variables

If all hosts in a group share a variable value, you can apply that variable to an entire group at once. 

**Note:** host variables have high priority.

```ini
[atlanta]
host1 version=17.2
host2 version=17.1

[atlanta:vars]
ntp_server=ntp.atlanta.example.com
proxy=proxy.atlanta.example.com
version=17.0
```

### Ansible ad-hoc command

An Ansible ad-hoc command uses the usr/bin/ansible command-line tool to automate a single task on one or more managed nods.

> ansible -i hosts all -m ping
>
> ansible all -m ios_command -a "command='show ip interface brief'"
>
> ansible all -i 192.168.52.71, -c network_cli -u admin -k -m ios_facts -e ansible_network_os=ios

1. -i: list of managed nodes which they are separated by comma
2. -c: Connection type
3. -u: Username
4. -k: Ask for a password
5. -m: Module name
6. -e: Network platform

### YAML Introduction

[YMAL](https://yaml.org) is a human-readable structured data format, it's less complex than XML or JSON and it allows you to provide configuration settings.

#### YAML Basic Rules

1. YAML files should end in .yaml or .yml
2. YAML is case sensitive
3. YAML does not allow the use of tabs, spaces are used instead of tab.

#### YAML Basic Data Types

**scalers** is a single value and a variable, it can be string or integer.

```YAML
max_retry: 100
database: users
```

**mapping** is key, value pair dictionary.

```YAML
animals:
  name: cat
  age: 5
```

**sequence** is list.

```YAML
employee:
  - age: 28
    name: Jack
  - age: 34
    name: Syd
```

### Ansible Playbook

Playbook are Ansible's configuration, deployment and orchestration language. Each playbook  is composed of one or more *plays* in a list. One *play* is a collection of one or more *task*, a *task* is a single action that you want to execute through Ansible.

```yaml
---
- name: "Playbook-1: Get ios facts" 
  connection: network_cli
  gather_facts: false
  hosts: all
  tasks:
 
    - name: "P1T1: Get config from ios_facts" 
      ios_facts:
        gather_subset: all
 
    - name: "P1T2: Display debug message"
      debug:
        msg: "The  {{ ansible_net_hostname }} has {{ ansible_net_iostype }}  platform"
```

### Module

A module is a reusable, script that Ansible runs on either locally or remotely. A module provides a defined interface, accepts arguments and returns information to Ansible in JSON string to stdout. By default ansible uses command module. For example:-

1. ios command
2. ios config
3. ios facts
