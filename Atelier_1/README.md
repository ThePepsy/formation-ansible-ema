# Exercice 1

## Objectif
Effectuer les opérations suivantes sur une machine virtuelle Ubuntu :

- Démarrez la VM ubuntu depuis le répertoire atelier-01.
- Connectez-vous à cette VM.
- Rafraîchissez les informations sur les paquets.
- Recherchez le paquet ansible avec les options qui vont bien.
- Installez le paquet officiel fourni par la distribution.
- Vérifiez si l’installation s’est bien déroulée.
- Notez la version d’Ansible.
- Déconnectez-vous et supprimez la VM.

## Étapes

### 1. Démarrer la VM Ubuntu
Dans le répertoire `atelier-01`, exécutez :
```bash
cd atelier-01
vagrant up
```

### 2. Se connecter à la VM
```bash
vagrant ssh ubuntu
```

### 3. Rafraîchir les informations sur les paquets
```bash
sudo apt update
```

### 4. Rechercher le paquet Ansible
```bash
apt search ansible | grep "^ansible"
```

### 5. Installer Ansible
```bash
sudo apt install -y ansible
```

### 6. Vérifier l'installation et noter la version
```bash
ansible --version
```
```bash
ansible 2.10.8
```

### 7. Se déconnecter de la VM
```bash
exit
```

### 8. Supprimer la VM
```bash
vagrant destroy ubuntu
```

# Exercice 2

## Objectif
Répétez l’exercice précédent en configurant un dépôt PPA (Personal Package Archive) pour Ansible.

## Étapes

1. Démarrer et se connecter à la VM comme précédemment.
2. Ajouter le dépôt PPA pour Ansible :
   ```bash
   sudo apt-add-repository ppa:ansible/ansible
   ```
3. Mettre à jour la liste des paquets :
   ```bash
   sudo apt update
   ```
4. Installer Ansible depuis le PPA :
   ```bash
   sudo apt install -y ansible
   ```
5. Vérifier l’installation et noter la version :
   ```bash
   ansible --version
   ```
   ```bash
   ansible [core 2.17.8]
   ```
6. ansible __2.10.8__ vs core __2.17.8__
7. Se déconnecter et supprimer la VM comme dans l’exercice précédent.

# Exercice 3

## Objectif
Lancer une VM Rocky Linux et installer Ansible en utilisant **PIP** et **Virtualenv**.

## Remarque importante
Contrairement à Debian, le paquet `python3-venv` n’est pas nécessaire ici, car **Virtualenv** fait partie des modules standard de Python dans cette distribution.

## Étapes

1. Démarrer et se connecter à la VM Rocky Linux.
   ```bash
   vagrant up rocky
   vagrant ssh rocky
   ```
2. Mettre à jour la liste des paquets :
   ```bash
   sudo dnf check-update
   ```
3. Installer Python et PIP (si ce n'est pas déjà fait).
   ```bash
   sudo dnf install -y python3 python3-pip
   ```
4. Installer **Virtualenv**.
   ```bash
   pip3 install --user virtualenv
   ```
5. Créer un environnement virtuel et l’activer.
   ```bash
   python3 -m venv ~/.venv/ansible
   source ~/.venv/ansible/bin/activate
   ```
6. Installer Ansible via **PIP** dans l’environnement virtuel.
   ```bash
   pip install ansible
   ```
7. Vérifier l’installation et noter la version.
   ```bash
   ansible --version
   ```
   resultat:
      ```bash
   ansible [core 2.15.13]
   ```
8. Désactiver l’environnement virtuel et quitter la VM.
   ```bash
   deactivate
   exit
   ```
9. Supprimer la VM.
   ```bash
   vagrant destroy rocky
   ```

## Conclusion
Avec cet exercice, nous avons installé Ansible sur **Rocky Linux** en utilisant **PIP** et **Virtualenv**, offrant ainsi une méthode alternative et flexible par rapport aux installations précédentes. 🚀

