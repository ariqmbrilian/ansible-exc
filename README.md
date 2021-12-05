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
create user
```
ansible all -m user -a "name=devops" -b
```
check syntax
```
ansible-playbook --syntax-check user.yml
```