# ansible
Some modifications for tests!
for Ubuntu use apt
for CentOS use dnf
# vim - find a string and replace it
:%s/apt/dnf/g
# apache on CentOS
# just run
ansible-playbook --ask-become-pass install_apache_for_CentOS_fixed.yml --limit target-3 
