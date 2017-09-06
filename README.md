# Automating Linux Host Configuration
### Rapidly apply hardware settings, configuration details, and install/remove packages
[![lang](https://img.shields.io/badge/language-ansible-3572A5.svg?style=flat-square)](https://github.com/robertpeteuil/ansible-linux-config)
[![status](https://img.shields.io/badge/status-stable-brightgreen.svg?style=flat-square)](https://github.com/robertpeteuil/ansible-linux-config)

---

This automation process was created for configuring linux hosts, wether they're notebooks, servers or cloud hosts.  It currently runs on Debian/Ubuntu based hosts, but it can be adapted for use on Redhat/CEntOS by changing only a few files.

It's implemented using Ansible, a free open-source provisioning and management tool.  Ansible is very simple to install and remove.  In fact you can even install it, run this playbook and then uninstall it.

### Requirements
Since Ansible is an agent-less utility, the requirements are minimal:
- A computer to serve as the "control computer" that has Ansible installed.
- Target host(s) with an SSH Server installed and running.
- An account on each target-host with SSH access and sudo capabilities.

### Playbooks, Roles and Settings

**Playbooks**
- `main.yml` - the primary method of execution.  It prompts for the target host password and executes all three roles (described below).
- `main-sudo.yml` - this is only for users who have configured the host for Ansible use (by running main.yml with ssh_pub_key_path pointing to their SSH certificate, and set_pwless_sudo set to True).
- `mac-fixes-only.yml` - for users who only want to apply the hardware adjustments for Linux on a notebook or Mac.

**Roles**
- `hardware`: current HW adjustments are for linux installs on notebooks and macs
- `install-base`: installs/removes apt packages and installs/updates python modules
- `config-sys`: configure SSH security, enable no-spoof, install fail2ban and (optionally) set user account for Ansible use

**Settings**
- The host inventory file is the file named `inventory`
- Configuration settings are consolidated in the file `config.yml`
- Settings also exist in the default folder for each role
  - Advanced Ansible users can remove the `vars_files` section from the playbooks and use the settings files within each role.

### Setup Steps

**Setup SSH on target host(s) (if required)**
- Most cloud instances/VMs have SSH installed & running by default.
- If you're installing Linux on a local machine or notebook, you may need to install it.
  - It can be installed with by typing the following command into the terminal on the target host: `sudo apt-get install openssh-server`

**Install Ansible on the main computer**

Install Ansible via pip, package manager, or git
- If you have Python and pip installed you can type `pip install ansible`
- on linux, type `sudo apt-get install ansible`
- on a mac with brew installed, type `brew install ansible`

**Clone this repo to your main computer**

Create a directory, download the repo into it and `cd` to it with the following commands:

```
mkdir host-setup
git clone https://github.com/robertpeteuil/ansible-linux-config host-setup
cd host-setup
```

Note: you can replace `host-setup` with a different folder-name, just be sure its the same on all three commands.


**Rename example Inventory & Config files**
Create copies of the inventory and configuration files without the ".example" suffix
- The included script does this for you: `./rename-examples.sh`
- The files can also be copied or renamed manually

**Adjust "inventory" file**
Open the `inventory` file with your editor
- replace `hostname.local` with the IP address or hostname of the target host
- if the username on the target host is different than your main computer
  - add the parameter `ansible_user=username` after the target hostname / IP address
  - change `username` to match the username for the target host

**Adjust settings in "config.yml"**
open the `config.yml` file with your editor.
- `ssh_pub_key_path` specifies the public ssh-key that can be added as an authorized_user on the target host, review, edit, change or delete as necessary
  - you can disable this by commenting-out or deleting the line
- Review and adjust options for `reboot_after_fixes`, `set_pwless_sudo` and `ssh_disable_pw_logon` to your liking
  - By default, all options except `reboot_after_fixes` are set to False
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
- Run the primary playbook (you will be prompted for the password):
  - `./main.yml`
- Run an alternate playbook if you have certificate-authentication and password-less sudo configured (this runs without a password prompt):
  - `./main-sudo.yml`
- Run a playbook that applies only the notebook and mac related hardware fixes (you will be prompted for a password):
  - `./mac-fixes-only.yml`

### Acknowledgments

This playbook includes the [mounopts module](https://github.com/Uberspace/ansible-mountopts) by Uberspace in the library directory.  It's used when the target computer is a notebook and the primary drive is an SSD.
