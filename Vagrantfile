# -*- mode: ruby -*-
# vi: set ft=ruby :

#############################
# Provisioning Script
#############################

$ansible_script = <<SCRIPT
#!/bin/bash

sudo apt-get update
sudo apt-get -y upgrade

sudo apt-get install -y software-properties-common
sudo apt-add-repository -y ppa:ansible/ansible
sudo apt-get update
sudo apt-get install -y ansible

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
    node.vm.network "private_network", ip: "192.168.33.11"

    # VM Config
    node.vm.provider "virtualbox" do |vb|
      vb.name   = "ansible"
      vb.memory = 521
      vb.cpus   = 1
    end

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
    node.vm.network "private_network", ip: "192.168.33.10"

    # Synced_folder
    node.vm.synced_folder ".", "/home/vagrant/"

    # VM Config
    node.vm.provider "virtualbox" do |vb|
      vb.name   = "client"
      vb.memory = 2024
      vb.cpus   = 2
    end
  end
end
