# -*- mode: ruby -*-
# vi: set ft=ruby :

#############################
# Provisioning Script
#############################

$ansible_script = <<SCRIPT
#!/bin/bash

sudo apt-get update >/dev/null
sudo apt-get -y upgrade

sudo apt-get install -y software-properties-common
sudo apt-add-repository -y ppa:ansible/ansible
sudo apt-get update >/dev/null
sudo apt-get install -y ansible

if [[ ! -d /home/ansible/.ssh ]]; then 
  sudo useradd ansible
  mkdir -p /home/ansible/.ssh
fi

cp /vagrant/ssh/AnsibleSSHKey /home/ansible/.ssh/
touch /home/ansible/.ssh/config
sudo chown -R ansible:ansible /home/ansible
sudo chmod 700 /home/ansible/.ssh
sudo chmod 600 /home/ansible/.ssh/*
if ! grep --silent '192.168.33.10' /home/ansible/.ssh/config ; then
cat << __SSHCONFIG__ >> /home/ansible/.ssh/config
Host client
  HostName 192.168.33.10
  User vagrant
  Port 22
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile ~/.ssh/AnsibleSSHKey
  IdentitiesOnly yes
  LogLevel FATAL
__SSHCONFIG__
fi

SCRIPT

$client_script = <<SCRIPT
#!/bin/bash

sudo apt-get update >/dev/null
sudo apt-get -y upgrade

cat /vagrant/ssh/AnsibleSSHKey.pub >> /home/vagrant/.ssh/authorized_keys

SCRIPT

#############################
# Vagrantfile
#############################
Vagrant.configure(2) do |config|
 
  # Box
  config.vm.box_url = "https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"
  config.vm.box = "ubuntu14.04"

  ############################
  # ansible node
  ############################
  config.vm.define "ansible-node" do |node|
    
    # SSH Config
    node.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"

    # Network Config
    node.vm.network "private_network", ip: "192.168.33.11", virtualbox__intnet: "intnet"

    # VM Config
    node.vm.provider "virtualbox" do |vb|
      vb.name   = "ansible"
      vb.memory = 521
      vb.cpus   = 1
    end
    
    # Synced_folder
    node.vm.synced_folder "playbooks", "/home/ansible/playbooks"

    # ansible set up
    node.vm.provision "shell", :inline => $ansible_script
  end

  ############################
  # client node
  ############################
  config.vm.define "client-node" do |node|

    # SSH Config
    node.ssh.forward_agent = true
    node.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"

    # Network
    node.vm.network "forwarded_port", guest: 8080, host: 8080
    node.vm.network "private_network", ip: "192.168.33.10", virtualbox__intnet: "intnet"

    # VM Config
    node.vm.provider "virtualbox" do |vb|
      vb.name   = "client"
      vb.memory = 2024
      vb.cpus   = 2
    end
    
    # client set up
    node.vm.provision "shell", :inline => $client_script
  end
end
