# OKD-ansible-installer

Ansible structure for deploying an OKD 4.18 cluster.

## Intended topology

From the bundle outline:

- Cluster name: `okd-qro`
- Base domain: `inguzp.cloud`
- HAProxy bastion (RHEL 9): 1 server
- Masters: 3
- Workers: N
- Bootstrap: 1
- Web console: `https://<cluster-name>.<domain>`
- Storage: local + VMware

## Layout

| Path | Purpose |
| --- | --- |
| `site.yml` | Main playbook: runs every role, in order, against all hosts (`hosts: all`) |
| `roles/prepare_host/` | Prepare the target hosts |
| `roles/generate_ignition/` | Generate the Ignition configs |
| `roles/configure_haproxy/` | Configure HAProxy on the bastion |
| `roles/deploy_cluster/` | Deploy the cluster |
| `roles/post_install/` | Post-installation steps |
| `playbook.yml` | Separate playbook for installing the OKD installer on a Fedora 41 VM |
| `vars.yml` | Variables for the installer playbook (release, architecture, directories, pull secret path) |

The roles run in this order: `prepare_host`, `generate_ignition`,
`configure_haproxy`, `deploy_cluster`, `post_install`.

## Requirements

- Ansible on the control node.
- Target hosts reachable over SSH, with privilege escalation (`become: true`
  is set in `site.yml`).
- An inventory of your own: **no inventory file ships in this repo**, so
  provide one when you run the playbook.
- A pull secret at the path configured in `vars.yml` for the installer
  playbook.

## Usage

Customize the variables under each role's `defaults/main.yml`, provide your
inventory, and run:

```bash
ansible-playbook -i inventory site.yml
```

## Current status — read before running

This bundle is a scaffold, not a finished installer yet:

- The five roles in `roles/` currently contain placeholder tasks only (a
  `debug` message each). The real provisioning steps still need to be
  implemented before `site.yml` can deploy a cluster.
- `playbook.yml` references a role named `okd_installer` that is not present
  in `roles/`; running it as-is will fail until that role is added.
- `vars.yml` pins OKD release `4.16.0-0.okd-2024-04-16-152930`, while this
  bundle targets OKD 4.18 — align the release string before use.

## License

GNU General Public License v3 — see `LICENSE`.
