# Ansible Linux Config Playbook
### Enables rapidly applying hardware settings, security config and installing packages
[![lang](https://img.shields.io/badge/language-ansible-3572A5.svg?style=flat-square)](https://github.com/robertpeteuil/ansible-linux-config)
[![status](https://img.shields.io/badge/status-stable-brightgreen.svg?style=flat-square)](https://github.com/robertpeteuil/ansible-linux-config)

---

This playbook currently works with any Debian/Ubuntu based linux distro.  It was  created for configuring fresh-installs on notebooks, but it can also be used with servers and cloud hosts without change.  

There are three playbooks, but most users will only use one.  They have been configured to be executed directly, which eliminates the need to enter the `ansible-playbook` command and required parameters.

### Requirements
Since Ansible is an agent-less utility, the requirements are minimal:
- A computer to serve as the "control computer" that has Ansible installed.
- Target host(s) with an SSH Server installed and running.
- An account on each target-host with SSH access and Sudo capabilities.

### Playbooks, Roles and Settings

**Playbooks**
- `main.yml` - the primary method of execution.  It prompts for the target host password and executes all three roles.
- `main-sudo.yml` - this can be used by users who have the account on the target host configured for SSH certificate authentication and password-less sudo.  With these in place, they do not have to type in the password each time it is ran.
- `mac-fixes-only.yml` - for users who have installed linux on a Mac and only want to apply the hardware adjustments.

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

Create a directory, download the repo into it and `cd` to it with the following commmands:

```
mkdir host-setup
git clone https://github.com/robertpeteuil/ansible-linux-config host-setup
cd host-setup
```

Note: you can replace `host-setup` with a different name, just be sure to change it on all three commands.


**Rename example Inventory & Config files**

Create inventory and configuration files without the ".example" suffix
- These can be copied or renamed manually
- or you can use the included scripts: `./rename-examples.sh`

**Adjust "inventory" file**
open the `inventory` file with your editor
- replace `hostname.local` with the IP address or hostname of the target host
- if the username on the target host is different than the username your main computer
  - change `username` in the parameter `ansible_user=username` to match the username for the target host

**Adjust settings in "config.yml"**
open the `config.yml` file with your editor.
- review, edit or change the public ssh-key that will be made an authorized_user on the target host.
  - you can disable this by commenting-out or deleting the line.
- Change the options for rebooting, enabling password-less sudo and disabling ssh-logon-via-password to your liking.
  - By default, all options except reboot are set to False.
- Edit the list of packages to  install and remove via the package manager.
- Edit the list of Python packages to install or update.
- You can comment out and list and it will not execute.

**Select and run a playbook to configure target host(s)**
- Run the primary playbook (you will be prompted for the password):
  - `./main.yml`
- Run an alternate playbook if you have certificate-authentication and password-less sudo configured (this runs without a password prompt):
  - `./main-sudo.yml`
- Run a playbook that applies only the mac related hardware fixes (you will be prompted for a password):
  - `./mac-fixes-only.yml`

### Acknowledgments

This playboook includes the [mounopts module](https://github.com/Uberspace/ansible-mountopts) written by Uberspace in the library directory.  It was included directly as there was no easy way to define a single file as a submodule.

Note: it's only used when the target computer is a notebook and the primary drive is an SSD.
