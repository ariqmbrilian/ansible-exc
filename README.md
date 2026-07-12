# Ansible Notes & Practice

Personal scratchpad of Ansible playbooks and ad-hoc commands. Two parts:

- **Root folder** — small single-purpose practice playbooks (one topic each).
- **`ansible_implementation/`** — a fuller multi-tier project (roles: lb / app / db, vault, custom facts).

---

## 1. Ad-hoc command cheat sheet

```bash
ansible all -m ping                              # ping all hosts
ansible -i [inventory] [group] -m ping           # ping a group in a given inventory
ansible all -m command -a "hostname"             # run a command
ansible vm1 -m setup                             # dump all facts about a host
ansible all -m user -a "name=devops" -b          # create a user (-b = become root)
ansible-playbook --syntax-check user.yml         # check playbook syntax
ansible webservers -m command -a "uptime" -o     # -o = one line per host

# package / service (RedHat)
ansible webservers -m yum -a "name=httpd state=present" -b
ansible webservers -m service -a "name=httpd state=started" -b
ansible webservers -m service -a "name=httpd state=stopped" -b
ansible webservers -m yum -a "name=httpd state=absent" -b
```

---

## 2. Config & inventory

| File | What it is |
|------|-----------|
| `ansible.cfg` | Default config — points inventory at `etc/ansible/hosts`, disables host key checking. |
| `etc/ansible/hosts` | Real inventory: a `windows` (WinRM) group and an `ubuntu` group. YAML format. |
| `sample/hosts` | Example inventory (lb / webservers / db). INI format. |
| `ansible_implementation/hosts` | Inventory for the multi-tier project. |

---

## 3. Root practice playbooks

### Linux — packages
| File | Notes |
|------|-------|
| `install-package.yml` | Install a list of packages (apt + yum examples). |
| `redhat-install-package.yml` | Install packages with `dnf`. |
| `downgrade-package.yaml` | Pin/downgrade a package with `allow_downgrade`. |
| `install-start-service.yaml` | Install firewalld then start it. |

### Linux — services
| File | Notes |
|------|-------|
| `start-service.yaml` | Start httpd (with/without `enabled`). |
| `restart-service.yml` | start / stop / restart / reload / enable service examples. |
| `firewalld.yaml` | firewalld source / port / service rules. |

### Linux — files & config
| File | Notes |
|------|-------|
| `create-dir.yml` | Create a directory with owner/mode. |
| `copy-file.yaml` | Copy a welcome file, reload httpd. |
| `replace-httpd-port.yaml` | Edit httpd.conf with `replace`, restart Apache. |

### Linux — updates
| File | Notes |
|------|-------|
| `update-ubuntu.yml` | apt upgrade + autoremove. |
| `redhat-update.yml` | yum `*` update + autoremove. |
| `update-os.yaml` | Mixed Windows + Ubuntu update snippets. |

### Windows (WinRM)
| File | Notes |
|------|-------|
| `1-hostname.yml` | Rename hosts in a loop, reboot if required. |
| `2-cert.yml` | Install AD CS root Certificate Authority. |
| `3-sec-log.yml` | Disable RDP service + scheduled task logging uptime. |
| `4-tools.yml` | Install Telnet client feature. |
| `5-environment.yml` | Install AD Domain Services, promote domain controller. |
| `update.yml` | Windows Update, reboot. |

### RedHat vs Ubuntu split
`redhat-*` files target RHEL/CentOS (`yum`/`dnf`); `update-ubuntu.yml`, `docker.yaml` target Debian/Ubuntu (`apt`).

---

## 4. `ansible_implementation/` — multi-tier project

The "real" project. Deploys a load-balanced web app across three tiers using roles.

| File | Notes |
|------|-------|
| `webapp-main.yml` | Top-level play: applies `lb-tier`, `app-tier`, `db-tier` roles. |
| `main_playbook.yml` | Installs firewall + Apache, opens ports, writes index.html. Uses `vars/` and `tasks/`. |
| `deploy_apache.yml` | Simple Apache install + start + index.html. |
| `variable_test.yml` | Same, driven by play-level `vars`. |
| `setup_facts.yml` / `setup_facts_httpd.yml` / `custom.fact` | Install custom facts, then use them. |
| `create_users.yml` | Create users from encrypted `secret.yml`. |
| `check_webservers.yml` | Ping check. |
| `webserver_smoketest.yml` | HTTP 200 check via `uri`. |
| `secret.yml`, `super-secret*.yml`, `vault-pass` | Ansible Vault demo files. See warning below. |
| `roles/{lb,app,db}-tier/` | Roles for each tier (haproxy / apache+vhost / db restore). |

---

## 5. Cleanup suggestions

**Empty placeholder files (0 content — safe to delete or fill in):**
`archive.yml`, `backup-router.yml`, `group.yml`, `lvm.yml`, `motd.yml`, `ssh-feature.yml`, `user.yml`

**Duplicates / overlap:**
- `copy-file.yml` is malformed (task has `src:`/`dest:` with no `copy:` module) — `copy-file.yaml` is the working one. Delete `copy-file.yml`.
- `start-service.yaml` contains the same play twice — trim to one.
- `sample/apache.yml` ≈ `ansible_implementation/deploy_apache.yml` — keep one as the reference.

**Naming consistency:** files mix `.yml` and `.yaml`. Pick one (`.yml` is the common convention) and rename the rest.

**⚠️ Secrets:** `etc/ansible/hosts` has plaintext passwords, and the repo commits `vault-pass` (the Vault password) alongside vaulted files. If this ever goes public, rotate those and add `vault-pass` to `.gitignore`.
