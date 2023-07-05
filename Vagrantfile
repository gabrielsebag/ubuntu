# encoding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANT_BOX = 'bento/ubuntu-20.04'
# Memorable name for your box
VM_NAME = 'ubuntu'
# VM User — 'vagrant' by default
VM_USER = 'vagrant'
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

  # Set VM disk size
  config.disksize.size = '100GB'

  #DHCP — comment this out if planning on using NAT instead
  #config.vm.network "private_network", type: "dhcp"
  config.vm.network :forwarded_port, guest: 80, host: 80
  # # Port forwarding — uncomment this to use NAT instead of DHCP
  # config.vm.network "forwarded_port", guest: 80, host: VM_PORT

  # Disable default Vagrant folder, use a unique path per project
  config.vm.synced_folder '.', '/home/'+VM_USER+'', disabled: true
  # Sync folder
  config.vm.synced_folder HOST_PATH, GUEST_PATH

  # Login to the guest path on ssh
  config.ssh.extra_args = ["-t", "cd "+GUEST_PATH+"; bash --login"]

  #Install package for your VM
  config.vm.provision "shell", inline: <<-SHELL
    # DISK SIZE
    # ------------------------------------------------------------------------------
    # https://medium.com/@kanrangsan/how-to-automatically-resize-virtual-box-disk-with-vagrant-9f0f48aa46b3
    sudo sgdisk /dev/sda -e
    sudo parted /dev/sda resizepart 3 100%
    sudo pvresize /dev/sda3
    sudo lvextend -r -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
    sudo resize2fs -p /dev/mapper/ubuntu--vg-ubuntu--lv

    # DOCKER
    # ------------------------------------------------------------------------------
    sudo apt-get update -y
    sudo apt-get install -y ca-certificates curl gnupg
    sudo install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    sudo chmod a+r /etc/apt/keyrings/docker.gpg
    echo \
      "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update -y
    DOCKER_VERSION=5:23.0.6-1~ubuntu.20.04~focal
    sudo apt-get install -y docker-ce=$DOCKER_VERSION docker-ce-cli=$DOCKER_VERSION containerd.io docker-buildx-plugin docker-compose-plugin
    sudo ln -s /usr/libexec/docker/cli-plugins/docker-compose /usr/bin/docker-compose
    sudo usermod -aG docker vagrant

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