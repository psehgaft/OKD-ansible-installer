# OKD 4.18 Installation - Ansible Playbook Structure

This repository provides a modular Ansible playbook structure for deploying an OKD 4.18 cluster on VMware using Fedora CoreOS.

## Structure

- `site.yml` - Main entrypoint playbook
- `inventory.yml` - Example inventory defining groups: bastion, bootstrap, masters, workers
- `roles/` - Modular roles:
  - `prepare_host`
  - `generate_ignition`
  - `configure_haproxy`
  - `deploy_cluster`
  - `post_install`
- `PRE_DEPLOY.md` - Pre-installation steps and instructions
- `README.md` - This documentation

## Usage

1. Configure inventory and variable files under `roles/*/defaults/main.yml`
2. Run connectivity test:
```bash
ansible -i inventory.yml all -m ping
```
3. Execute the playbook:
```bash
ansible-playbook -i inventory.yml site.yml
```
