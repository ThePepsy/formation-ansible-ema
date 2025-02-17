# Atelier 08 - Ansible : Idempotence et commandes ad hoc

## Objectif
Exercice

Pour vous familiariser avec la notion d’idempotence, exécutez une série de tâches administratives sur toutes les machines cibles. Pour ce faire, servez-vous des commandes ad hoc que nous avons vues dans le précédent article. Prenez soin d’exécuter chaque commande deux fois et observez ce qui se passe dans l’affichage du résultat.

-   Installez successivement les paquets tree, git et nmap sur toutes les cibles.
-   Désinstallez successivement ces trois paquets en utilisant le paramètre supplémentaire state=absent.
- Copier le fichier /etc/fstab du Control Host vers tous les Target Hosts sous forme d’un fichier /tmp/test3.txt.
- Supprimez le fichier /tmp/test3.txt sur les Target Hosts en utilisant le module file avec le paramètre state=absent.
- Enfin, affichez l’espace utilisé par la partition principale sur tous les Target Hosts. Que remarquez-vous ?


## Étapes de l'exercice

### 1. Installation de paquets sur les cibles
```sh
ansible all -m package -a "name=tree"
ansible all -m package -a "name=git"
ansible all -m package -a "name=nmap"
```

### 2. Désinstallation des paquets
```sh
ansible all -m package -a "name=tree state=absent"
ansible all -m package -a "name=git state=absent"
ansible all -m package -a "name=nmap state=absent"
```

### 3. Copie d’un fichier vers les cibles
Copier `/etc/fstab` du Control Host vers tous les Target Hosts sous `/tmp/test3.txt` :
```sh
ansible all -m copy -a "src=/etc/fstab dest=/tmp/test3.txt mode=0644"
```

### 4. Suppression du fichier sur les cibles
```sh
ansible all -m file -a "path=/tmp/test3.txt state=absent"
```

### 5. Afficher l’espace disque utilisé sur les cibles
```sh
ansible all -m command -a "df -h /"
```

## Observation
L’exécution répétée de ces commandes montre que :
- Installer un paquet déjà installé ne change rien.
- Désinstaller un paquet déjà absent ne génère pas d’erreur.
- Copier un fichier déjà présent remplace son contenu, mais ne crée pas de doublon.
- Supprimer un fichier déjà absent ne génère pas d’erreur.
- La commande `df -h /` est purement informative et n’altère pas l’état du système.

Cela illustre bien la notion d’idempotence en Ansible ! ✅

