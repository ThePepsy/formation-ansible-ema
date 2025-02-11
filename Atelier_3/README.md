
# Exercice 4

## Objectif
Faites le nécessaire pour réussir un ping Ansible comme ceci :

```bash
$ ansible all -i target01,target02,target03 -m ping
target03 | SUCCESS => {
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
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

## Étapes

1. Démarrer les VM et se connecter au Control Host.
   ```bash
   cd ~/formation-ansible/atelier-02
   vagrant up
   vagrant ssh ansible
   ```
2. Vérifier qu’Ansible est installé.
   ```bash
   type ansible
   ```
3. Modifier le fichier `/etc/hosts` pour ajouter les entrées des Target Hosts.
   ```bash
   sudo nano /etc/hosts
   ```
   Ajouter :
   ```bash
    192.168.56.10  control.sandbox.lan    control
    192.168.56.20  target01.sandbox.lan   target01
    192.168.56.30  target02.sandbox.lan   target02
    192.168.56.40  target03.sandbox.lan   target03
   ```
4. Collecter les clés SSH des Target Hosts.
   ```bash
   ssh-keyscan -t rsa target01 target02 target03 >> .ssh/known_hosts
   ```
   ```bash
    #target01:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.10
    #target03:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.10
    #target02:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.10
   ```
5. Générer une paire de clés SSH sur le Control Host.
   ```bash
   ssh-keygen
   ```
6. Distribuer la clé publique sur les Target Hosts.
   ```bash
   ssh-copy-id vagrant@target01
   ssh-copy-id vagrant@target02
   ssh-copy-id vagrant@target03
   ```
7. Tester l’authentification avec Ansible.
   ```bash
   ansible all -i target01,target02,target03 -m ping
   ```
   ```bash
   target02 | SUCCESS => {
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
    target03 | SUCCESS => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python3"
        },
        "changed": false,
        "ping": "pong"
    }
   ```
8. Quitter le Control Host et supprimer les VM.
    ```bash
    exit
    vagrant destroy -f
    ```


