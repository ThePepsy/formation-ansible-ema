# Atelier 06 - Ansible : Exercice récapitulatif
## Objectif

Voici les quatre machines virtuelles Ubuntu 22.04 de cet atelier :
```bash
Machine virtuelle 	Adresse IP
control 	192.168.56.10
target01 	192.168.56.20
target02 	192.168.56.30
target03 	192.168.56.40
```

   - Éditez /etc/hosts de manière à ce que les Target Hosts soient joignables par leur nom d’hôte simple.
   - Configurez l’authentification par clé SSH avec les trois Target Hosts.
   - Installez Ansible.
   - Envoyez un premier ping Ansible sans configuration.
   - Créez un répertoire de projet ~/monprojet.
   - Créez un fichier vide ansible.cfg dans ce répertoire.
   - Vérifiez si ce fichier est bien pris en compte par Ansible.
   - Spécifiez un inventaire nommé hosts.
   - Activez la journalisation dans ~/journal/ansible.log.
   - Testez la journalisation.
   - Créez un groupe [testlab] avec vos trois Target Hosts.
   - Définissez explicitement l’utilisateur vagrant pour la connexion à vos cibles.
   - Envoyez un ping Ansible vers le groupe de machines [all].
   - Définissez l’élévation des droits pour l’utilisateur vagrant sur les Target Hosts.
   - Affichez la première ligne du fichier /etc/shadow sur tous les Target Hosts.
   - Quittez le Control Host et supprimez toutes les VM de l’atelier.

## Préparation de l'environnement

1. **Se placer dans le répertoire de l'atelier**
   ```sh
   cd ~/formation-ansible/atelier-06
   ```
2. **Démarrer les machines virtuelles**
   ```sh
   vagrant up
   ```
3. **Se connecter à la machine de contrôle**
   ```sh
   vagrant ssh control
   ```

## Configuration initiale

### Modifier `/etc/hosts` pour ajouter les machines cibles

```sh
sudo nano /etc/hosts
```
Ajouter les lignes suivantes :
```txt
192.168.56.20 target01
192.168.56.30 target02
192.168.56.40 target03
```
Enregistrer et quitter.

### Configurer l'authentification SSH par clé

Générer une clé SSH (si elle n'existe pas déjà) :
```sh
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa -N ""
```
Copier la clé sur chaque cible :
```sh
ssh-copy-id vagrant@target01
ssh-copy-id vagrant@target02
ssh-copy-id vagrant@target03
```
Vérifier l'accès :
```sh
ssh vagrant@target01 "echo OK"
ssh vagrant@target02 "echo OK"
ssh vagrant@target03 "echo OK"
```

## Installation et premier test d'Ansible

### Installer Ansible
```sh
sudo apt update
sudo apt install -y ansible
```
Vérifier l'installation :
```sh
ansible --version
```

### Envoyer un premier ping Ansible sans configuration
```sh
ansible all -i "target01,target02,target03," -m ping 
```

## Configuration du projet Ansible

### Créer le répertoire de projet
```sh
mkdir ~/monprojet
cd ~/monprojet
```

### Créer un fichier `ansible.cfg`
```sh
touch ansible.cfg
```

### Vérifier que `ansible.cfg` est pris en compte
```sh
ansible --version | head -n 2
```
```sh
vagrant@control:~$ ansible --version | head -n 2
ansible 2.10.8
  config file = None
```
```sh
vagrant@control:~$ cd monprojet/
vagrant@control:~/monprojet$ ansible --version | head -n 2
ansible 2.10.8
  config file = /home/vagrant/monprojet/ansible.cfg

```


### Définir un inventaire `hosts`
Créer le fichier `hosts` :
```sh
nano ~/monprojet/hosts
```
Ajouter :
```ini
[testlab]
target01
target02
target03

[testlab:vars]
ansible_user=vagrant
```

### Activer la journalisation Ansible
Créer le répertoire de journalisation :
```sh
mkdir -p ~/journal
```
Modifier `ansible.cfg` :
```sh
nano ~/monprojet/ansible.cfg
```
Ajouter :
```ini
[defaults]
inventory = ~/monprojet/hosts
log_path = ~/journal/ansible.log
```

### Tester la journalisation
Envoyer un ping :
```sh
ansible all -m ping 
```
```sh
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

```
Vérifier les logs :
```sh
cat ~/journal/ansible.log
```
```sh
2025-02-12 10:00:51,257 p=3814 u=vagrant n=ansible | target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
2025-02-12 10:00:51,275 p=3814 u=vagrant n=ansible | target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
2025-02-12 10:00:51,276 p=3814 u=vagrant n=ansible | target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

```

### Tester un ping avec le groupe [testlab] avec les trois Target Hosts`[all]`
`hosts`
```ini
[testlab]
target01
target02
target03

[testlab:vars]
ansible_user=vagrant
``
```sh
ansible all -m ping
```

### Activer l'élévation des privilèges (sudo)
Modifier `hosts` :
```sh
nano ~/monprojet/hosts
```
Ajouter ansible_become=yes dans le hosts:
```ini
[testlab:vars]
ansible_user=vagrant
ansible_become=yes
```

### Exécuter une commande sur les cibles
Afficher la première ligne de `/etc/shadow` :
```sh
ansible all -m shell -a "head -n 1 /etc/shadow"
```
```sh
target01 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
target03 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
target02 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
```

## Nettoyage de l'environnement

### Quitter et supprimer les VM
```sh
exit
vagrant destroy -f
```



