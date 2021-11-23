## Ansible CHEAT SHEET
ping 
```
ansible all -m ping
```
show hostname 
```
ansible all -m command -a "hostname"
```
show information
```
ansible vm1 -m setup
```
