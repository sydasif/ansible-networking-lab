# YAML Introduction

## What is YAML

[YMAL](https://yaml.org) is a human-readable structured data format, it's less complex than XML or JSON and it allows you to provide configuration settings.

### YAML Basic Rules

1. YAML files should end in .yaml or .yml
2. YAML is case sensitive
3. YAML does not allow the use of tabs, spaces are used instead of tab.

### YAML Basic Data Types

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

## Inventory

1. Inventory is the target host in our infrastructure which we want to managed by ansible. We put all the hosts information (IP Address/FQDN) into one file called inventory.
2. The default inventory file is /etc/ansible/hosts.
3. Inventory is a file in INI or YAML format.
4. The headings in brackets are group names, which are used in classifying hosts.
5. *server* group is group of group.
6. mail.example.com is not include in any group, but in a default group.
7. There are two default groups, *all* and *ungrouped*, all contains every host, ungrouped contains all hosts that do not have any other group aside from all.

```INI
mail.example.com

[webservers]
foo.example.com
bar.example.com

[dbservers]
one.example.com
two.example.com
three.example.com

[server:children]
webserver
dbserver

[cisco]
R1 ansible_host=192.168.52.71 
S1 ansible_host=192.168.52.72 

[cisco:vars] 
ansible_user=admin
ansible_password=my_pass
ansible_network_os=ios  
ansible_connection=network_cli
```

Here’s that same basic inventory file in YAML format:

```YAML
all:
  hosts:
    mail.example.com:
  children:
    webservers:
      hosts:
        foo.example.com:
        bar.example.com:
    dbservers:
      hosts:
        one.example.com:
        two.example.com:
        three.example.com:
```

For more information on inventory see [Ansible Documentation](https://docs.ansible.com/ansible/2.9/user_guide/intro_inventory.html#)

### Host Variables

You can easily assign a variable to a single host.

```ini
[targets]

localhost              ansible_connection=local
other1.example.com     ansible_connection=ssh        ansible_user=admin
other2.example.com     ansible_connection=ssh        ansible_user=user
```

### Group Variables

If all hosts in a group share a variable value, you can apply that variable to an entire group at once. **Note:** host variable has high priority.

```ini
[atlanta]
host1 version=17.2
host2 version=17.1

[atlanta:vars]
ntp_server=ntp.atlanta.example.com
proxy=proxy.atlanta.example.com
version=17.0
```

## Module

A module is a reusable, script that Ansible runs on either locally or remotely. A module provides a defined interface, accepts arguments and returns information to Ansible in JSON string to stdout. By default ansible uses command module.
For example:

1. ios command
2. raw
3. shell

## Ansible ad-hoc command

An Ansible ad-hoc command uses the usr/bin/ansible command-line tool to automate a single task on one or more managed nods.

> ansible -i hosts all -m ping
>
> ansible -i host web -m raw -a "pwd"
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

## Ansible Playbook

Playbook are Ansible's configuration, deployment and orchestration language. Each playbook  is composed of one or more *plays* in a list. One *play* is a collection of one or more *task*, a *task* is a single action that you want to execute through Ansible.

```yaml
- name: set up nginx
  hosts: web
  tasks:
    - name: install epel-release
      become: yes
      yum:
        name: epel-release
        state: present
    - name: install nginx on centOS
      become: yes
      yum:
        name: nginx
        state: present
    - name: copy index.html
      become: yes
      copy:
        src: index.html
        dest: /usr/share/nginx/html/index.html
    - name: start nginx
      become: yes
      service:
        name: nginx
        state: started
  