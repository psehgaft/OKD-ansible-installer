# OKD 4.18 Installation - Full Ansible Bundle

This package contains a full Ansible playbook structure for deploying an OKD 4.18 cluster.

## Contents:
- `site.yml`: Main playbook
- `roles/`: Modular roles for each phase of installation

## Usage:
Customize variables under each role's `defaults/main.yml` and run the playbook with:
```bash
ansible-playbook -i inventory site.yml
```
