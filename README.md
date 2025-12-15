Docker Ansible Role
=========

This role is to install docker and docker compose.

* Ubuntu 
* Debian

Requirements
------------
  
ansible 2.10+  - Recommended >= 9.0   



molecule / vagrant
------------ 
Vagrant:  
https://computingforgeeks.com/install-latest-vagrant-on-ubuntu/  

Molecule:  
https://philnewm.github.io/molecule-start/  


apt:  
- python3-dev  
- libvirt-daemon-system  
- libvirt-clients  
- libvirt-dev  
- pkg-config  
  
pip:  
- python-vagrant  
- testinfra  
- libvirt-python  
- molecule  
- molecule-plugins  
- rich  

vagrant:  
- vagrant plugin install vagrant-libvirt

Role Variables
--------------

See defaults!  


Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

```yaml
- name: Deploy docker
  hosts: all
  gather_facts: true # Disable if your role does not rely on facts
  become: true
  roles:
    - name: balazspetik.ansible-docker
  # or by tasks
  tasks:    
    - name: Deploy docker
      ansible.builtin.include_role:
        name: balazspetik.ansible-docker

```
License
-------

MIT

Author Information
------------------

Balázs Petik
DecOps Engineer
