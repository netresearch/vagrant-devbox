# -*- mode: ruby -*-
# vi: set ft=ruby :

# vagrant-devbox
#
# LICENSE:    MIT
#
# @project    vagrant-devbox
# @package    vagrant
# @author     André Lademann <vergissberlin@googlemail.com>
# @author     André Lademann <andre.Lademann@netresearch.de>
# @author     Norman Bestfleisch <norman.bestfleisch@netresearch.de>
# @author     Norman Walter <norman.walter@netresearch.de>
# @license    http://opensource.org/licenses/MIT
# @link       http://blog.scottlowe.org/2015/02/10/using-docker-with-vagrant/
# @link       https://www.jeffgeerling.com/blogs/jeff-geerling/vagrant-nfs-shared-folders

# Requirements
VAGRANTFILE_API_VERSION = "2"
VAGRANT_DEFAULT_PROVIDER = "virtualbox"
Vagrant.require_version ">= 1.6.5"

# Plugins
# VirtualBox Guest plugin
unless Vagrant.has_plugin?("vagrant-disksize")
  system("vagrant plugin install vagrant-disksize")
  puts "Dependencies installed, please try the command again."
  exit
end
unless Vagrant.has_plugin?("vagrant-vbguest")
  system("vagrant plugin install vagrant-vbguest")
  puts "Dependencies installed, please try the command again."
  exit
end

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Assign a friendly name to this host VM and box
  config.vm.hostname   = "vagrant-devbox"
  config.vm.box        = "vagrant-devbox"
  config.disksize.size = "20GB"


  # Every Vagrant development environment requires a box.
  config.vm.box = "debian/contrib-stretch64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  config.vm.box_check_update = true

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network :forwarded_port, guest: 80, host: 8080, auto_correct: true
  config.vm.network :forwarded_port, guest: 22, host: 2222, auto_correct: true, id: 'ssh'

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  config.vm.network "private_network", type: "dhcp"
  config.vm.network "public_network",  type: "dhcp"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  if Vagrant::Util::Platform.windows? then
    config.vm.synced_folder "./data", "/var/data", :create => true
  else
    config.vm.synced_folder "./data", "/var/data", type: "nfs", :create => true
  end

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    vb.name = "vagrant-devbox"
    # Display the VirtualBox GUI when booting the machine
    # vb.gui = true

    # Customize the amount of memory on the VM:
    vb.cpus = 4
    vb.memory = 8192
  end

  config.vm.provider "vmware_fusion" do |vm|
    vm.name = "vagrant-devbox"

    # Display the vmware GUI when booting the machine
    # vm.gui = true

    # Customize the amount of memory on the VM:
    vm.vmx["memsize"] = 8192
    vm.vmx["numvcpus"] = 4
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
  config.vm.provision "file", source: "./provision/files/config/home/vagrant/.bashrc", destination: ".bashrc"
  config.vm.provision "file", source: "./provision/files/config/home/vagrant/.bash_aliases", destination: ".bash_aliases"
  config.vm.provision "file", source: "./provision/files/config/home/vagrant/.vimrc", destination: ".vimrc"

  if Vagrant::Util::Platform.windows? then
      config.vm.provision "file", source: ENV["USERPROFILE"] +"/.ssh", destination: ".ssh"
  else
      config.vm.provision "file", source: "~/.ssh", destination: ".ssh"
  end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "ansible_local" do |ansible|
    ansible.install            = true
    ansible.install_mode       = "pip"
    ansible.compatibility_mode = "auto"
    ansible.playbook           = "provision/playbook.yml"
    ansible.become             = true
    ansible.galaxy_role_file   = "provision/requirements.yml"
    ansible.galaxy_roles_path  = "provision/roles/"
    # ansible.tags               = "ruby"
  end

  config.ssh.forward_agent    = false
  config.ssh.insert_key       = true
  config.vm.provision :docker
  config.vm.provision :docker_compose

end
