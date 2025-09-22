# ansible-juniper-example - virtualenv Ansible prêt à l'emploi pour utiliser la [collection insa_strasbourg.juniper](https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection/)

Ce projet fournit tout le nécessaire pour mettre en œuvre un virtualenv prêt à l'emploi permettant d'exploiter la [collection insa_strasbourg.juniper](https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection/), ainsi que des exemples de configuration.

## Récupération des fichiers du projet

```bash
git clone https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection.git [dossier_destination]
```

## Mise en place du virtualenv

### Pré-requis

Le script de création du virtualenv a été créé et testé sous Ubuntu. Il utilise les commandes `dpkg-query` et `apt` et devrait donc fonctionner sous la plupart des distributions basées sur Debian mais ne fonctionnera **PAS** sur les autres types de distributions.

### Fonctionnement du script

1. Vérification de la présence sur le système d'une version de Python supportée
2. Installation des paquets suivants s'ils ne sont pas déjà présents sur le système :
    - direnv
    - python3-venv
    - sshpass
    - whois
    - libssh-dev
3. Vérification de la présence de la commande `direnv` sur le système - celle-ci activera et désactivera automatiquement le virtualenv en changeant de répertoire de travail
4. Ajout du dossier de travail dans les dossiers autorisés par direnv (`direnv allow`)
5. Vérification que direnv se soit bien chargé, et que son hook existe
6. Suppression et création du virtalenv dans le dossier `.venv-ansible`, sauf si l'option `-e` ou `--dont-recreate-venv` a été spécifiée
7. Suppression et du dossier cache d'Ansible `.ansible` contenant les rôles et collections, sauf si l'option `-d` ou `--dont-delete-ansible-dir` a été spécifiée
8. Installation d'Ansible et des dépendances Python `wheel` ainsi que celles contenues dans le ficher `requirements-python.txt` - sauf si l'option `-p` ou `--dont-install-python-requirements` a été spécifiée
9. Installation des dépendances Ansible (collections/roles) d'après le contenu du fichier `requirements.yml` - sauf si l'option `-a` ou `--dont-install-ansible-requirements`a été spécifiée
10. Installation d'ansible-lint selon le contenu du fichier `requirements-python-ansible-lint.txt` - uniquement si l'option `-l` ou `--install-ansible-lint` a été spécifiée
11. Installation de l'extension VSCode Ansible `redhat.ansible` - uniquement si l'option `-c` ou `--install-ansible-vscode-extensions` a été spécifiée
12. Installation des paramètres de dossier de travail VSCode dans `.vscode/settings.json`, et d'un fichier de workspace prêt à être chargé contenant les mêmes paramètres dans `.vscode/*nom du dossier racine*.code-workspace` - uniquement si l'option `-s` ou `--install-ansible-vscode-settings` a été spécifiée

### Création du virtualenv

Si les pré-requis sont satisfaits, il suffit de lancer l'une de ces commandes, selon vos besoins :

```bash
# - crée le virtualenv
# - installe Ansible, les dépendances Python, les dépendances Ansible
# - installe ansible-lint
# - installe l'extension 'redhat.ansible' pour vscode
# - paramètre le dossier/workspace pour vscode : un fichier 'vscode/ansible-juniper-example.code-workspace' sera créé
./reset_ansible_env -l -c -s

# - crée le virtualenv
# - installe Ansible, les dépendances Python, les dépendances Ansible
# - installe ansible-lint
./reset_ansible_env -l

# - crée le virtualenv
# - installe Ansible, les dépendances Python, les dépendances Ansible
./reset_ansible_env
```

### Ce qui est mis en place, et où

- Le virtualenv et les dépendances Python sont installées dans le dossier `.venv-ansible`
- Les dépendances Ansible (rôles, collections) et le cache d'ansible-galaxy dans le dossier `.ansible`
- L'éventuelle configuration VSCode est installée dans le dossier `.vscode`

## Utilisation des fichiers d'exemple pour créer la configuration initiale

### Documentation exhaustive des rôles

- [insa_strasbourg.ex_config](https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection/blob/main/collections/ansible_collections/insa_strasbourg/juniper/docs/ex_config.fr.md)
- [insa_strasbourg.ex_firmware](https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection/blob/main/collections/ansible_collections/insa_strasbourg/juniper/docs/ex_firmware.fr.md)

### Copie des fichiers d'exemple

```code bash
cp -a EXAMPLES/base/* .
```

### Détail des fichiers

#### Inventaire

Fichier `network` : fichier d'inventaire comportant tous les groupes requis, ainsi qu'un groupe facultatif `juniper_datacenter`, et comportant quatre commutateurs d'exemple :

