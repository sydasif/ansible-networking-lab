# Introduction to Ansible

Ansible is a powerful open-source configuration management and provisioning tool that stands alongside industry favorites like Chef, Puppet, and Salt. Designed to streamline the process of managing and configuring nodes, Ansible enables administrators to control multiple devices from a single, centralized machine. Leveraging SSH (Paramiko) and various APIs for connectivity, Ansible simplifies the execution of configuration tasks, making it an essential tool for efficient and effective infrastructure management.

## Why Use Ansible?

In today's fast-paced IT environments, efficient and reliable configuration management tools are essential for maintaining smooth operations. Ansible stands out as a preferred choice for many organizations due to its simplicity, power, and flexibility. Here are some key reasons why Ansible is a valuable tool for managing your infrastructure:

1. **Simple:** Ansible uses a straightforward syntax written in YAML format, known as playbooks, making it easy to learn and use.
2. **Agentless:** There is no need to install any agents or additional software on the client systems or hosts you wish to automate, simplifying the setup and reducing security concerns.
3. **Powerful and Flexible:** Ansible boasts powerful features that allow you to model and manage even the most complex IT workflows with ease.
4. **Efficient:** By not requiring extra software on your servers, Ansible ensures that more resources are available for your applications, enhancing overall system performance.
5. **Idempotent:** Ansible ensures that changes are only made when necessary to achieve the desired state, preventing unnecessary modifications and maintaining consistency across your infrastructure.

## Ansible Basic Components

Understanding the key components of Ansible is essential for effectively utilizing its capabilities. Here’s a breakdown of the fundamental elements that make up Ansible:

