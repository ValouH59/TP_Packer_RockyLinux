# TP Rocky Linux - Packer/Ansible

## Prérequis sur la VM Principale

Après installation de la VM Rocky Linux dans VMware Workstation, on peut installer les prérequis du système

### Installer Ansible

Tout d'abord, il faut installer le paquet Ansible pour pouvoir exécuter le playbook.yaml

```
yum install ansible -y
```

### Installer le package Ansible POSIX

Ce paquet sera nécessaire pour pouvoir modifier le SELinux en mode Permissive sur la VM que l'on va build

```
yum install ansible-collection-ansible-posix -y
```

## Clonage du répertoire configuré

Le repo initial est disponible ici :

```
git clone https://github.com/BastienBalaud/packer-course-bootstrap.git
```

Le repo configuré est disponible en clonant mon projet :

```
git clone https://github.com/ValouH59/TP_Packer_RockyLinux.git
```

## Que contiennent les fichiers de configuration ?

- golangmyip_service/golang-myip.service : Il s'agit du template du service golang-myip.service
- ks/rocky-8.cfg : Contient la configuration automatique de base de Rocky Linux que l'on souhaite build
- playbook.yaml : Contient les instructions Ansible pour déployer les paquets et services demandés
- rocky-8.pkr.hcl : Il s'agit du fichier de configuration nécessaire à Packer pour build la VM

## Lancer le build avec Packer

Pour lancer le build avec Packer il suffit d'éxecuter dans le répertoire où se trouve le fichier de configuration .hcl, la commande :

```
packer build rocky-8.pkr.hcl
```

## Lancer la VM

Lors du 1er build, Packer nous affiche ce message :

```
qemu.example: output will be in this color.

==> qemu.example: Retrieving ISO
==> qemu.example: Trying ../Rocky-8.4-x86_64-minimal.iso
==> qemu.example: Trying ../Rocky-8.4-x86_64-minimal.iso?checksum=sha256%3A0de5f12eba93e00fefc06cdb0aa4389a0972a4212977362ea18bde46a1a1aa4f
==> qemu.example: ../Rocky-8.4-x86_64-minimal.iso?checksum=sha256%3A0de5f12eba93e00fefc06cdb0aa4389a0972a4212977362ea18bde46a1a1aa4f => /root/Rocky-8.4-x86_64-minimal.iso
==> qemu.example: Starting HTTP server on port 8622
==> qemu.example: Found port for communicator (SSH, WinRM, etc): 2904.
==> qemu.example: Looking for available port between 5900 and 6000 on 127.0.0.1
==> qemu.example: Starting VM, booting from CD-ROM
    qemu.example: The VM will be run headless, without a GUI. If you want to
    qemu.example: view the screen of the VM, connect via VNC without a password to
    qemu.example: vnc://127.0.0.1:5999
==> qemu.example: Waiting 10s for boot...
==> qemu.example: Connecting to VM via VNC (127.0.0.1:5999)
==> qemu.example: Typing the boot command over VNC...
```

On doit alors utiliser un autre terminal pour créer un tunnel SSH sur la VM afin ensuite de se connecter avec un client VNC
Pour créer un tunnel SSH il faut utiliser cette commande :

```
ssh -L 5999:127.0.0.1:5999 root@192.168.1.31
```

On lance ensuite le client VNC avec comme adresse IP : 127.0.0.1 et avec le port qui nous a été attribué par Packer. Ici il s'agit du port 5999

Une fenêtre GUI va s'ouvrir et on va alors pouvoir suivre l'installation de la VM en direct

### Exécution du playbook.yaml

Ansible va donc lancer le playbook.yaml que l'on a précédemment configuré :

```
==> qemu.example: Provisioning with Ansible...
    qemu.example: Setting up proxy adapter for Ansible....
==> qemu.example: Executing Ansible: ansible-playbook -e packer_build_name="example" -e packer_builder_type=qemu -e packer_http_addr=10.0.2.2:8622 --ssh-extra-args '-o IdentitiesOnly=yes' -e ansible_ssh_private_key_file=/tmp/ansible-key1261892468 -i /tmp/packer-provisioner-ansible921927046 /root/packer-course-bootstrap/playbook.yaml
    qemu.example:
    qemu.example: PLAY [all] *********************************************************************
    qemu.example:
    qemu.example: TASK [Gathering Facts] *********************************************************
    qemu.example: ok: [default]
    qemu.example:
    qemu.example: TASK [Installation de Git] *****************************************************
    qemu.example: changed: [default]
    qemu.example:
    qemu.example: TASK [Clonage du repository Golang-myip] ***************************************
    qemu.example: changed: [default]
    qemu.example:
    qemu.example: TASK [Installation de la commande make] ****************************************
    qemu.example: changed: [default]
    qemu.example:
    qemu.example: TASK [Installation du langage Go] **********************************************
    qemu.example: changed: [default]
    qemu.example:
    qemu.example: TASK [Execution du fichier Makefile dans son répertoire] ***********************
    qemu.example: changed: [default]
    qemu.example:
    qemu.example: TASK [Création du ficher golang-myip.service dans le dossier Temporaire] *******
    qemu.example: changed: [default]
    qemu.example:
    qemu.example: TASK [Ajout du contenu du service] *********************************************
    qemu.example: changed: [default]
    qemu.example:
    qemu.example: TASK [Déplacement du fichier golang-myip.service dans systemd] *****************
    qemu.example: changed: [default]
    qemu.example:
    qemu.example: TASK [Changer temporairement le SELinux en Permissive pour activer le service] ***
    qemu.example: changed: [default]
    qemu.example:
    qemu.example: TASK [Redémarrage du Daemon système] *******************************************
    qemu.example: changed: [default]
    qemu.example:
    qemu.example: TASK [Démarrage du nouveau service Golang-MyIP] ********************************
    qemu.example: changed: [default]
    qemu.example:
    qemu.example: TASK [Activation du service Golang-MyIP au démarrage du système] ***************
    qemu.example: changed: [default]
    qemu.example:
    qemu.example: TASK [Rendre le mode Permissive du SELinux permanent] **************************
    qemu.example: changed: [default]
    qemu.example:
    qemu.example: TASK [Ajout des clés SSH prof] *************************************************
    qemu.example: changed: [default]
    qemu.example:
    qemu.example: PLAY RECAP *********************************************************************
    qemu.example: default                    : ok=15   changed=14   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    qemu.example:
==> qemu.example: Gracefully halting virtual machine...
==> qemu.example: Converting hard drive...
Build 'qemu.example' finished after 12 minutes 9 seconds.
```

### Build opérationnel

Une fois que Packer a terminé le build, il va afficher un message :

```
==> Wait completed after 12 minutes 9 seconds

==> Builds finished. The artifacts of successful builds are:
--> qemu.example: VM files in directory: build-rocky-8
```

On trouvera alors dans le répertoire build-rocky-8/ un fichier qui s'appelle tp-1 et qui correspond à notre VM que l'on vient de build
