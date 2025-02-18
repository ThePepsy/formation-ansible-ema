# Atelier 10 - Ansible : Playbooks et Configuration d'Apache

## Objectif
Cet atelier a pour but de créer et exécuter des playbooks Ansible permettant d'installer et configurer Apache sur différentes distributions Linux.

## Préparation de l'environnement

```sh
$ cd ~/formation-ansible/atelier-10
```

```sh
$ vagrant up
```

```sh
$ vagrant ssh ansible
```

L'environnement est préconfiguré avec :
- Ansible installé sur le Control Host.
- Configuration correcte du fichier `/etc/hosts`.
- Authentification par clé SSH configurée.
- Un répertoire de projet existant avec une configuration de base et un inventaire.
- Direnv installé et activé.
- Validateur de syntaxe `yamllint` disponible.

Accédez au répertoire du projet :
```sh
$ cd ansible/projets/ema/
```

## Création des Playbooks

### 1. Playbook pour Debian
```yaml
- name: Installer Apache sur Debian
  hosts: debian
  tasks:
    - name: Update cache information
      apt:
        update_cache: true
    
    - name: Installer Apache
      apt:
        name: apache2
        state: present
    
    - name: Démarrer et activer Apache
      service:
        name: apache2
        state: started
        enabled: true
    
    - name: Définir la page d'accueil
      copy:
        dest: /var/www/html/index.html
        mode: 0664
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>DEBIAN WEB SERVER CONFIGURED BY ANSIBLE</h1>
            </body>
          </html>
```

### 2. Playbook pour Rocky
```yaml
- name: Installer Apache sur Rocky Linux
  hosts: rocky
  tasks:
    - name: Installer Apache
      dnf:
        name: httpd
        state: present
    
    - name: Démarrer et activer Apache
      service:
        name: httpd
        state: started
        enabled: true
    
    - name: Définir la page d'accueil
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>ROCKY WEB SERVER CONFIGURED BY ANSIBLE</h1>
            </body>
          </html>
```

### 3. Playbook pour SUSE
```yaml
- name: Installer Apache sur SUSE
  hosts: suse
  tasks:
    - name: Installer Apache
      zypper:
        name: apache2
        state: present
    
    - name: Démarrer et activer Apache
      service:
        name: apache2
        state: started
        enabled: true
    
    - name: Définir la page d'accueil
      copy:
        dest: /srv/www/htdocs/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>SUSE WEB SERVER CONFIGURED BY ANSIBLE</h1>
            </body>
          </html>
```

## Vérification du bon fonctionnement
Testez l'accès aux serveurs web configurés :
```sh
$ curl debian
$ curl rocky
$ curl suse
```

## Handler Playbook : Configuration de NTP avec Chrony

```yaml
- name: Configure NTP with Chrony
  hosts: redhat
  tasks:
    - name: Install chrony package
      ansible.builtin.package:
        name: chrony
        state: present
    
    - name: Enable and start chronyd service
      ansible.builtin.service:
        name: chronyd
        enabled: yes
        state: started
    
    - name: Check default chrony configuration
      ansible.builtin.command: cat /etc/chrony.conf
      register: default_config
      changed_when: false
    
    - name: Display default chrony configuration
      ansible.builtin.debug:
        var: default_config.stdout_lines
    
    - name: Install custom chrony configuration
      ansible.builtin.copy:
        content: |
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
        dest: /etc/chrony.conf
        owner: root
        group: root
        mode: '0644'
      notify: Restart chronyd

  handlers:
    - name: Restart chronyd
      ansible.builtin.service:
        name: chronyd
        state: restarted
```

## Exécution et Résultat
Exécutez le playbook Chrony :
```sh
$ ansible-playbook chrony.yml
```

Exemple de sortie :
```
PLAY RECAP *****************************************************************
target01                   : ok=7    changed=4    unreachable=0    failed=0    
target02                   : ok=7    changed=4    unreachable=0    failed=0    
target03                   : ok=7    changed=4    unreachable=0    failed=0    
```



