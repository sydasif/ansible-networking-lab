# Ansible

## Ansible – Network automation- 1

**Here are some basic information about ansible:**

### Ansible component:

**Control node :** A central management of ansible and ansible code run on it and gather information about managed node. You can have multiple control node.

**Managed nodes :** Managed nodes are network devices which are managing by control node.

**Tasks:** Tasks are actions which running by ansible.

**Playbooks:** Playbooks Contains list of actions which they can be in playbook by order. Playbook file is written in YAML.

Ansible handle communication between control node and managed nodes through multiple protocol:

* network_cli by SSH
* netconf by SSH
* httpapi by HTTP/HTTPS

Network platforms which supported by ansible are :

* Arista : eos
* Cisco : ios , iosxr , nxos
* Juniper : junos
* VyOS : vyos

### Installation: 

For installation you can use ansible documentation [**website**](https://docs.ansible.com/ansible/2.9/installation_guide/index.html) and it support various kind of platform.

#### To test your installation:

> ansible --version

#### Adhoc Command :

> ansible all -i 192.168.52.71, -c network_cli -u admin -k -m ios_facts -e ansible_network_os=ios

1. -i: list of managed nodes which they are separated by comma
2. -c: Connection type
3. -u: Username
4. -k: Ask for password
5. -m: Module name
6. -e: Network platform

We progress more and add playbook and explain more, the below text file is simple example of playbook:

playbook-1.yml :

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

The new command to execute :

ansible-playbook -i 192.168.52.72, -u admin -k -e ansible_network_os=ios playbook-1.yml

> As you can see there are some changes in ansible command:

1. -c : connection moved to playbook >> connection: network_cli
2. all : moved to playbook >> hosts: all
3. -m : moved to playbook

The second part of playbook is task 1:

Each task has name, module in this task ansible get device fact but it keep the result in memory and use it in same or other tasks the purpose of this task was just get the fact and dont show it in screen but instead use it in future.

The third part is task 2:

this task uses info from previous task debug lets use the variable in message.

We progress more and add Inventory list to our command and remove more details from command and transfer them to file.

creating inventory file :

[cisco_routers]
R1 ansible_host=192.168.52.71 ansible_network_os=ios ansible_user=admin ansible_connection=network_cli
S1 ansible_host=192.168.52.72 ansible_network_os=ios ansible_user=admin ansible_connection=network_cli

change playbook-1.yml file according to this change as playbool-2.yml :

playbook-2.yml

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

Now new command will be :

> ansible-playbook -i inventory  -k playbook-2.yml

Also you have option to send your SSH password in encrypted way instead of insert every time in unsecure condition. 
For this purpose you need to configure your ansible vault.