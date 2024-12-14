<h1 align="center" style="color: red;">Les Playbooks</h1>

### Introduction
👋 Dans cette section, nous allons explorer comment écrire et exécuter les playbooks.

<p align="center">
  <img src="images/playbook.png" alt="cap" style="width: 400px;"/>
</p>  

- l'extention de playbook peut être .yml ou .yaml.
- Pour vérifier la syntaxe on fait
``` bash
 ansible-playbook --syntax-check
 ```
- Pour exécuter le playbook on fait
``` bash
 ansible-playbook playbook.yml
 ```


## Syntaxe
```bash
- name: Description du playbook
  hosts: [hôtes]
  become: [true/false]
  tasks:
    - name: Description de la tâche 1
      [module]: 
        [arguments]
    - name: Description de la tâche 2
      [module]: 
        [arguments]
```


### LAB 02
#### Q0. Utilisez un playbook Ansible pour ajouter un utilisateur nommé « alice » avec un shell /sbin/nologin et un home directory /host/alice sur toutes les machines hôtes, aussi pour modifier l'utilisateur « bob » afin que son shell soit /bin/bsh, son UID soit 2006, et son home directory soit /home/bob. 

``` bash
- name: ajouter alice te modifier bob
  hosts: all
  tasks:
  - name: ajouter alice
    user:
      name: alice
      home: /home/alice
      shell: /bin/bash
      state: present
  - name: modifier bob
    user:
      name: bob
      uid: 2006
      home: /home/bob
      shell: /bin/sh
      state: present
```

#### Q1. Utilisez un playbook Ansible pour supprimer l'utilisateur « charlie » de toutes les machines hôtes.

``` bash
- name: supprimer charlie
  hosts: all
  become: true
  tasks:
  - name: delete charlie
    user:
      name: charlie
      state: absent
```

#### Q2. Utilisez un playbook Ansible pour installer le package httpd sur toutes les machines hôtes, vérifier l'installation du package httpd en utilisant le module command, ensuite démarrer et activer ce service.


``` bash
- name: install and start service httpd
  hosts: all
  tasks:
  - name: install httpd
    yum:
      name: httpd
      state: latest
  - name: start and enable httpd
    service:
      name: httpd
      state: started
      enabled: yes
```

#### Q3. Utilisez un playbook Ansible pour ajouter une ligne au fichier /etc/hosts sur toutes les machines hôtes. La ligne à ajouter est '10.10.10.100 myserver'.


``` bash
- name: add new line in /etc/hosts
  hosts: all
  tasks:
  - name: modified ligne
    lineinfile:
      path: /etc/hosts
      line: 10.10.10.100 myserver
```

#### Q4. Utilisez un playbook Ansible pour copier un fichier nommé /config.json depuis la machine de contrôle vers le répertoire /etc/myapp/ sur la machine app-server.


``` bash
- name: copy /config.json file
  hosts: all
  tasks:
  - name: créer répertoire /etc/myapp
    file:
      path: /etc/myapp
      state: directory
  - name: copier fichier
    copy:
      src: /config.json
      dest: /etc/myapp
```

#### Q5. Utilisez un playbook Ansible pour créer un répertoire nommé /backup sur toutes les machines hôtes avec les permissions 755, appartenant à l'utilisateur root et au groupe root.


``` bash
- name: create /backup file
  hosts: all
  become: true
  tasks:
  - name: création répertoire
    file:
      path: /backup
      state: directory
      mode: 755
      owner: root
      group: root
```

### Playbook avec plusieurs plays
Un playbook Ansible peut contenir plusieurs "plays". Chaque "play" applique un ensemble de tâches à un groupe d'hôtes. Cela permet de structurer le playbook pour réaliser différentes phases d'une configuration ou d'un déploiement.  
Exemple 1:  
- play1: ajouter un utilisateur
- play2: le modifier  
NB: on utilise plusieurs plays pour qu'on puisse exécuter les plays dans différentes hotes `hosts`

```bash
- name: play1 to add group and its user
  hosts: all
  tasks:
  - name: add the group
    group:
      name: group1
      gid: 4900
  - name: add the user
    user:
      name: user1
      uid: 4008
      group: group1
- name: play2 to modify the user1
  hosts: all
  tasks:
  - name: modify
    user:
      name: user1
      uid: 4007
```



