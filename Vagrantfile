#!/bin/bash

# vagrant-devbox
#
# LICENSE:    MIT
#
# @project    vagrant-devbox
# @package    vagrant
# @author     André Lademann <vergissberlin@googlemail.com>
# @license    http://opensource.org/licenses/MIT
# @link http://blog.scottlowe.org/2015/02/10/using-docker-with-vagrant/


# Plugins
# vagrant plugin install vagrant-docker-compose
unless Vagrant.has_plugin?("vagrant-hosts")
  system("vagrant plugin install vagrant-hosts")
  puts "Dependencies installed, please try the command again."
  exit
end
unless Vagrant.has_plugin?("vagrant-vbox-snapshot")
  system("vagrant plugin install vagrant-vbox-snapshot")
  puts "Dependencies installed, please try the command again."
  exit
end
unless Vagrant.has_plugin?("vagrant-docker-compose")
  system("vagrant plugin install vagrant-docker-compose")
  puts "Dependencies installed, please try the command again."
  exit
end
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Assign a friendly name to this host VM
  config.vm.hostname = "vagrant-devbox"

  # Every Vagrant development environment requires a box.
  config.vm.box = "ubuntu/xenial64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 2375, host: 2375  # docker

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "./data", "/vagrant_data",
    type: "nfs"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
    # Customize the amount of memory on the VM:
    vb.cpus = 4
    vb.memory = 8192
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  config.vm.provision "file", source: "./config/.bashrc", destination: ".bashrc"
  config.vm.provision "file", source: "./config/.bash_aliases", destination: ".bash_aliases"
  config.vm.provision "file", source: "./config/.vimrc", destination: ".vimrc"

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y \
      curl \
      git \
      language-pack-UTF-8 \
      make \
      mariadb-client \
      mc \
      python-pip \
      vim \
      wget

      update-alternatives --set editor /usr/bin/vim.basic

      . /vagrant/install/bashlight.sh
      . /vagrant/install/composer.sh
      . /vagrant/install/docker-compose.sh
      . /vagrant/config/structure.sh
      chown -R vagrant:vagrant /home/vagrant/
  SHELL

  # Always use Vagrant's default insecure key
  config.ssh.forward_agent    = true
  config.ssh.insert_key       = true

  config.vm.provision :docker
  config.vm.provision :docker_compose

end
