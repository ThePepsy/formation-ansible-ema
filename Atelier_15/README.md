# Atelier 15 - Ansible : Récupération d'informations système

## Objectif
Exercice

Pour vous familiariser avec la récupération d'informations système à l'aide d'Ansible, vous allez créer des playbooks qui affichent des informations détaillées sur le noyau et le nombre de paquets RPM installés sur vos machines cibles.

- Écrivez un playbook `kernel.yml` qui affiche les informations détaillées du noyau sur tous vos Target Hosts en utilisant la commande `uname -a` et le module `debug` avec le paramètre `msg`.
- Essayez d’obtenir le même résultat en utilisant le paramètre `var` du module `debug`.
- Écrivez un playbook `packages.yml` qui affiche le nombre total de paquets RPM installés sur les hôtes `rocky` et `suse` en utilisant la commande `rpm -qa | wc -l`.

## Étapes de l'exercice

### 1. Démarrer les VMs
```bash
cd ~/formation-ansible/atelier-15
vagrant up
```

### 2. Connexion au Control Host
```bash
vagrant ssh ansible
```

### 3. Se rendre dans le répertoire du projet
```bash
cd ansible/projets/ema/playbooks/
```

### 4. Création du playbook `kernel.yml`
```bash
nano kernel.yml
```
```yml
---
- hosts: all
  gather_facts: false
  tasks:
    - name: Récupérer les informations du noyau avec uname -a
      command: uname -a
      register: kernel_info
      changed_when: false

    - name: Afficher les infos du noyau avec debug msg
      debug:
        msg: "Kernel info: {{ kernel_info.stdout }}"

    - name: Afficher les infos du noyau avec debug var
      debug:
        var: kernel_info.stdout_lines
```

### 5. Vérifier la syntaxe du fichier `kernel.yml`
```bash
yamllint kernel.yml
```
Aucune sortie, le fichier `kernel.yml` est correct.

### 6. Exécuter le playbook `kernel.yml`
```bash
ansible-playbook kernel.yml
```

### 7. Création du playbook `packages.yml`
```bash
nano packages.yml
```
```yml
---
- hosts: rocky,suse
  gather_facts: false
  tasks:
    - name: Compter le nombre total de paquets RPM installés
      shell: "rpm -qa | wc -l"
      register: rpm_count
      changed_when: false

    - name: Afficher le nombre total de paquets RPM installés
      debug:
        msg: "Nombre total de paquets RPM: {{ rpm_count.stdout | trim }}"
```

### 8. Vérifier la syntaxe du fichier `packages.yml`
```bash
yamllint packages.yml
```
Aucune sortie, le fichier `packages.yml` est correct.

### 9. Exécuter le playbook `packages.yml`
```bash
ansible-playbook packages.yml
```

### 10. Quitter et supprimer les VMs
```bash
exit
vagrant destroy -f
```

## Observation
L’exécution de ces playbooks montre que :
- Les informations détaillées du noyau peuvent être récupérées et affichées en utilisant le module `debug` avec les paramètres `msg` et `var`.
- Le nombre total de paquets RPM installés peut être compté et affiché en utilisant la commande `rpm -qa | wc -l`.