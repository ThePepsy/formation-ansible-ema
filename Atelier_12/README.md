# Atelier 12 - Synchronisation NTP avec Ansible

## Objectif
L'objectif de cet atelier est de configurer la synchronisation NTP sur un groupe de machines Rocky Linux en utilisant Ansible.

## Prérequis
- Environnement Ansible préconfiguré
- Accès SSH configuré entre le Control Host et les Target Hosts
- YAMLLint installé pour la validation de la syntaxe

## Configuration des machines virtuelles
### Démarrage des VM
```bash
cd ~/formation-ansible/atelier-12
vagrant up
```

### Connexion au Control Host
```bash
vagrant ssh control
```

### Accès au répertoire du projet
```bash
cd ansible/projets/ema/
```

### Inventaire des machines
Le fichier `inventory` contient la configuration suivante :
```ini
[redhat]
target01
target02
target03

[redhat:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=vagrant
ansible_become=yes
```

## Création du playbook `chrony.yml`
Créez le fichier `playbooks/chrony.yml` et ajoutez le contenu suivant :
```yaml
---
- name: Configure NTP with Chrony
  hosts: redhat
  become: true
  tasks:
    - name: Install chrony package
      dnf:
        name: chrony
        state: present

    - name: Enable and start chronyd service
      service:
        name: chronyd
        state: started
        enabled: true

    - name: Deploy custom Chrony configuration
      copy:
        dest: /etc/chrony.conf
        mode: '0644'
        content: |
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      notify: Restart chronyd

  handlers:
    - name: Restart chronyd
      service:
        name: chronyd
        state: restarted
```

## Vérification et exécution
### Validation de la syntaxe du playbook
```bash
yamllint playbooks/chrony.yml
```

### Exécution du playbook
```bash
ansible-playbook playbooks/chrony.yml
```

### Vérification de l'idempotence
Exécutez une nouvelle fois le playbook pour vérifier qu'aucune modification n'est apportée si tout est déjà en place.
```bash
ansible-playbook playbooks/chrony.yml
```

### Vérification du service Chrony sur les hôtes cibles
```bash
ansible redhat -a "systemctl status chronyd"
```

## Nettoyage
Pour arrêter et détruire les machines virtuelles :
```bash
exit
vagrant destroy -f
```

Ce processus garantit une synchronisation précise de l'heure sur toutes les machines cibles, essentielle pour la cohérence et la sécurité du système.

