# PRE-DEPLOYMENT GUIDE for OKD 4.18 on VMware using Fedora CoreOS

This document outlines all the preparatory steps you must complete **before executing the Ansible playbook**.

---

## 🧭 1. Prepare the Bastion Node (RHEL 9)

Ensure this node:
- Is connected to the internal network and internet
- Has Ansible installed
- Can SSH into all Fedora CoreOS nodes

**Install Ansible:**
```bash
sudo dnf install -y epel-release
sudo dnf install -y ansible-core
```

---

## 📥 2. Download and Import the Fedora CoreOS OVA on VMware

Use the OVA to create VMs for:
- 1 Bootstrap node
- 3 Control Plane (masters)
- 3 Compute (workers)

**OVA download:**
[https://builds.coreos.fedoraproject.org/prod/streams/stable/builds/42.20250427.3.0/x86_64/fedora-coreos-42.20250427.3.0-vmware.x86_64.ova](https://builds.coreos.fedoraproject.org/prod/streams/stable/builds/42.20250427.3.0/x86_64/fedora-coreos-42.20250427.3.0-vmware.x86_64.ova)

**Import the OVA into vSphere**:
1. Use "Deploy OVF Template" in vCenter.
2. Assign static IPs as per your cluster plan.
3. DO NOT POWER ON YET — first attach the Ignition file and configure guestinfo.

---

## 🔧 3. Configure Ignition for Fedora CoreOS VMs

Before first boot:
- Generate Ignition files using `openshift-install create ignition-configs`
- Use `guestinfo.ignition.config.data` and `guestinfo.ignition.config.data.encoding` for VMware
  OR
- Attach an Ignition ISO (created with `coreos-installer iso ignition embed`)

---

## 🔐 4. Generate and Deploy SSH Keys (Optional if done by Playbook)

**Generate SSH key on bastion**:
```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa
```

**Add public key to each Fedora CoreOS node**:
```bash
for ip in 192.168.7.18 192.168.7.12 192.168.7.13 192.168.7.14 192.168.17.12 192.168.17.13 192.168.17.14; do
  ssh-copy-id -i ~/.ssh/id_rsa.pub core@$ip
done
```

---

## 🗂️ 5. Organize Playbook Files

Unpack the bundle in a folder like:

```
okd4.18_installer_full/
├── site.yml
├── inventory.yml
├── PRE_DEPLOY.md
├── README.md
└── roles/
```

---

## 🌐 6. Test Ansible Connectivity

Run:
```bash
ansible -i inventory.yml all -m ping
```

Expected output: `pong` from all hosts.

---

## 🚀 7. Execute the Playbook

Run the installation:
```bash
ansible-playbook -i inventory.yml site.yml
```

Ensure all Ignition files and `install-config.yaml` are prepared correctly.

---
