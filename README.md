# Auto Host Config - Remote Host Version
### Rapid application of settings, configuration, updates and app installations on remote Linux hosts
[![lang](https://img.shields.io/badge/language-ansible-3572A5.svg?style=flat-square)](https://github.com/robertpeteuil/auto-host-config)
[![status](https://img.shields.io/badge/status-stable-brightgreen.svg?style=flat-square)](https://github.com/robertpeteuil/auto-host-config)

---

This is an automated process to rapidly configure Linux hosts - this is the Remote Host Version.  There are two versions, each with their own git branch.  Make sure  you download the correct one:
- Remote: for configuring a host from a separate control computer.  ex: configure a notebook from your desktop.
- Local: for configuring a host directly.  ex: configure a notebook directly from that notebook.

This process supports across a variety of device types, including: notebooks, desktops, servers, cloud hosts, and embedded devices.  The initial release supports the configuration of Ubuntu-based hosts, with Redhat/Centos and Windows support planned.

This is implemented using a free, open-source utility called Ansible which runs on Windows, Mac and Linux.  Use of this process does not require previous knowledge of Ansible.

### Requirements
The requirements are minimal:
- The "remote-host" to be configured (referred to as "target host" below)
  - SSH server installed and running (instructions below)
  - A user account that allows SSH access and sudo capabilities
- A second computer to use as the "control computer"
  - If you want to configure it directly, switch to the "local" branch of this repo.
  - Ansible and the repo files are installed on the "control computer"
    - Ansible version 2.4.0.0 or later is required.

### Setup

**Ensure SSH is installed on target (if required)**
- If you're installing Linux on a local machine or notebook, you may need to install it.
  - Typing the following command into the terminal on the target will install it: `sudo apt-get install openssh-server`
- On cloud instances/VMs, SSH is installed & running by default.

**Install Ansible on the main computer**

Install Ansible via pip, package manager, or git
- If you have Python and pip installed you can type `pip install ansible`
- on linux, type `sudo apt-get install ansible`
- on a mac with brew installed, type `brew install ansible`

**Clone this repo to your main computer**

Create a directory, download the repo into it and `cd` to it with the following commands:

```
mkdir host-config
git clone https://github.com/robertpeteuil/auto-host-config host-config
cd host-config
```

Note: you can replace `host-config` with a different folder-name, just make sure to use the same name in all three commands.

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
- Run an alternate playbook if you have certificate-authentication and passwordless sudo configured (this runs without a password prompt):
  - `./main-sudo.yml`
- Run a playbook that applies only the notebook and mac related hardware fixes (you will be prompted for a password):
  - `./mac-fixes-only.yml`


### Playbooks, Roles and Settings

**Playbooks**
- `main.yml` - the primary method of execution.  It prompts for the target host password and executes all three roles (described below).
- `main-sudo.yml` - this is only for users who have configured the host for Ansible use (by running main.yml with ssh_pub_key_path pointing to their SSH certificate, and set_pwless_sudo set to True).
- `mac-fixes-only.yml` - for users who only want to apply the hardware adjustments for Linux on a notebook or Mac.

**Roles**
- `config-linux-hw`: current HW adjustments are for linux installs on notebooks and macs
- `config-linux-base`: installs/removes apt packages and installs/updates python modules
- `config-linux-sys`: configure SSH security, enable no-spoof, install fail2ban and (optionally) set user account for Ansible use

**Settings**
- The host inventory file is the file named `inventory`
- Configuration settings are consolidated in the file `config.yml`
- Settings also exist in the default folder for each role
  - Advanced Ansible users can use settings files within each role by removing the `vars_files` section from each playbooks


### Acknowledgments

This playbook includes the [mountopts module](https://github.com/Uberspace/ansible-mountopts) by Uberspace in the library directory.  It's only used if the target computer is a notebook and the primary drive is an SSD.
