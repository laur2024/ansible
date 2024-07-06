Here is the formatted `README.md` content for GitHub:

```markdown
# Ansible

Some modifications for tests!

For Ubuntu, use `apt`.  
For CentOS, use `dnf`.

## vim - Find a string and replace it

```vim
:%s/apt/dnf/g
```

## Apache on CentOS

Just run:

```sh
ansible-playbook --ask-become-pass install_apache_for_CentOS_fixed.yml --limit target-3
```

## Ansible Variables

You can use `set_fact` to dynamically set the variables for each host within the playbook. This ensures that the correct variables are used for each host based on its hostname.

### Updated Inventory File

Ensure the inventory file lists the hosts correctly:

```ini
[target_servers]
target-1
target-2
target-3
```

### Group Variables File

You can simplify `group_vars/target_servers.yml` by only defining the common variables:

`group_vars/target_servers.yml`:

```yaml
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
```

### Playbook File

Update the playbook to use the `set_fact` module to set the correct variables for each host:

```yaml
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
```

### Directory Structure

Your directory structure should look like this:

```plaintext
.
├── ansible.cfg
├── inventory
├── group_vars
│   └── target_servers.yml
└── install_apache_and_php.yml
```

### Running the Playbook

Make sure your `ansible.cfg` is correctly configured to find the inventory file. Example `ansible.cfg`:

```ini
[defaults]
inventory = ./inventory
```

Example command:

```sh
ansible-playbook -i ~/ansible/inventory --ask-become-pass install_apache_and_php.yml --limit target-1,target-3
```
```

This formatting includes proper headings, code blocks, and consistent styling, making it easy to read and understand on GitHub.
