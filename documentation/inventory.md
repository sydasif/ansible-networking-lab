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
