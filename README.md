# Ansible Linux Config Playbook
### Enables rapidly applying hardware settings, security config and installing packages
[![lang](https://img.shields.io/badge/language-ansible-3572A5.svg?style=flat-square)](https://github.com/robertpeteuil/ansible-linux-config)
[![status](https://img.shields.io/badge/status-stable-brightgreen.svg?style=flat-square)](https://github.com/robertpeteuil/ansible-linux-config)

---

This playbook currently works with any Debian/Ubuntu based linux distro.  It was originally created for configuring new notebook installs, but it functions on servers and cloud hosts as well.

It is designed to be ran from a "control computer" and configures the target-host over a network connection.  In order to achive this, Ansible needs to be installed on the "control computer" and SSH needs to be installed and running on the target host.  Detailed directions are below.

There are three playbooks included (note: each playbook is executable, so it can be ran directly):
- main.yml - the default method of execution; prompts for the password for the remote host and executes all three roles
- main-sudo.yml - for users who have SSH certificate authentication and sudoless password setup on the host.
- mac-fixes-only.yml - for users who have installed linux on a Mac and only want to apply hardware configurations.

This repo includes three roles:
- hardware: current HW adjustments are for linux installs on notebooks and macs
- install-base: installs/removes apt packages and installs/updates python modules 
- config-sys: configure SSH security, enable nospoof, install fail2ban and (optionally) set user account for ansible use

Configuration of Settings:
- The host inventory file is the file named 'inventory'
- Configuration settings are consolidated in the file 'config.yml' in the playbook directory
- Settings also exist in the default folder for each role
  - Advanced Ansible users can remove the `vars_files` section from the playbooks and use settings in each role.


### Setup SSH on new host (if required)
Most cloud instances/VMs pre-configure SSH by default.
But, if you're installing Linux on a local machine or notebook, you may need to install it.
Fortunately, it's easily installed by typing this into the new computer's terminal: `sudo apt-get install openssh-server`

### Install Ansible on your main computer
Install Ansible on your computer via pip, package manager, or git
 - If you have Python and pip installed you can type `pip install ansible`
 - on linux, type `sudo apt-get install ansible`
 - on a mac with brew installed, type `brew install ansible`

### Clone this repo to your main computer
Create a directory for the repo: `mkdir host-setup`
Then, clone this repo into that directory: `git clone https://github.com/robertpeteuil/ansible-linux-config host-setup`
Finally, cd into the repo directory: `cd host-setup`

### Rename example Inventory & Config files
Next, the example inventory and configuration files need to be renamed to the names the playbook uses.  To make this easy, I've included a simple script that does this, which can be ran with the command: `./rename-examples.sh`

### Adjust "inventory" file
- open the `inventory` file with your editor
- replace `hostname.local` with the IP address or hostname of the target host
- if the username on the target host is different than the username your main computer
  - change `username` in the parameter `ansible_user=username` to match the username for the target host

### Adjust settings in "config.yml"
- open the `config.yml` file with your editor
- review, edit or change the public ssh-key that will be made an authorized_user on the target host
  - you can disable this by commenting-out or deleting the line
- Change the options for rebooting, enabling password-less sudo and disabling ssh logons with passwords as necessary.
  - By default, only reboot is set to true, the others are set to false.
- Edit the list of Packages to be installed and removed via the package manager
- Edit the list of Python packages to install

## Run the playbook to configure the Target Machine
To run the full configuration and base-package install:
`./main.yml`

To run only the mac related hardware fixes, run the command:
`./mac-fixes-only.yml`

To run the full configuration without the password prompt requires that SSH cert-authentication and sudoless password are setup.  If so, you can run the command:
`./main-sudo.yml`
