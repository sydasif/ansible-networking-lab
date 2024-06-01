# Introduction to Ansible

Ansible is a powerful open-source configuration management and provisioning tool that stands alongside industry favorites like Chef, Puppet, and Salt. Designed to streamline the process of managing and configuring nodes, Ansible enables administrators to control multiple devices from a single, centralized machine. Leveraging SSH (Paramiko) and various APIs for connectivity, Ansible simplifies the execution of configuration tasks, making it an essential tool for efficient and effective infrastructure management.

## Why Ansible?

In today's fast-paced IT environments, efficient and reliable configuration management tools are essential for maintaining smooth operations. Ansible stands out as a preferred choice for many organizations due to its simplicity, power, and flexibility. Here are some key reasons why Ansible is a valuable tool for managing your infrastructure:

1. **Simple:** Ansible uses a straightforward syntax written in YAML format, known as playbooks, making it easy to learn and use.
2. **Agentless:** There is no need to install any agents or additional software on the client systems or hosts you wish to automate, simplifying the setup and reducing security concerns.
3. **Powerful and Flexible:** Ansible boasts powerful features that allow you to model and manage even the most complex IT workflows with ease
4. **Efficient:** By not requiring extra software on your servers, Ansible ensures that more resources are available for your applications, enhancing overall system performance.
5. **Idempotent:** Ansible ensures that changes are only made when necessary to achieve the desired state, preventing unnecessary modifications and maintaining consistency across your infrastructure.

## Ansible Components

Understanding the key components of Ansible is essential for effectively utilizing its capabilities. Here’s a breakdown of the fundamental elements that make up Ansible:

1. **Control Node:** The control node is the central management point where Ansible and its code are executed. It is responsible for running tasks and gathering information about the managed nodes.
2. **Managed Nodes:** These are the network devices and systems managed by the control node. Ansible connects to these nodes to perform configuration and management tasks.
3. **Tasks:** Tasks are the individual actions executed by Ansible. Each task performs a specific function, such as installing a package, restarting a service, or managing files.
4. **Playbooks:** Playbooks are YAML files that contain a list of tasks. They define the desired state of the managed nodes and outline the sequence of actions to achieve that state, allowing for complex workflows and automation scenarios.

By understanding these components, you can effectively leverage Ansible to automate and streamline your IT operations, ensuring a more efficient and consistent infrastructure management process.

## [Ansible for Network Automation](https://docs.ansible.com/ansible/2.9/network/index.html)

Ansible initially focused on managing servers but has since extended its capabilities to include network devices. The core concepts, including modules and playbooks, remain consistent between server and network management, though there are some differences tailored to network automation. This expansion allows for a unified approach to managing both servers and network equipment, streamlining IT operations and improving efficiency.

### Getting Started with Ansible for Network Automation

Ansible modules support a wide range of vendors, device types, and actions, so you can manage your entire network with a single automation tool. This versatility makes Ansible a powerful solution for network automation, allowing you to automate routine tasks and ensure consistency across your network infrastructure. Ansible handles communication between the control node and managed nodes through multiple protocols:

* **network_cli by SSH:** A widely-used protocol for managing network devices, ensuring secure and reliable communication.
* **netconf by SSH:** A protocol designed specifically for network management, offering enhanced capabilities for configuration and monitoring.
* **httpapi by HTTP/HTTPS:** A flexible and efficient protocol for interacting with network devices that support RESTful APIs.

Network platforms supported by Ansible are:

* **Arista: eos:** Providing robust automation capabilities for Arista's Extensible Operating System.
* **Cisco: ios, iosxr, nxos:** Enabling comprehensive management of Cisco's diverse range of network operating systems, from traditional IOS to the advanced features of IOS-XR and NX-OS.
* **Juniper: junos:** Facilitating powerful automation for Juniper's Junos OS, known for its flexibility and performance.
* **VyOS: vyos:** Supporting automation for VyOS, an open-source network operating system, ideal for various networking tasks.

With Ansible's extensive support for different network platforms and protocols, you can streamline your network automation processes and achieve greater operational efficiency.

### Execution of Network Modules in Ansible

Unlike most Ansible modules, network modules do not run on the managed nodes due to the inability of most network devices to run Python. Instead, these modules are executed on the Ansible control node. This different methodology ensures that Ansible can still manage network devices effectively. Additionally, network modules use the control node as a destination for backup files, typically storing them in the backup directory under the playbook root directory. This approach allows Ansible to provide consistent network management and backup capabilities without the need for Python on the network devices themselves.

## Installation on Ansible

Ansible can be installed on most Unix systems, with the only dependency being Python 2.7 or Python 3.5. Currently, the Windows operating system is not officially supported as a control machine. For detailed installation instructions, you can refer to the Ansible documentation [**website**](https://docs.ansible.com/ansible/2.9/installation_guide/index.html), which provides guidance for various platforms. In this guide, we will install Ansible on an Ubuntu machine using the following commands:

```console
sudo apt update
sudo apt install software-properties-common
sudo apt-add-repository ppa:ansible/ansible
sudo apt install ansible
```

These steps ensure that your Ubuntu system is updated, necessary software properties are installed, the Ansible repository is added, and Ansible itself is installed, allowing you to start using Ansible for automation tasks.

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

```yml
---
- name: "Playbook: Configure Vlans" 
  gather_facts: false
  hosts: switch
  tasks:

    - name: configure vlan
      ios_config:
        lines:
          - vlan "{{ item }}"

      register: commands
        
      with_items:
        - '10'
        - '20'

    -  debug: 
        var: commands
```

#### Looping over dictionaries

Looping over a simple list is good. However, we often more than one attribute associated with it. If you check above the vlan example, each vlan would have several unique attributes, such as description, vlan gateway IP address, and possibly others. we can use a dictionary to represent these multiple attributes to it.
