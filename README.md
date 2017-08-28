# Ansible Linux Config Playbook
## Enables rapid hardware config, applying security settings, and package/library installation


Although originally designed for configuring new notebook builds, it works on servers and cloud hosts just as well.

multiple hosts, they can be configured simultaneously.


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
Then, clone this repo into that directory: `git clone path host-setup`
Finally, cd into the repo directory: `cd host-setup`

### Rename example Inventory & Config files
run the command: `./rename-examples.sh`

### Adjust "inventory" file
- replace `hostname.local` with the IP or hostname of the target host
- if the username on the target host differs from the username your main computer
  - set the target hosts `username` in the parameter `ansible_user=username`

### Adjust settings in "config.yml"
- delete or change the public ssh-key that will be made an authorized_user on the target host
  - if you don't want any key copied, comment-out or delete the line
- Change the options for rebooting, enabling password-less sudo and disabling ssh logons with passwords as necessary.
  - By default, only reboot is set to true, the others are set to false.
- Edit the list of Packages to be installed and removed via the package manager
- Edit the list of Python packages to install

## Configure remote machine
To run the full configuration and base-package install:
`./main.yml`

To run only the mac related hardware fixes, run the command:
`./mac-fixes-only.yml`
