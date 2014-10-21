ansible-sample
==============

```
#login ansible-node
vagrant ssh ansible-node

# change user ansible
sudo su
su ansible

# ping client-node for ansible-playbook
cd /home/ansible/playbooks
ansible-playbook -i hosts ping.yml
```