1. **Control Node:** The central management point where Ansible and its code are executed. It is responsible for running tasks and gathering information about the managed nodes.
2. **Managed Nodes:** These are the network devices and systems managed by the control node. Ansible connects to these nodes to perform configuration and management tasks.
3. **Tasks:** The individual actions executed by Ansible. Each task performs a specific function, such as installing a package, restarting a service, or managing files.
4. **Playbooks:** YAML files that contain a list of tasks. They define the desired state of the managed nodes and outline the sequence of actions to achieve that state, allowing for complex workflows and automation scenarios.
5. [**Inventory**](https://docs.ansible.com/ansible/2.9/user_guide/intro_inventory.html#how-to-build-your-inventory): A list of managed nodes. An inventory file is also sometimes called a “hostfile”. Your inventory can specify information like the IP address for each managed node. An inventory can also organize managed nodes, creating and nesting groups for easier scaling.
6. **Modules:** Units of code Ansible executes. Each module has a particular use, from administering users on a specific type of database to managing VLAN interfaces on a specific type of network device. You can invoke a single module with a task or invoke several different modules in a playbook. For an idea of how many modules Ansible includes, take a look at the [list of all modules](https://docs.ansible.com/ansible/2.9/modules/modules_by_category.html#module-index).

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

### [Execution of Network Modules in Ansible](https://docs.ansible.com/ansible/2.9/network/getting_started/network_differences.html#how-network-automation-is-different)

Unlike most Ansible modules, network modules do not run on the managed nodes due to the inability of most network devices to run Python. Instead, these modules are executed on the Ansible control node. This different methodology ensures that Ansible can still manage network devices effectively. Additionally, network modules use the control node as a destination for backup files, typically storing them in the backup directory under the playbook root directory. This approach allows Ansible to provide consistent network management and backup capabilities without the need for Python on the network devices themselves.

## Installation of Ansible

Ansible can be installed on most Unix systems, with the only dependency being Python 2.7 or Python 3.5. Currently, the Windows operating system is not officially supported as a control machine. For detailed installation instructions, you can refer to the Ansible documentation [**website**](https://docs.ansible.com/ansible/2.9/installation_guide/index.html), which provides guidance for various platforms. In this guide, we will install Ansible on an Ubuntu machine using the following commands:

```console
sudo apt update
sudo apt install software-properties-common
sudo apt-add-repository ppa:ansible/ansible
sudo apt install ansible
```

These steps ensure that your Ubuntu system is updated, necessary software properties are installed, the Ansible repository is added, and Ansible itself is installed, allowing you to start using Ansible for automation tasks.

To ensure that your Ansible installation is successful and operational, you can run the following commands in your terminal:

```bash
zolo@u22s:~$ ansible --version
ansible [core 2.16.7]
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/zolo/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  ansible collection location = /home/zolo/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.10.12 (main, Nov 20 2023, 15:14:05) [GCC 11.4.0] (/usr/bin/python3)
  jinja version = 3.0.3
  libyaml = True
```

Executing this command will display information about the installed version of Ansible, confirming that it is correctly installed and ready for use.

Additionally, you can test Ansible's connectivity and functionality by running a simple ping command against the localhost:

```shell
zolo@u22s:~$ ansible localhost -m ping
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

Running this command should result in a successful ping response from the localhost, indicating that Ansible can communicate with the target host and execute commands successfully.

### Privilege Escalation - Networking Device

As of Ansible 2.6, you can use the top-level Ansible parameter `become: yes` with `become_method: enable` to run a task, play, or playbook with escalated privileges on any network platform that supports privilege escalation.

### Establish a Manual Connection to a Managed Node

To confirm your credentials, connect to a network device manually and retrieve its configuration. Replace the sample user and device name with your real credentials. For example, for a router:

```bash
zolo@u22s:~$ ssh admin@172.16.10.11
(admin@172.16.10.11) Password: 

r1#sh ip int bri
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            172.16.10.11    YES NVRAM  up                    up      
FastEthernet0/1            unassigned      YES NVRAM  administratively down down    
FastEthernet1/0            unassigned      YES unset  administratively down down    
r1#exit
Connection to 172.16.10.11 closed.
```

### Run Your First Command

Instead of manually connecting and running a command on the network device, you can retrieve its configuration with a single, stripped-down Ansible command

:

```bash
zolo@u22s:~$ ansible all -i 172.16.10.12, -c network_cli -u admin -k -m ios_facts -e ansible_network_os=ios
SSH password: 
172.16.10.12 | SUCCESS => {
    "ansible_facts": {
        "ansible_net_api": "cliconf",
        "ansible_net_gather_network_resources": [],
        "ansible_net_gather_subset": [
            "default"
        ],
        "ansible_net_hostname": "core-sw",
        "ansible_net_image": "tftp://255.255.255.255/unknown",
        "ansible_net_iostype": "IOS",
        "ansible_net_model": "3725",
        "ansible_net_operatingmode": "autonomous",
        "ansible_net_python_version": "3.10.12",
        "ansible_net_serialnum": "FTX0945W0MY",
        "ansible_net_system": "ios",
        "ansible_net_version": "12.4(15)T14",
        "ansible_network_resources": {}
    },
    "changed": false
}
```

In this example, Ansible targets all hosts defined in the inventory, specifying a custom inventory file with `-i`, connecting using `network_cli` as the connection type with `-c`, authenticating with the username `admin` using `-u`, prompting for a password with `-k`, executing the `ios_facts` module to gather facts about IOS devices, and specifying the network platform as `ios` using `-e`.

### Key Parameters

* `-i`: Specifies the list of managed nodes, separated by commas.
* `-c`: Defines the connection type.
* `-u`: Specifies the username for authentication.
* `-k`: Prompts for a password.
* `-m`: Specifies the module name.
* `-e`: Defines additional variables, such as the network platform.

These ad-hoc commands provide quick and efficient ways to perform specific tasks across your infrastructure using Ansible's versatile command-line interface.

Ansible is a robust and flexible tool for automating IT infrastructure management. Expanding into network automation, Ansible supports a wide range of network devices and protocols, ensuring comprehensive management capabilities. The installation process is straightforward, and Ansible's ability to handle privilege escalation and network-specific tasks further enhances its utility.

## Ansible Configuration and Setting

Ansible supports several sources for configuring its behavior, including an ini file named `ansible.cfg`, environment variables, command-line options, playbook keywords, and variables. This flexibility allows users to customize and optimize Ansible for their specific environments and needs.

### The Configuration File

Changes to Ansible's behavior can be made in a configuration file. Ansible will search for this configuration file in the following order:

1. `ANSIBLE_CONFIG` (environment variable if set)
2. `ansible.cfg` (in the current directory)
3. `~/.ansible.cfg` (in the home directory)
4. `/etc/ansible/ansible.cfg`

Ansible will process the list and use the first configuration file it finds, ignoring all others. This hierarchical search allows for different configurations based on the context in which Ansible is being used.

### The ansible-config Utility

The `ansible-config` utility is a powerful tool for viewing, listing, or dumping the various settings available for Ansible. This utility helps users manage and understand their Ansible configurations effectively.

#### Viewing the Current Configuration

You can use the `ansible-config view` command to print the content of your current `ansible.cfg` file. For example:

```bash
zolo@u22s:~/ansible-networking-lab$ ansible-config view
[defaults]
inventory=./inventory.cfg
gathering=explicit
host_key_checking=false
deprecation_warnings=false
interpreter_python=auto
retry_files_enabled=false
```

This output reflects the exact settings in the `ansible.cfg` file for the current directory.

#### Dumping the Current Configuration

To dump all of Ansible's current configuration settings, use the following command:

```bash
ansible-config dump
```

This command provides a comprehensive view of all configuration settings, making it easier to troubleshoot and understand Ansible's behavior in your environment.

#### Listing All Parameters

To see a list of all different parameters, their default values, and the possible values you can set, use the `ansible-config list` command:

```bash
ansible-config list
```

This command is particularly useful for discovering configurable options and understanding the defaults that Ansible uses.

#### How Ansible Works - Precedence Rules

To give you maximum flexibility in managing your environments, Ansible offers many ways to control how Ansible behaves: how it connects to managed nodes and how it works once it has connected. If you use Ansible to manage a large number of servers, network devices, and cloud resources, you may define Ansible behavior in several different places and pass that information to Ansible in several different ways.

Ansible offers four sources for controlling its behavior. In order of precedence from lowest (most easily overridden) to highest (overrides all others), the categories are:

1. **Configuration settings:** The settings in your `ansible.cfg` file.
2. **Command-line options:** Flags and parameters passed directly in the command line when running Ansible commands.
3. **Playbook keywords:** Directives specified within your playbooks that control how tasks are executed.
4. **Variables:** Values defined in your inventory files, playbooks, or directly in tasks, often providing the most granular level of control.

Each category overrides any information from all lower-precedence categories. For example, a playbook keyword will override any configuration setting. Within each precedence category, specific rules apply. However, generally speaking, 'last defined' wins and overrides any previous definitions.

#### Example Configuration

Below is an example configuration file (`ansible.cfg`) that might be used in a typical lab setup:

```ini
[defaults]
inventory=./inventory.cfg
gathering=explicit
host_key_checking=false
deprecation_warnings=false
interpreter_python=auto
retry_files_enabled=false
```

* **inventory:** Specifies the path to the inventory file.
* **gathering:** Controls the gathering of facts; `explicit` means facts are only gathered when explicitly requested.
* **host_key_checking:** Disables SSH key checking to avoid issues with new hosts.
* **deprecation_warnings:** Disables deprecation warnings to reduce clutter in output.
* **interpreter_python:** Automatically determines the Python interpreter to use.
* **retry_files_enabled:** Disables the creation of retry files for failed playbook runs.

This configuration helps streamline Ansible's operation in a controlled lab environment, ensuring that tasks run smoothly without unnecessary interruptions or checks.

By understanding and utilizing the configuration options available in Ansible, you can tailor the tool to better fit your needs, improving efficiency and control over your automation tasks. The `ansible-config` utility further enhances this capability by providing clear insights into the current settings and available options.

### [Ansible Inventory Setting](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html#how-to-build-your-inventory)

Inventory serves as the foundation for managing hosts within our infrastructure using Ansible. It consolidates information about target hosts, including their IP addresses or Fully Qualified Domain Names (FQDNs), into a single file called the inventory.

#### Formats for Inventory Files

You can create your inventory file in one of many formats, the most common being `INI` and [`YAML`](/yaml-into.md). A basic INI `/etc/ansible/hosts` might look like this:

```ini
[router]
r1 ansible_host=172.16.10.11

[core]
core-sw ansible_host=172.16.10.12

[switch]
access1 ansible_host=172.16.10.13
access2 ansible_host=172.16.10.14
```

The headings in brackets are group names, which are used in classifying hosts and deciding what hosts you are controlling at what times and for what purpose.

Here’s that same basic inventory file in YAML format:

```yaml
all:
  children:
    router:
      hosts:
        r1:
          ansible_host: 172.16.10.11
    core:
      hosts:
        core-sw:
          ansible_host: 172.16.10.12
    switch:
      hosts:
        access1:
          ansible_host: 172.16.10.13
        access2:
          ansible_host: 172.16.10.14
```

In this YAML inventory, `all` is the top-level group that contains all hosts and groups. The `hosts` key under `all` lists each host along with its properties, such as `ansible_host`. The `children` key under `all` contains subgroups like `router`, `core`, and `switch`, each with their respective hosts. This structure ensures the inventory is organized and easily manageable.

> By default, we can also reference two groups without defining them. The `all` group targets all our hosts in the inventory, and `ungrouped` contains any host that isn’t part of any user-defined group.

#### Defining Host Aliases

Another useful functionality is the option to define aliases for hosts in the inventory. For example, we can run Ansible against the host alias `core-sw` if we define it in the inventory as:

```ini
core-sw ansible_host=172.16.10.12
```

#### Inventory and Variables

An important aspect of Ansible’s setup is variable assignment and management. Ansible offers many different ways of setting variables, and defining them in the inventory is one of them.

##### Host Variables

For example, let’s define variables for each host in our inventory:

```ini
[router]
r1 ansible_host=172.16.10.11 ansible_user=bob

[core]
core-sw ansible_host=172.16.10.12 ansible_user=joe
```

Ansible-specific connection variables such as `ansible_user` or `ansible_host` are examples of host variables defined in the inventory.

##### Group Variables

Similarly, variables can also be set at the group level in the inventory, offering a convenient way to apply variables to hosts with common characteristics:

```ini
[router]
r1 ansible_host=172.16.10.11 ansible_user=bob ansible_password=bob123

[core]
core-sw ansible_host=172.16.10.12 ansible_user=joe ansible_password=joe123

[campus:vars]
ansible_network_os=ios
ansible_connection=network_cli
```

Although setting variables in the inventory is possible, it’s usually not the preferred way. Instead, store separate host and group variable files to enable better organization and clarity for your Ansible projects. Note that host and group variable files must use the YAML syntax.

In the same directory where we keep our inventory file, we can create two folders named `group_vars` and `host_vars` containing our variable files. For example, we could have a file `group_vars/campus.yaml` that contains all the variables for the campus network:

```yaml
---
# group_vars/campus.yaml
ansible_user: admin
ansible_password: cisco
ansible_network_os: ios
ansible_connection: network_cli
```

Let’s view the generated inventory with the command:

```bash
zolo@u22s:~/ansible-networking-lab$ ansible-inventory -i inventory.yaml --graph
@all:
  |--@ungrouped:
  |--@campus:
  |  |--@router:
  |  |  |--r1
  |  |--@core:
  |  |  |--core-sw
  |  |--@switch:
  |  |  |--access1
  |  |  |--access2
zolo@u22s:~/ansible-networking-lab$ ansible-inventory -i inventory.yaml --host r1
{
    "ansible_connection": "network_cli",
    "ansible_host": "172.16.10.11",
    "ansible_network_os": "ios",
    "ansible_password": "cisco",
    "ansible_user": "admin",
    "hostname": "r1"
}
```

The command fetches information from our hosts/inventory file and creates several groups according to our configuration/setup.

#### Key Points About Inventory

1. **Host Information:** Inventory files contain crucial information about target hosts, such as their IP addresses and connection variables.
2. **Default Location:** The default location for the inventory file is `/etc/ansible/hosts`, though you can specify a different location as needed.
3. **File Format:** Inventory can be written in either `INI` or `YAML` format, providing flexibility in structuring and organizing host information.
4. **Grouping Hosts:** Group names, enclosed in brackets, serve to classify hosts based on common attributes or roles they fulfill within the infrastructure.
5. **Special Groups:** The `campus` group is an example of a special group used for categorizing network devices, enabling targeted management of specific device types.
6. **Default Groups:** Ansible includes two default groups, namely `all` and `ungrouped`. The `all` group encompasses every host defined in the inventory, while the `ungrouped` group comprises hosts that do not belong to any other group aside from `all`.

By leveraging the inventory, Ansible users can effectively organize, manage, and automate tasks across their infrastructure, streamlining operations and enhancing efficiency.

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
