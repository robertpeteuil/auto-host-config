# Auto Host Config
### Rapid applicaton of settings, configuration, package updates and installation on Linux hosts
[![lang](https://img.shields.io/badge/language-ansible-3572A5.svg?style=flat-square)](https://github.com/robertpeteuil/auto-host-config)
[![status](https://img.shields.io/badge/status-stable-brightgreen.svg?style=flat-square)](https://github.com/robertpeteuil/auto-host-config)

---

This is an automated process to rapidly configure Linux hosts.  It supports across a variety of device types, including: notebooks, desktops, servers, cloud hosts, and embedded devices.  The initial release supports the configuration of Ubuntu-based hosts, with Redhat/Centos and Windows support planned.

This branch (local) is for use when you want to run this directly on the new host. Because everything is done locally the setup required is dramatically simplified. 

If you want to configure hosts remotely, or multiple hosts in parallel you need to use the (remote) branch. 

### Requirements
- A host running a Ubuntu or other Debian-based distro

### Setup
**Install Ansible on the main computer**

Install Ansible via pip, package manager, or git
- If you have Python and pip installed you can type `pip install ansible`
- on linux, type `sudo apt-get install ansible`
- on a mac with brew installed, type `brew install ansible`

**Clone this repo to**

Create a directory, download the repo into it and `cd` to it with the following commands:

```
mkdir host-config
git clone https://github.com/robertpeteuil/auto-host-config host-config
cd host-config
git checkout local
```

Note: you can replace `host-config` with a different folder-name, just make sure to use the same name in all three lines.


**Adjust settings in "config.yml"**
open the `config.yml` file with your editor.
- Review and adjust options for `set_pwless_sudo` and `ssh_disable_pw_logon` to your liking
  - By default, all options are set to False
- Any of the package lists below may be commented out as necessary
- System package lists:
  - `install_packages` - specifies packages to install via the package manager
  - `remove_packages` - specifies packages to remove
- Python package lists
  - `python_upgrade_user` - libraries to upgrade to the latest version in the user directory
  - `python_install_user` - libraries to install in the user context directory
  - `python_install_sys` - libraries to install for the entire system
  - `python_upgrade_sys` - libraries to upgrade to the latest version for the entire system

**Select and run a playbook to configure target host(s)**
- Primary playbook (you will be prompted for the password):
  - `./main.yml`
- Playbook that applies only the notebook and mac related hardware fixes (you will be prompted for a password):
  - `./mac-fixes-only.yml`


### Playbooks, Roles and Settings

**Playbooks**
- `main.yml` - the primary method of execution.  It prompts for the target host password and executes all three roles (described below).
- `mac-fixes-only.yml` - for users who only want to apply the hardware adjustments for Linux on a notebook or Mac.

**Roles**
- `hardware`: current HW adjustments are for linux installs on notebooks and macs
- `install-base`: installs/removes apt packages and installs/updates python modules
- `config-sys`: configure SSH security, enable no-spoof, install fail2ban and (optionally) set user account for Ansible use

**Settings**
- Configuration settings are consolidated in the file `config.yml`
- Settings also exist in the default folder for each role
  - Advanced Ansible users can remove the `vars_files` section from the playbooks and use the settings files within each role.


### Acknowledgments

This playbook includes the [mountopts module](https://github.com/Uberspace/ansible-mountopts) by Uberspace in the library directory.  It's only used if the target computer is a notebook and the primary drive is an SSD.
