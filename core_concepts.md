# Ansible Core Concepts

## YAML Introduction

### What is YAML

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

## Inventory

1. Inventory is the target host in our infrastructure which we want to managed by ansible. We put all the hosts information (IP Address/FQDN) into one file called inventory.
2. The default inventory file is /etc/ansible/hosts.
3. Inventory is a file in INI or YAML format.
4. The headings in brackets are group names, which are used in classifying hosts.
5. *server* group is group of group.
6. mail.example.com is not include in any group, but in a default group *all*.

```INI
mail.example.com

[webservers]
foo.example.com
bar.example.com

[dbservers]
one.example.com
two.example.com
three.example.com
[cisco]
cisco.test.com

[server:children]
webserver
dbserver
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
