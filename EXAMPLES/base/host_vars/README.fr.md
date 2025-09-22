# ansible-juniper-example - virtualenv Ansible prêt à l'emploi pour utiliser la [collection insa_strasbourg.juniper](https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection/)

Ce projet fournit tout le nécessaire pour mettre en oeuvre un virtualenv prêt à l'emploi permettant d'exploiter la [collection insa_strasbourg.juniper](https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection/), ainsi que des exemples de configuration.

## Récupération des fichiers du projet

```bash
git clone https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection.git [dossier_destination]
```

## Mise en place du virtualenv

### Pré-requis

Le script de création du virtualenv a été créé et testé sous Ubuntu. Il utilise les commandes `dpkg-query` et `apt` et devrait donc fonctionner sous la plupart des distributions basées sur Debian mais ne fonctionnera **PAS** sur les autres types de distributions.

Il installera les paquets suivants s'ils ne sont pas déjà présents sur le système :

- direnv
- python3-venv
- sshpass
- whois
- libssh-dev

### Création du virtualenv

Si les pré-requis sont satisfaits, il suffit de lancer l'une de ces commandes, selon vos besoins :

```bash
# - crée le virtualenv
# - installe les dépendances Python, ansible, les dépendances Ansible
# - installe installe ansible-lint
# - installe l'extension 'redhat.ansible' pour vscode
# - paramètre le dossier/workspace pour vscode : un fichier 'vscode/ansible-juniper-example.code-workspace' sera créé
./reset_ansible_env -l -c -s

# - crée le virtualenv
# - installe les dépendances Python, ansible, les dépendances Ansible
# - installe installe ansible-lint
./reset_ansible_env -l

# - crée le virtualenv
# - installe les dépendances Python, ansible, les dépendances Ansible
./reset_ansible_env
```

### Qu'est ce qui est mis en place, et où

- Le virtualenv et les dépendances Python sont intallées dans le dossier `.venv-ansible`
- les dépendances Ansible (rôles, collection) et le cache d'ansible-galaxy dans le dossier `.ansible`

## Utilisation des fichiers d'exemple pour créer la configuration initiale