- `datacenter-001` (EX4600)
- `datacenter-002` (EX4300)
- `distribution-003` (EX3300)
- `distribution-004` (EX4100)

#### Configuration globale

La configuration globale est définie par la configuration du groupe `juniper`, dont tous les équipements sont membres. Par souci de clarté, elle a été éclatée dans 14 fichiers du dossier `group_vars/juniper/`, mais elle pourrait tout aussi bien être présente dans un unique fichier `group_vars/juniper.yml`. Ces fichiers sont :

- `firmware.yml` : Configuration globale pour la mise à jour des firmwares
- `ansible.yml` : Configuration spécifique d'Ansible
- `global.yml` : Options globales
- `firewall_denied_ips.yml` : Liste des IP devant être bloquées par le pare-feu des commutateurs
- `network.yml` : Configuration réseau des commutateurs
- `ntp.yml` : Serveurs NTP à utiliser pour synchroniser les horloges
- `poe.yml` : Activation ou non du PoE par défaut
- `port_security.yml` : Configuration des options de sécurité réseau : storm control, dhcp snooping, dynamic ARP inspection (DAI), IP source guard
- `qos.yml` : Règles de QoS (CoS dans le jargon Juniper)
- `snmp.yml` : Clients SNMP autorisés à interroger les commutateurs, et cibles des traps SNMP
- `stp.yml` : Configuration spanning-tree
- `syslog.yml` : Serveurs Syslog où propager les logs
- `users.yml` : Utilisateurs à créer sur les commutateurs
- `vlans.yml` : Liste des VLAN à déployer sur les commutateurs

#### Exemple de surcharge de configuration pour les commutateurs du groupe juniper_datacenter

Dans notre exemple, ces commutateurs appliquent la configuration du groupe `juniper`, et la surchargent avec les valeurs définies dans `group_vars/juniper_datacenter.yml`. Cette surcharge est rendue possible par la variable d'inventaire [ansible_group_priority](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html#how-variables-are-merged).

#### Configuration spécifique par famille (modèle) de commutateurs

Un fichier par famille de modèle de commutateur existe également dans le dossier `group_vars`. Par exemple `group_vars\juniper_ex4100.yml`.
Ces fichiers contiennent certains paramètres permettant aux rôles de fonctionner qui ne devraient pas être modifiés, mais aussi deux variables à paramétrer pour gérer les mises à jour de firmware `ex_firmware_dir` et `ex_firmware`.

#### Configuration individuelle des commutateurs

La configuration spécifique des commutateurs se fait dans les fichiers `host_vars` de chaque équipement de l'inventaire. Dans notre exemple :

- `host_vars/datacenter-001.yml`
- `host_vars/datacenter-002.yml`
- `host_vars/distribution-003.yml`
- `host_vars/distribution-004.yml`

## Usage courant

### Pré-requis

Que ce soit pour un déploiement de configuration ou une mise à jour de firmware, l'utilisateur exécutant le playbook Ansible doit être en mesure de se connecter en SSH aux commutateurs, idéalement par clé sans avoir à saisir de mot de passe/passphrase. L'usage d'un agent SSH est fortement conseillé !

### Génération de la configuration hors-ligne

S'il n'est pas possible de se connecter en SSH au commutateur, par exemple pour déployer une première configuration, il est possible de générer un fichier de configuration qu'il sera possible de copier sur l'équipement avec une clé USB.
Ceci peut également être appliqué à la configuration d'exemple fournie pour avoir une idée de la configuration qui sera appliquée sur les commutateurs.

Les fichiers de configuration seront générés dans le dossier spécifié par la variable `ex_config_save_dir`, définie dans `group_vars/juniper/ansible.yml`. Dans l'exemple fourni, ce sera dans le dossier `$HOME/.juniper-configs-example/`.

```bash
# Génération de la configuration hors-ligne pour tous les commutateurs de l'inventaire
ansible-playbook -i network insa_strasbourg.juniper.ex_config --tags offline

# Génération de la configuration hors-ligne pour les commutateurs membres du groupe juniper_datacenter
ansible-playbook -i network -l juniper_datacenter insa_strasbourg.juniper.ex_config --tags offline

# Génération de la configuration hors-ligne pour les commutateurs spécifiés
ansible-playbook -i network -l distribution-003,distribution-004 insa_strasbourg.juniper.ex_config --tags offline
```

### Autres options de déploiement

- [insa_strasbourg.ex_config](https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection/blob/main/collections/ansible_collections/insa_strasbourg/juniper/docs/ex_config.fr.md#d%C3%A9ploiement)
- [insa_strasbourg.ex_firmware](https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection/blob/main/collections/ansible_collections/insa_strasbourg/juniper/docs/ex_config.fr.md#d%C3%A9ploiement)
