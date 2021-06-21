# Ansible

## Ansible – Network automation

Here are some basic information about ansible:

### Ansible component:

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

### Installation: 

For installation, you can use ansible documentation [**website**](https://docs.ansible.com/ansible/2.9/installation_guide/index.html) and it supports various kind of platform.

#### To test your installation:

> *ansible --version*

#### Adhoc Command:

> *ansible all -i 192.168.52.71, -c network_cli -u admin -k -m ios_facts -e ansible_network_os=ios*

1. -i: list of managed nodes which they are separated by comma
2. -c: Connection type
3. -u: Username
4. -k: Ask for a password
5. -m: Module name
6. -e: Network platform

We progress more and add playbook, the below text file is a simple example of the playbook:

*playbook-1.yml:*

```YAML
---
- name: playbook-1
  connection: network_cli
  gather_facts: false
  hosts: all
  tasks:
 
    - name: Get config 
      ios_facts:
        gather_subset: all
 
    - name: Display the message
      debug:
        msg: "The  {{ ansible_net_hostname }} has has  {{ ansible_net_iostype }}  platform" 
```        

The new command to execute:

> *ansible-playbook -i 192.168.52.72, -u admin -k -e ansible_network_os=ios playbook-1.yml*

As you can see there are some changes in the ansible command:

1.  -c : connection moved to playbook >> connection: network_cli
2.  all: moved to playbook >> hosts: all
3.  -m: moved to the playbook

The second part of the playbook is task 1:

Each task has a name, module in this task ansible get device fact but it keeps the result in memory and uses it in same or other tasks, the purpose of this task was just to get the fact and don't show it in screen but instead use it in future.

The third part is task 2:

This task uses information from the previous task debug let's use the variable in the message.

We progress more and add Inventory list to our command and remove more details from the command and transfer them to file.

**Creating inventory file:**

[cisco_routers]
R1 ansible_host=192.168.52.71 ansible_network_os=ios ansible_user=admin ansible_connection=network_cli
S1 ansible_host=192.168.52.72 ansible_network_os=ios ansible_user=admin ansible_connection=network_cli

change playbook-1.yml file according to this change as playbook-2.yml :

playbook-2.yml

```YAML
---
- name: playbook-2
  hosts: cisco_routers
  gather_facts: false
  tasks:
 
    - name: Get config 
      ios_facts:
        gather_subset: all
 
    - name: Display the message
      debug:
        msg: "The  {{ ansible_net_hostname }} has  {{ ansible_net_iostype }}  platform"
```

Now new command will be:

> ansible-playbook -i inventory  -k playbook-2.yml

Also, you have the option to send your SSH password in an encrypted way instead of insert every time in an un-secure condition. 
For this purpose, you need to configure your ansible vault.
