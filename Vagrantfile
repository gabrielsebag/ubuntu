# encoding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANT_BOX = 'bento/ubuntu-20.04'
# Memorable name for your box
VM_NAME = 'ubuntu'
# VM User — 'vagrant' by default
VM_USER = 'gabrielsebag'
# Username on your Mac
MAC_USER = 'gabrielsebag'
# Host folder to sync
HOST_PATH = '/Users/gabrielsebag/Code'
# Where to sync to on Guest — 'vagrant' is the default user name
GUEST_PATH = '/home/vagrant'
# # VM Port — uncomment this to use NAT instead of DHCP
# VM_PORT = 8080
Vagrant.configure(2) do |config|
  # Vagrant box from Hashicorp
  config.vm.box = VAGRANT_BOX
  # Actual machine name
  config.vm.hostname = VM_NAME
  # Set VM name in Virtualbox
  config.vm.provider "virtualbox" do |v|
    v.name = VM_NAME
    v.memory = 4096
    v.cpus = 2
  end
  #DHCP — comment this out if planning on using NAT instead
  #config.vm.network "private_network", type: "dhcp"
  config.vm.network :forwarded_port, guest: 80, host: 80
  # # Port forwarding — uncomment this to use NAT instead of DHCP
  # config.vm.network "forwarded_port", guest: 80, host: VM_PORT
  # Sync folder
  config.vm.synced_folder HOST_PATH, GUEST_PATH
  # Disable default Vagrant folder, use a unique path per project
  config.vm.synced_folder '.', '/home/'+VM_USER+'', disabled: true
  #Install package for your VM
  config.vm.provision "shell", inline: <<-SHELL
    # DOCKER
    # ------------------------------------------------------------------------------
    sudo apt-get update -y
    sudo apt-get install -y \
      apt-transport-https \
      ca-certificates \
      curl \
      software-properties-common
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository \
     "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) \
     stable"

    sudo apt-get update -y
    sudo apt-get install -y docker-ce
    sudo apt-get install -y docker-compose
    sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose
    sudo chmod +x /usr/bin/docker-compose

    # MAKE
    # ------------------------------------------------------------------------------
    sudo apt-get install -y make

    # NODE
    # ------------------------------------------------------------------------------
    sudo curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -
    sudo apt-get update -y
    sudo apt-get install -y nodejs
  SHELL
end