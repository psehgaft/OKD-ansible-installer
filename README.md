# OKD 4.18 Installation - Full Ansible Bundle

This package contains a full Ansible playbook structure for deploying an OKD 4.18 cluster.


## Contents:
- `site.yml`: Main playbook
- `roles/`: Modular roles for each phase of installation

## Overview
- Cluster Name: okd-qro
- Base Domain: inguzp.cloud
- HAProxy Bastion (RHEL 9): 1 server
- Masters: 3
- Workers: N
- Bootstrap: 1
- Web Console: https:/cluster-name.domain.prefix
- Storage: Local + VMware


## Usage:
Customize variables under each role's `defaults/main.yml` and run the playbook with:
```bash
ansible-playbook -i inventory site.yml
```
