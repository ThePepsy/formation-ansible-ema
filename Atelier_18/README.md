# Atelier 18 - Configuration de Chrony avec Ansible

## Objectif
L'objectif de cet atelier est de configurer la synchronisation NTP avec Chrony sur un groupe de machines en utilisant Ansible, en tenant compte des différences de chemins de configuration selon le système d'exploitation.

## Prérequis
- Environnement Ansible préconfiguré
- Accès SSH configuré entre le Control Host et les Target Hosts
- YAMLLint installé pour la validation de la syntaxe

## Configuration des machines virtuelles
### Démarrage des VM
```bash
cd ~/formation-ansible/atelier-18
vagrant up
```

### Connexion au Control Host
```bash
vagrant ssh ansible
```

### Accès au répertoire du projet
```bash
cd ansible/projets/ema/playbooks/
```

## Création des fichiers de configuration
### Création d'un template pour Chrony
Créez le fichier `templates/chrony.conf.j2` avec le contenu suivant :
```jinja
{# templates/chrony.conf.j2 #}
# {{ chrony_conf_path }}
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```

### Création d'un fichier de variables
Créez le fichier `vars/chrony_vars.yml` avec le contenu suivant :
```yml
---
chrony_package:
  Debian: chrony
  RedHat: chrony
  Suse: chrony

chrony_service:
  Debian: chrony
  RedHat: chronyd
  Suse: chronyd

chrony_conf_path:
  Debian: /etc/chrony/chrony.conf
  RedHat: /etc/chrony.conf
  Suse: /etc/chrony.conf
```

### Création du playbook `chrony.yml`
Créez le fichier `chrony.yml` et ajoutez le contenu suivant :
```yml
---
- hosts: all
  become: true
  vars_files:
    - vars/chrony_vars.yml
  tasks:
    - name: Install Chrony
      package:
        name: "{{ chrony_package[ansible_os_family] }}"
        state: present

    - name: Start and enable Chrony service
      service:
        name: "{{ chrony_service[ansible_os_family] }}"
        state: started
        enabled: true

    - name: Configure Chrony
      template:
        src: templates/chrony.conf.j2
        dest: "{{ chrony_conf_path[ansible_os_family] }}"
      notify: Restart Chrony

  handlers:
    - name: Restart Chrony
      service:
        name: "{{ chrony_service[ansible_os_family] }}"
        state: restarted
```

## Vérification et exécution
### Validation de la syntaxe du playbook
```bash
yamllint chrony.yml
```
_Si une erreur mineure de longueur de ligne est présente, elle peut être ignorée._

### Exécution du playbook
```bash
ansible-playbook chrony.yml
```

### Vérification de la configuration sur un hôte cible
```bash
ssh rocky
cat /etc/chrony.conf
```

## Nettoyage
Pour quitter et détruire les machines virtuelles :
```bash
exit
exit
vagrant destroy -f
```

