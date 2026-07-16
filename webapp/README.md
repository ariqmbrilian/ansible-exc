# Web app project

A load-balanced web app deployed across three tiers with roles. Also the place
where the more advanced concepts (roles, vault, custom facts, variables) are
practiced.

Run the full stack:

```bash
cd webapp
ansible-playbook site.yml
```

## Layout

| Path | What it is |
|------|-----------|
| `ansible.cfg` | Project config (own inventory + ssh key). |
| `hosts` | Inventory: `lb` / `webservers` / `db` groups. |
| `site.yml` | **Main playbook** — applies the `lb-tier`, `app-tier`, `db-tier` roles. |
| `roles/lb-tier` | HAProxy load balancer. |
| `roles/app-tier` | Apache + vhost + index page. |
| `roles/db-tier` | Database restore from `files/userdb.backup`. |

## Standalone learning playbooks

These are step-by-step experiments, not part of `site.yml`:

| File | Concept |
|------|---------|
| `deploy_apache.yml` | Plain Apache install + start + index.html. |
| `variable_test.yml` | Same, driven by play-level `vars`. |
| `main_playbook.yml` | Uses `vars/variables.yml` + `tasks/environment.yml` includes. |
| `setup_facts.yml` + `setup_facts_httpd.yml` + `custom.fact` | Install and use custom local facts. |
| `create_users.yml` + `secret.yml` | Create users from a Vault-encrypted var file. |
| `check_webservers.yml` | Ping check. |
| `webserver_smoketest.yml` | HTTP 200 check with `uri`. |

## Vault demo files

`secret.yml`, `super-secret*.yml`, `vault-pass` demonstrate `ansible-vault`
(encrypt/decrypt). `vault-pass` is the password file — keep it out of any public
repo.
