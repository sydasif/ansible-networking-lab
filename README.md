# What is Ansible?

1. Ansible is an open-source configuration management and provisioning tool, similar to Chef, Puppet, and Salt.
2. Ansible lets you control and configure nodes from a single machine.
3. It uses SSH (Paramiko) and API to connect to devices and run the configuration task.

## Why Ansible?

**Simple:** Ansible uses a simple syntax written in YAML format called playbook.

**Agentless:** There are no agent/software or additional firewall ports that you need to install on the client's systems or hosts which you want to automate.

**Powerful and Flexible:** Ansible has powerful features that enable you to model even the most complex IT workflow.

**Efficient:** No extra software on your server, means more resources for your applications.

## Ansible component

**Control node:** A central management of ansible and ansible code run on it and gather information about the managed node.

**Managed nodes:** Managed nodes are network devices that are managing by the control node.

**Tasks:** Tasks are actions that running by ansible.

**Playbooks:** Playbooks Contains a list of actions that can be in the playbook.

## [Ansible for Network Automation](https://docs.ansible.com/ansible/2.9/network/index.html)

As with other automation tools, Ansible started out by managing servers before expanding its ability to manage networking equipment. For the most part, the modules and what Ansible refers to as the 'playbooks' are similar between server modules and network modules, with some differences.

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

Ansible can be installed on most Unix systems, with the only dependency of Python 2.7 or Python 3.5. Currently, the Windows operating system is not officially supported as the control machine. For installation, you can use ansible documentation [**website**](https://docs.ansible.com/ansible/2.9/installation_guide/index.html), its supports various kinds of platforms. We will be installing Ansible on our Ubuntu machine.

```console
sudo apt update
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get install ansible
```

### To test your installation

```console
ansible --version
ansible all -m ping
```

### Inventory

1. Inventory is the target host in our infrastructure which we want to manage by ansible. We put all the hosts information (IP Address/FQDN) into one file called inventory.
2. The default inventory file is /etc/ansible/hosts.
3. Inventory is a file in INI or YAML format.
4. The headings in brackets are group names, which are used in classifying hosts.
5. *network* is a group.
6. There are two default groups, *all* and *ungrouped*, all contain every host, ungrouped contains all hosts that do not have any other group aside from all.

**Note**: My [lab host's](https://github.com/sydasif/ansible_cisco_lab/blob/master/hosts) file has a different configuration from the below hosts example, so the playbook output will be not matched with the example inventory file.

```INI
[network]
R1 ansible_host=192.168.52.1 
S1 ansible_host=192.168.52.2
```

#### Host Variables

You can easily assign a variable to a single host.

```ini
[network]
R1 ansible_host=192.168.52.1 ansible_user=bob ansible_password=bob123 
S1 ansible_host=192.168.52.2 ansible_user=joe ansible_password=joe123 

```

#### Group Variables

If all hosts in a group share a variable value, you can apply that variable to an entire group at once.

```ini
[network]
R1 ansible_host=192.168.52.1 ansible_user=bob ansible_password=bob123 
S1 ansible_host=192.168.52.2 ansible_user=joe ansible_password=joe123

[network:vars]
ansible_network_os=ios 
ansible_connection=network_cli
```

**Note:** host variables have high priority.

### Ansible ad-hoc command

An Ansible ad-hoc command uses the usr/bin/ansible command-line tool to automate a single task on one or more managed nodes.

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
3. YAML does not allow the use of tabs, spaces are used instead of tabs.

#### YAML Basic Data Types

**scalers** is a single value and a variable, it can be string or integer.

```YAML
max_retry: 100
database: users
```

**mapping** is a key, value pair dictionary.

```YAML
animals:
  name: cat
  age: 5
```

**sequence** is a list.

```YAML
employee:
  - age: 28
    name: Jack
  - age: 34
    name: Syd
```

### Ansible Playbook

A playbook is Ansible's configuration, deployment, and orchestration language. Each playbook is composed of one or more *plays* in a list. One *play* is a collection of one or more *tasks*, a *task* is a single action that you want to execute through Ansible.

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

### Ansible networking modules

A module is a reusable script that Ansible runs on either locally or remotely. A module provides a defined interface, accepts arguments and returns information to Ansible in JSON string to stdout. By default, Ansible uses a command module. Ansible network modules example:-

1. ios_command
2. ios_config
3. ios_facts

#### ios_command

##### **ios-command.yml**

```yaml
---
- name: IOS Show Commands
  hosts: R1
  gather_facts: false
  tasks:
    - name: ios show commands
      ios_command:
        commands:
          - show version | i IOS
          - show run | i hostname
      register: output

    - name: show output
      debug:
        var: output
```

The result of the show version and show run output:

```JSON
$ ansible-playbook test.yml

PLAY [IOS Show Commands] *******************************************************************************************************

TASK [ios show commands] *******************************************************************************************************
ok: [R1]

TASK [show output] *************************************************************************************************************
ok: [R1] => {
    "output": {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python3"
        },
        "changed": false,
        "failed": false,
        "stdout": [
            "Cisco IOS Software, 7200 Software (C7200-ADVIPSERVICESK9-M), Version 15.2(4)S5, RELEASE SOFTWARE (fc1)",
            "hostname R1"
        ],
        "stdout_lines": [
            [
                "Cisco IOS Software, 7200 Software (C7200-ADVIPSERVICESK9-M), Version 15.2(4)S5, RELEASE SOFTWARE (fc1)"
            ],
            [
                "hostname R1"
            ]
        ]
    }
}

PLAY RECAP *********************************************************************************************************************
R1                         : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

### Ansible conditionals

Ansible conditionals are similar to conditional statements in programming languages. In Python, we uses conditional statements to execute only a section of the code by using if , then , or while statements. In Ansible, it uses conditional keywords to only run a task when a given condition is met. Ansible networking
command modules. Some of the conditions are as follows:

* Equal to (eq)
* Not equal to (neq)
* Greater than (gt)
* Greater than or equal to (ge)
* Less than (lt)
* Less than or equal to (le)

#### The when clause

The when clause is useful when you need to check the output of a variable or a play execution result and act accordingly

##### **ios-config.yml**

```yml
---
- name: IOS Show Commands
  hosts: all
  gather_facts: false
  tasks:
    - name: backup
      ios_config:
        backup: yes
      when: inventory_hostname == 'R1'
```

The output from above ios-config.yml playbook:

```JSON
$ ansible-playbook test.yml

PLAY [IOS Show Commands] *******************************************************************************************************

TASK [backup] ******************************************************************************************************************
skipping: [CORE-RTR]
skipping: [SW1]
skipping: [CORE01]
skipping: [CORE02]
changed: [R1]

PLAY RECAP *********************************************************************************************************************
CORE-RTR                   : ok=0    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
CORE01                     : ok=0    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
CORE02                     : ok=0    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
R1                         : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
SW1                        : ok=0    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
```

When the playbook is run, a new backup folder will be created with the configuration backed up for the hosts. The use of the when condition in this example, so that, this task will not be applied to other hosts.

### Ansible loops

Ansible provides a number of loops in the playbook.

#### Standard loops

Standard loops in playbooks are often used to easily perform similar tasks multiple times.
