# PRE-DEPLOYMENT GUIDE for OKD 4.18 on VMware using Fedora CoreOS

## 1. Prepare Bastion Node

- Install Ansible:
```bash
sudo dnf install -y epel-release
sudo dnf install -y ansible-core
```

## 2. Import Fedora CoreOS OVA

Download and import the OVA to vSphere for bootstrap, masters, and workers.

## 3. Configure Ignition

Generate ignition files using `openshift-install` and apply using guestinfo variables or mounted ISO.

## 4. Generate and Deploy SSH Key

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa
for ip in <node_ips>; do
  ssh-copy-id -i ~/.ssh/id_rsa.pub core@$ip
done
```

## 5. Verify Connectivity

```bash
ansible -i inventory.yml all -m ping
```

## 6. Run the Playbook

```bash
ansible-playbook -i inventory.yml site.yml
```
