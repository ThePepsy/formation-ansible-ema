# Atelier 14 - Gestion des Variables dans Ansible  

## Objectif  
L'objectif de cet atelier est d'explorer différentes méthodes pour gérer les variables dans Ansible, notamment via les play vars, `set_fact`, `group_vars`, `host_vars` et les variables interactives.  

## Prérequis  
- Environnement Ansible préconfiguré  
- Accès SSH configuré entre le Control Host et les Target Hosts  
- YAMLLint installé pour la validation de la syntaxe  

## Configuration des machines virtuelles  

### Démarrage des VM  
```bash
cd ~/formation-ansible/atelier-14
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

## Définition des variables dans un playbook (Play Vars)  

### Création du playbook `myvars1.yml`  
```bash
nano playbooks/myvars1.yml
```  
```yaml
---
- hosts: all
  gather_facts: false
  vars:
    mycar: "Volkswagen Polo II"
    mybike: "Harley-Davidson"
  tasks:
    - name: Afficher ma voiture et ma moto préférées (play vars)
      debug:
        msg: "J'adore la voiture: {{ mycar }}, et la moto: {{ mybike }}"
```  

### Validation et exécution du playbook  
#### Vérification de la syntaxe  
```bash
yamllint playbooks/myvars1.yml
```  

#### Exécution du playbook  
```bash
ansible-playbook playbooks/myvars1.yml
```  

#### Passage de variables en ligne de commande  
```bash
ansible-playbook playbooks/myvars1.yml -e mycar="Alpine"
ansible-playbook playbooks/myvars1.yml -e mybike="Suzuki"
ansible-playbook playbooks/myvars1.yml -e mycar="Rolls-Royce" -e mybike="Yamaha"
```  

## Définition des variables avec `set_fact`  

### Création du playbook `myvars2.yml`  
```bash
nano playbooks/myvars2.yml
```  
```yaml
---
- hosts: all
  gather_facts: false
  tasks:
    - name: Définir la voiture et la moto préférées avec set_fact
      set_fact:
        mycar: "Volkswagen Polo II"
        mybike: "Harley-Davidson"
    - name: Afficher ma voiture et ma moto préférées (set_fact)
      debug:
        msg: "J'adore la voiture: {{ mycar }}, et la moto: {{ mybike }}"
```  

### Validation et exécution du playbook  
```bash
yamllint playbooks/myvars2.yml
ansible-playbook playbooks/myvars2.yml
```  

#### Passage de variables en ligne de commande  
```bash
ansible-playbook playbooks/myvars2.yml -e mycar="Alpine"
ansible-playbook playbooks/myvars2.yml -e mybike="Suzuki"
ansible-playbook playbooks/myvars2.yml -e mycar="Rolls-Royce" -e mybike="Yamaha"
```  

## Définition des variables avec `group_vars` et `host_vars`  

### Création des répertoires  
```bash
mkdir -p group_vars host_vars
```  

### Définition des variables globales (`group_vars/all.yml`)  
```bash
nano group_vars/all.yml
```  
```yaml
mycar: "VW"
mybike: "BMW"
```  

### Définition des variables spécifiques à `target02` (`host_vars/target02.yml`)  
```bash
nano host_vars/target02.yml
```  
```yaml
mycar: "Mercedes"
mybike: "Honda"
```  

### Création du playbook `myvars3.yml`  
```bash
nano playbooks/myvars3.yml
```  
```yaml
---
- hosts: all
  gather_facts: false
  tasks:
    - name: Afficher ma voiture et ma moto préférées (group_vars/host_vars)
      debug:
        msg: "J'adore la voiture: {{ mycar }}, et la moto: {{ mybike }}"
```  

### Validation et exécution du playbook  
```bash
yamllint playbooks/myvars3.yml
ansible-playbook playbooks/myvars3.yml
```  

## Utilisation des variables interactives  

### Création du playbook `display_user.yml`  
```bash
nano playbooks/display_user.yml
```  
```yaml
---
- hosts: all
  gather_facts: false
  vars_prompt:
    - name: "user"
      prompt: "Entrez votre super nom d'utilisateur"
      default: "microlinux"
      private: false
    - name: "password"
      prompt: "Entrez votre mot de passe sécurisé"
      default: "yatahongaga"
      private: true
  tasks:
    - name: Afficher le nom d'utilisateur et le mot de passe
      debug:
        msg: "Ton super nom d'utilisateur est {{ user }} et ton mot de passe est {{ password }}"
```  

### Validation et exécution du playbook  
```bash
yamllint playbooks/display_user.yml
ansible-playbook playbooks/display_user.yml
```  

## Nettoyage  
Pour quitter et supprimer toutes les VM :  
```bash
exit
vagrant destroy -f
```  

Ce processus permet d’explorer les différentes méthodes de gestion des variables dans Ansible pour rendre les playbooks plus dynamiques et flexibles. 🚀

