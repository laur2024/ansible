# ansible
Some modifications for tests!
for Ubuntu use apt
for CentOS use dnf
# vim - find a string and replace it
:%s/apt/dnf/g
# apache on CentOS
# just run
ansible-playbook --ask-become-pass install_apache_for_CentOS_fixed.yml --limit target-3


#### Ansible variables ####

 
You can use set_fact to dynamically set the variables for each host within the playbook. This ensures that the correct variables are used for each host based on its hostname.

Updated Inventory File
Ensure the inventory file lists the hosts correctly:

[target_servers]
target-1
target-2
target-3


Group Variables File
You can simplify group_vars/target_servers.yml by only defining the common variables:

group_vars/target_servers.yml:

common_apache_package: apache2
common_php_package: libapache2-mod-php

host_vars:
  target-1:
    apache_package: apache2
    php_package: libapache2-mod-php
  target-2:
    apache_package: apache2
    php_package: libapache2-mod-php
  target-3:
    apache_package: httpd
    php_package: php

Playbook File
Update the playbook to use the set_fact module to set the correct variables for each host:

---
- name: Install Apache and PHP support packages
  hosts: all
  become: true
  tasks:
    - name: Set apache and php package variables based on hostname
      set_fact:
        apache_package: "{{ host_vars[inventory_hostname].apache_package | default(common_apache_package) }}"
        php_package: "{{ host_vars[inventory_hostname].php_package | default(common_php_package) }}"

    - name: Install Apache and PHP support packages
      package:
        name:
          - "{{ apache_package }}"
          - "{{ php_package }}"
        state: latest
        update_cache: yes

Directory Structure
Your directory structure should look like this:

.
├── ansible.cfg
├── inventory
├── group_vars
│   └── target_servers.yml
└── install_apache_and_php.yml


Running the Playbook
Make sure your ansible.cfg is correctly configured to find the inventory file. Example ansible.cfg:

[defaults]
inventory = ./inventory

Ex:
ansible-playbook -i ~/ansible/inventory --ask-become-pass install_apache_and_php.yml --limit target-1,target-3
