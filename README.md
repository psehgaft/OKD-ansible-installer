# OKD 4.18 Installation Guide on VMware with Fedora CoreOS

This document provides a step-by-step guide to install OKD 4.18 using Fedora CoreOS,
with automation powered by Ansible using a role-based structure.

## Overview
- Cluster Name: okd-qro
- Base Domain: inguzp.cloud
- HAProxy Bastion (RHEL 9): 1 server
- Masters: 3
- Workers: N
- Bootstrap: 1
- Web Console: https:/cluster-name.domain.prefix
- Storage: Local + VMware

## VMware Image (OVA) Setup

Instead of using the ISO, this deployment uses the prebuilt OVA image for Fedora CoreOS:

**Download the OVA image**:  
[Download OVA](https://builds.coreos.fedoraproject.org/prod/streams/stable/builds/42.20250427.3.0/x86_64/fedora-coreos-42.20250427.3.0-vmware.x86_64.ova)

This OVA is compatible with VMware environments and simplifies the creation of VMs for:
- Bootstrap
- Control Plane (Masters)
- Compute Nodes (Workers)

Import the OVA into your vSphere environment and attach the generated Ignition config via guestinfo metadata or mounted virtual disk.

## Playbook Structure

This playbook is modular and organized in roles:
- `prepare_host`
- `generate_ignition`
- `configure_haproxy`
- `deploy_cluster`
- `post_install`

Each role contains:
- `tasks/`: Main automation steps
- `templates/`: Templated configuration files
- `files/`: Static files used in setup
- `vars/`: Role-specific variables
- `defaults/`: Default variables
- `handlers/`: Ansible handlers
