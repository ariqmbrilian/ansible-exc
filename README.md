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
check uptime
```
ansible webservers -m command -a "uptime" -o
```
install apache adhoc command
```
ansible webservers -m yum -a "name=httpd state=present" -b -o
```
ansible start httpd adhoc command
```
ansible webservers -m service -a "name=httpd state=started" -b
```
disable web service
```
ansible webservers -m service -a "name=httpd state=stopped" -b

```
