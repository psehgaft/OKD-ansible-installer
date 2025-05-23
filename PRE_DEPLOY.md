# 🛠️ PRE-DEPLOYMENT GUIDE for OKD 4.18 on VMware using Fedora CoreOS

Este documento describe **todos los pasos necesarios previos a la ejecución del playbook Ansible** para desplegar un clúster OKD 4.18 sobre VMware usando Fedora CoreOS (FCOS).

---

## 📦 1. Preparar el nodo bastión (RHEL 9)

Este nodo tendrá:

- Ansible instalado
- Acceso SSH a todos los nodos Fedora CoreOS
- HAProxy (opcional)
- Conexión a internet

**Instalar Ansible en RHEL 9:**
```bash
sudo dnf install -y epel-release
sudo dnf install -y ansible-core
```

---

## 🧰 2. Descargar e importar la imagen OVA de Fedora CoreOS

Usa esta imagen para crear las VMs (bootstrap, masters, workers):

🔗 [Descargar Fedora CoreOS OVA (VMware)](https://builds.coreos.fedoraproject.org/prod/streams/stable/builds/42.20250427.3.0/x86_64/fedora-coreos-42.20250427.3.0-vmware.x86_64.ova)

**Pasos:**
1. En vSphere, selecciona **"Deploy OVF Template"**.
2. Importa la imagen OVA.
3. Asigna una IP estática a cada VM según tu topología.
4. **No inicies las VMs aún.**

---

## 📝 3. Configurar archivos Ignition

Antes de iniciar las VMs:

1. En tu nodo bastión, usa `openshift-install` para generar los Ignition:
   ```bash
   openshift-install create install-config --dir=/tmp/okd-install
   openshift-install create ignition-configs --dir=/tmp/okd-install
   ```

2. Lee los archivos `.ign` y convierte su contenido a Base64:
   ```bash
   base64 -w0 /tmp/okd-install/bootstrap.ign > bootstrap.64
   base64 -w0 /tmp/okd-install/master.ign > master.64
   base64 -w0 /tmp/okd-install/worker.ign > worker.64
   ```

3. Asigna estos valores como `guestinfo.ignition.config.data` en vSphere:
   - `guestinfo.ignition.config.data`: contenido Base64
   - `guestinfo.ignition.config.data.encoding`: `base64`

---

## 🔐 4. Generar y desplegar llaves SSH

**En el nodo bastión:**
```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa
```

**Copiar la llave pública a todos los nodos FCOS:**
```bash
for ip in 192.168.7.18 192.168.7.12 192.168.7.13 192.168.7.14 192.168.17.12 192.168.17.13 192.168.17.14; do
  ssh-copy-id -i ~/.ssh/id_rsa.pub core@$ip
done
```

> Asegúrate de que los nodos estén encendidos y accesibles por SSH.

---

## 🌐 5. Verificar conectividad Ansible

Ejecuta este comando desde el nodo bastión:
```bash
ansible -i inventory.yml all -m ping
```

✅ Esperado: todos los nodos responden con `"pong"`.

---

## 📁 6. Organizar tu entorno de ejecución

Estructura recomendada:
```
okd4.18_installer_full/
├── site.yml
├── inventory.yml
├── README.md
├── PRE_DEPLOY.md
└── roles/
    ├── prepare_host/
    ├── generate_ignition/
    ├── configure_haproxy/
    ├── deploy_cluster/
    └── post_install/
```

---

## 🚀 7. Ejecutar el playbook

Una vez verificado todo:
```bash
ansible-playbook -i inventory.yml site.yml
```
