# Ansible Notes & Practice

Personal Ansible cookbook. Small, self-contained playbooks grouped by target,
plus one full multi-tier project.

```
.
├── ansible.cfg          # points at inventory/hosts
├── inventory/           # host lists
├── linux/               # Linux playbooks, one file per topic
├── windows/             # Windows (WinRM) playbooks
├── network/             # network-device playbooks
└── webapp/              # full multi-tier project (roles + vault + facts)
```

Each `linux/` file bundles several worked examples as separate plays — open one
file to see every variation of that topic in one place.

---

## Ad-hoc command cheat sheet

```bash
ansible all -m ping                              # ping all hosts
ansible -i inventory/hosts webservers -m ping    # ping a group in a given inventory
ansible all -m command -a "hostname"             # run a command
ansible vm1 -m setup                             # dump all facts about a host
ansible all -m user -a "name=devops" -b          # create a user (-b = become root)
ansible-playbook --syntax-check linux/users.yml  # check playbook syntax
ansible webservers -m command -a "uptime" -o     # -o = one line per host

# package / service (RHEL)
ansible webservers -m yum     -a "name=httpd state=present" -b
ansible webservers -m service -a "name=httpd state=started" -b
ansible webservers -m yum     -a "name=httpd state=absent"  -b
```

Run a playbook: `ansible-playbook linux/packages.yml`

---

## Inventory (`inventory/`)

| File | What it is |
|------|-----------|
| `hosts` | Real inventory (default per `ansible.cfg`): `windows` (WinRM) + `ubuntu` groups. |
| `sample-hosts` | Example inventory in INI format (lb / webservers / db). |

## Linux (`linux/`)

| File | Covers |
|------|--------|
| `packages.yml` | Install (apt/yum/dnf), downgrade/pin, install-then-start. |
| `services.yml` | Service start/stop/restart/reload/enable + firewalld rules. |
| `files.yml` | copy, create dir, in-place `replace`, motd, archive. |
| `users.yml` | Create groups and users. |
| `storage.yml` | LVM: vg → lv → filesystem → mount. |
| `updates.yml` | Patch Ubuntu (apt) and RHEL (yum). |
| `ssh-hardening.yml` | Disable root SSH login (validated + restart handler). |
| `docker.yml` | Install Docker CE + docker-compose on Ubuntu. |

## Windows (`windows/`) — WinRM

| File | Covers |
|------|--------|
| `hostname.yml` | Rename hosts in a loop, reboot if required. |
| `certificate-authority.yml` | Install AD CS root CA. |
| `security-logging.yml` | Disable RDP + scheduled task logging uptime. |
| `features.yml` | Install a Windows feature (Telnet client). |
| `domain-controller.yml` | Install AD DS and promote a domain controller. |
| `updates.yml` | Install all Windows updates, reboot. |

## Network (`network/`)

| File | Covers |
|------|--------|
| `backup-router.yml` | Backup Cisco IOS running-config (needs `network_cli`). |

## Web app project (`webapp/`)

The full example — see [`webapp/README.md`](webapp/README.md).

---

## ⚠️ Secrets note

`inventory/hosts` contains plaintext passwords, and `webapp/` commits the Vault
password (`vault-pass`) next to vaulted files. Fine for a lab; if this repo ever
goes public, rotate those and gitignore `vault-pass`.
