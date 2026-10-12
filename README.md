# ansible-playbooks

Playbooks I reuse to bring up Ubuntu servers: base packages, SSH hardening,
Docker, and an nginx reverse proxy in front of whatever app is running.

Tested on Ubuntu 22.04.

## Layout

- `site.yml` — the whole run: common -> hardening -> docker -> nginx
- `inventory.example.ini` — example inventory, copy to `inventory.ini`
- `group_vars/all.yml` — the knobs (timezone, admin user, ssh port, app port)
- `roles/common` — apt update, base packages, timezone, unattended-upgrades, admin user
- `roles/hardening` — sshd config (no root login, no password auth), sysctl tweaks, fail2ban
- `roles/docker` — Docker engine + compose plugin from Docker's repo, users added to the docker group
- `roles/nginx` — nginx with a reverse-proxy vhost to `127.0.0.1:{{ app_port }}`

## Quickstart

Dry run first:

```
cp inventory.example.ini inventory.ini
# edit inventory.ini and group_vars/all.yml
ansible-playbook -i inventory.ini site.yml --check
```

Then for real:

```
ansible-playbook -i inventory.ini site.yml
```

Run one piece with tags:

```
ansible-playbook -i inventory.ini site.yml --tags hardening
```

## Variables you will want to change

In `group_vars/all.yml`: `timezone`, `admin_user`, `ssh_port`, `app_port`,
`server_name`, `ntp_servers`.

Note: disabling password auth in sshd means you need key-based access working
before you run the hardening role, or you will lock yourself out.
