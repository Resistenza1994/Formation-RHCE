<h1 align="center" style="color: red;">Les variables et ansible Facts</h1>

### Introduction
👋 Dans cette section, nous allons explorer comment utiliser les variables suivant deux méthodes et comment utiliser les 'Facts'.


### Les variables
#### Utilisation de `vars`

Les variables peuvent être utilisées pour faire référence à un large éventail de dynamiques. données, telles que les noms de fichiers, services, packages, utilisateurs, URL vers des serveurs spécifiques, et bien plus encore..
Pour définir une variable, il suffit d’utiliser la syntaxe clé : valeur dans une section vars dans l'en-tête de Playbook.

Certaines règles doivent être prises en considération en nommant une variable:
Les noms de variables doivent commencer par une lettre.
Les noms de variables sont sensibles à la casse- Les noms de variables ne peuvent contenir que des lettres, des chiffres, et des tirets bas.


Dans cette méthode, nous définissons les variables directement dans le playbook sous la section `vars`
Exemple 1 :

```bash

- name: Ajouter utilisateur avec des variables définies
  hosts: all
  vars:
    username: "alice"
    home_directory: "/home/alice"
    user_shell: "/bin/bash"
  tasks:
    - name: Ajouter utilisateur
      user:
        name: "{{ username }}"
        home: "{{ home_directory }}"
        shell: "{{ user_shell }}"
        state: present

```

#### Utilisation de `vars_files`
Dans cette méthode, nous stockons les variables dans un fichier séparé puis on fait l'appel de fichier dans la section `vars_files`

```bash

vim variables.yml

```

```bash

username: "alice"
home_directory: "/home/alice"
user_shell: "/bin/bash"

```

```bash

vim playbook.yml

```

```bash

- name: Ajouter utilisateur avec un fichier de variables
  hosts: all
  become: true
  vars_files:
    - variables.yml
  tasks:
    - name: Ajouter utilisateur
      user:
        name: "{{ username }}"
        home: "{{ home_directory }}"
        shell: "{{ user_shell }}"
        state: present
```

Variable magiques :
Les variables magiques sont des variables définies automatiquement par Ansible pour refléter un état interne à Ansible. Il y a environ 30 variables magiques.
Il n’est pas possible de modifier la valeur d’une variable magique. Elle sera réinitialisée toujours à la valeur par défaut.

Exemple des variables magiques :
hostvars ==> Contient tous les hôtes de l'inventaire et leurs variables assignées
groups ==> Contient tous les groupes de l’inventaire
Group_names ==> Les groupes dont cet hôte est actuellement membre
Inventory_hostname ==> Spécifie le nom d'hôte d'inventaire pour l'hôte actuel
Inventory_file ==> Spécifie le nom du fichier d'inventaire courant qui est utilisé



### Facts

Pour afficher les facts

```bash

ansible node1 -m setup | less

```

Lorsqu'un playbook Ansible est utilisé, il rassemble d'abord des faits sur tous les hôtes cibles.
Le résultat de ce processus est stocké dans la variable ansible_facts.
Les faits ansibles sont des propriétés système collectées sur un système distant.
Après collecte, les faits peuvent être utilisés comme variables.
Par défaut, lors de l'exécution de chaque playbook, les faits sont collectés. Cela ralentit les playbooks.
Pour désactiver la collecte des Faits, il faut utiliser le paramètre gather_facts: no dans l’entete de Playbook.

Facts ansible les plus utilisés :

Ansible_facts['hostname'] ==> Nom de machine
Ansible_facts[‘ interfaces’] ==> La liste des interfaces réseau
Ansible_facts[‘distribution_version’] ==> La version de la distribution actuelle
ansible_facts[’default_ipv4’][’address’] ==> Adresse ipv4 de la machine
Ansible_facts[‘devices’] ==> La liste des composants de stockage
Ansible_facts[‘devices’][‘sda’][‘partitions’][‘sda1’][‘size’] ==> La taille de la partition sda1


Exemple 1: Playbook pour affichage de facts.

```bash 

- name: Ansible Facts Playbook
  hosts: all
  tasks:
  - name: Display Facts method1
    debug:
      var: ansible_default_ipv4.address
  - name: Display Facts method2
    debug:
      var: ansible_facts['default_ipv4']['address']

```

Exemple 2: Playbook pour affichage de facts.

```bash 

- name: Ansible Facts Playbook
  hosts: all
  tasks:
  - name: Display ansible fact
    debug:
      var: ansible_hostname

```


