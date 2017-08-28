from: https://github.com/Uberspace/ansible-mountopts

An Ansible module which allows to easily add, remove or set mount options in /etc/fstab.  The original mount-module in Ansible only allows setting the whole mount option list. This can be troublesome when editing mount options of a partly-automated remote or when editing options from multiple roles.

This module depends on the fstab python-module, which needs to be installed separately on the remote machine:

```
- name: install dependencies for mountopts-module
  pip: name=fstab
```

example use:

```
# Set the 'noatime' for /home
- mountopts:
    name: /home
    option: noatime
    state: present
```
