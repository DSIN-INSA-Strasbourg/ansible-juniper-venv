# ansible-juniper-example - Ready-to-use Ansible virtualenv to use the [insa_strasbourg.juniper collection](https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection/)

[![en](https://img.shields.io/badge/lang-en-red.svg)](https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-venv/blob/main/README.md)
[![fr](https://img.shields.io/badge/lang-fr-blue.svg)](https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-venv/blob/main/README.fr.md)

---

This project provides everything needed to implement a ready-to-use virtualenv to leverage the [insa_strasbourg.juniper collection](https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection/), along with configuration examples.

## Supported devices

| Device | Support                        |
| ------ | ------------------------------ |
| EX4650 | 🚧 Work in progress            |
| EX4600 | ✅                             |
| EX4300 | ✅                             |
| EX4100 | ✅                             |
| EX3400 | ✅                             |
| EX3300 | ✅                             |
| EX2300 | ⚠️ Not tested, but should work |
| EX2200 | ✅                             |

## Get project files

```bash
git clone https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection.git [dest_dir]
```

## Setting up the virtualenv

### Requirements

The virtualenv creation script was created and tested under Ubuntu. It uses the `dpkg-query` and `apt` commands and should therefore work under most Debian-based distributions but will **NOT** work on other types of distributions.

### How the script works

1. Checking for a supported version of Python on the system
2. Installing the following packages if they are not already present on the system:
    - direnv
    - python3-venv
    - sshpass
    - whois
    - libssh-dev
3. Checking for the presence of the `direnv` command on the system - this will automatically activate and deactivate the virtualenv by changing the working directory
4. Adding the working folder to the folders allowed by direnv (`direnv allow`)
5. Checking that direnv has loaded correctly, and that its hook exists
6. Deleting and creating the virtalenv in the `.venv-ansible` folder, unless the `-e` or `--dont-recreate-venv` option was specified
7. Deleting the Ansible cache folder `.ansible` containing roles and collections, unless the `-d` or `--dont-delete-ansible-dir` option was specified
8. Installing Ansible and the Python `wheel` dependencies, as well as those contained in the `requirements-python.txt` file - unless the `-p` or `--dont-install-python-requirements` option was specified. **This installation includes a patch for `ncclient` to connect to legacy (non-ELS) Juniper switches**
9. Installing Ansible dependencies (collections/roles) based on the contents of the `requirements.yml` file - unless the `-a` or `--dont-install-ansible-requirements` option was specified
10. Installing ansible-lint according to the contents of the `requirements-python-ansible-lint.txt` file - only if the `-l` or `--install-ansible-lint` option was specified
11. Installing the VSCode Ansible extension `redhat.ansible` - only if the `-c` or `--install-ansible-vscode-extensions` option was specified
12. Installing VSCode working folder settings in `.vscode/settings.json`, and a ready-to-load workspace file containing the same settings in `.vscode/*root folder name*.code-workspace` - only if the `-s` or `--install-ansible-vscode-settings` option was specified

### Creating the virtualenv

If the requirements are met, simply run one of these commands, depending on your needs:

```bash
# - Create the virtualenv
# - Install Ansible, Python dependencies, and Ansible dependencies
# - Install ansible-lint
# - Install the 'redhat.ansible' extension for vscode
# - Configure the folder/workspace for vscode: a 'vscode/ansible-juniper-example.code-workspace' file will be created
./reset_ansible_env -l -c -s

# - Create the virtualenv
# - Install Ansible, Python dependencies, and Ansible dependencies
# - Install ansible-lint
./reset_ansible_env -l

# - Create the virtualenv
# - Install Ansible, Python dependencies, and Ansible dependencies
./reset_ansible_env
```

### What is being created, and where

- The virtualenv and Python dependencies are installed in the `.venv-ansible` folder
- Ansible dependencies (roles, collections) and the ansible-galaxy cache are installed in the `.ansible` folder
- The VSCode configuration is installed in the `.vscode` folder

## Using example files to create the initial configuration

### Documentation

#### Roles

- [`insa_strasbourg.juniper.ex_config`](https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection/blob/main/collections/ansible_collections/insa_strasbourg/juniper/docs/ex_config.md): deploy configuration on Juniper EX switches
- [`insa_strasbourg.juniper.ex_firmware`](https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection/blob/main/collections/ansible_collections/insa_strasbourg/juniper/docs/ex_firmware.md): deploy firmwares on Juniper EX switches

#### Playbooks

- [`insa_strasbourg.juniper.ex_config`](https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection/blob/main/collections/ansible_collections/insa_strasbourg/juniper/docs/ex_config.md#deployment): deploy configuration on Juniper EX switches
- [`insa_strasbourg.juniper.ex_firmware`](https://github.com/DSIN-INSA-Strasbourg/ansible-juniper-collection/blob/main/collections/ansible_collections/insa_strasbourg/juniper/docs/ex_config.md#deployment): deploy firmwares on Juniper EX switches
- `insa_strasbourg.juniper.ex_plan_reboot`: schedule a reboot on Juniper EX switches, or cancel a scheduled reboot
- `insa_strasbourg.juniper.ex_plan_shutdown`: schedule a shutdown on Juniper EX switches, or cancel a scheduled shutdown
- `insa_strasbourg.juniper.ex_show_plan`: print any scheduled reboot or shutdown on Juniper EX switches

### Copying the example files

```code bash
cp -a EXAMPLES/base/* .
```

### File details

#### Inventory

`network` file: Inventory file containing all required groups, plus an optional `juniper_datacenter` group, and containing four example network switches:

- `datacenter-001` (EX4600)
- `datacenter-002` (EX4300)
- `distribution-003` (EX3300)
- `distribution-004` (EX4100)

#### Global configuration

The global configuration is defined by the configuration of the `juniper` group, of which all devices are members. For clarity, it has been split into 14 files in the `group_vars/juniper/` folder, but it could just as easily be present in a single `group_vars/juniper.yml` file. These files are:

- `firmware.yml`: Global configuration for firmware updates
- `ansible.yml`: Ansible-specific configuration
- `global.yml`: Global options
- `firewall_denied_ips.yml`: List of IP addresses to be blocked by the switch firewall
- `network.yml`: Switch network configuration
- `ntp.yml`: NTP servers to use for clock synchronization
- `poe.yml`: Enable or disable PoE by default
- `port_security.yml`: Network security options configuration: storm control, dhcp snooping, dynamic ARP inspection (DAI), IP source guard
- `qos.yml`: QoS rules (CoS in Juniper jargon)
- `snmp.yml`: SNMP clients allowed to query the switches, and SNMP trap targets
- `stp.yml`: Spanning-tree configuration
- `syslog.yml`: Syslog servers to propagate logs to
- `users.yml`: Users to create on the switches
- `vlans.yml`: List of VLANs to deploy on the switches

#### Example of configuration override for switches in the juniper_datacenter group

In our example, these switches apply the configuration of the `juniper` group, and override it with the values ​​defined in `group_vars/juniper_datacenter.yml`. This overriding is made possible by the [ansible_group_priority](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html#how-variables-are-merged) inventory variable.

#### Specific configuration by family (model) of switches

There is also a file for each switch model family in the `group_vars` folder. For example, `group_vars\juniper_ex4100.yml`.
These files contain certain parameters that allow the roles to function and should not be modified, as well as two variables that must be set to manage firmware updates: `ex_firmware_dir` and `ex_firmware`.

#### Individual switch configuration

The specific configuration of the switches is set in the `host_vars` files of each device in the inventory. In our example:

- `host_vars/datacenter-001.yml`
- `host_vars/datacenter-002.yml`
- `host_vars/distribution-003.yml`
- `host_vars/distribution-004.yml`

## Common use

### Requirements

Whether for a configuration deployment or a firmware update, the user running the Ansible playbook must be able to SSH into the switches, ideally by key without having to enter a password/passphrase. Using an SSH agent is strongly recommended!

### Generating the offline configuration

If it's not possible to connect to the switch via SSH, for example to deploy an initial configuration, it's possible to generate a configuration file that can be copied to the device using a USB stick.
This can also be applied to the provided example configuration to get an idea of ​​the configuration that will be applied to the switches.

The configuration files will be generated in the folder specified by the `ex_config_save_dir` variable, defined in `group_vars/juniper/ansible.yml`. In the provided example, this will be in the `$HOME/.juniper-configs-example/` folder.

```bash
# Generate offline configuration for all switches in the inventory
ansible-playbook -i network insa_strasbourg.juniper.ex_config --tags offline

# Generating offline configuration for switches that are members of the juniper_datacenter group
ansible-playbook -i network -l juniper_datacenter insa_strasbourg.juniper.ex_config --tags offline

# Generate offline configuration for specified switches
ansible-playbook -i network -l distribution-003,distribution-004 insa_strasbourg.juniper.ex_config --tags offline
```
