# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "boxcutter/ubuntu1404"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

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
#  config.vm.synced_folder ".", "/home/vagrant/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #

$keys = <<SCRIPT
cp -f /vagrant/salt/minion.pem /etc/salt/pki/minion.pem
openssl rsa -in /vagrant/salt/minion.pem -pubout >> /etc/salt/pki/minion/minion.pub
cp -f /vagrant/salt/master_sign.pub /etc/salt/pki/minion
SCRIPT


  # View the documentation for the provider you are using for more
  # information on available options.
  config.vm.provision "shell", inline: <<-SHELL
  apt-get update -qq
  apt-get install git zip unzip -y
  adduser --disabled-password --gecos "" virl || true 
  git clone https://github.com/snergster/virl-salt.git /srv/salt || true
#  apt-get dist-upgrade -y
  mkdir -p /etc/salt/pki/minion
  mkdir -p /etc/salt/minion.d
  cp -f /vagrant/salt/minion /etc/salt/minion
  chmod go+w /etc/salt
  chmod go+w /etc/salt/minion
  cp -f /vagrant/extra.conf /etc/salt/minion.d/box.conf
  SHELL

  config.vm.provision "shell", inline: $keys
  
    config.vm.provision :salt do |salt|

      salt.masterless = false
      salt.minion_config = "salt/minion"
      salt.run_highstate = true
      salt.bootstrap_options = "-P"
      salt.minion_key = "salt/minion.pem"
      salt.minion_pub = "salt/minion.pub"
      salt.run_highstate = false

    end

  config.vm.provision "shell", privileged: false, inline: <<-SHELL
  sudo salt-call state.sls virl.terraform.install
  sudo salt-call state.sls virl.terraform.virl_packet_vagrant
  sudo salt-call state.sls virl.terraform.virl_cluster_vagrant
  /usr/local/bin/noprompt-ssh-keygen
  SHELL
end
