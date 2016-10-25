# -*- mode: ruby -*-
# vi: set ft=ruby :
ENV['VAGRANT_DEFAULT_PROVIDER'] = 'virtualbox'
#ENV['VAGRANT_DEFAULT_PROVIDER'] = 'vmware_appcatalyst'
#ENV['VAGRANT_DEFAULT_PROVIDER'] = 'vmware_desktop'
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

  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  
  config.vm.box = "boxcutter/ubuntu1404"
# If you need 32bit version comment out the line above and remove comment below
#  config.vm.box = "boxcutter/ubuntu1404-i386"

  config.vm.boot_timeout = 600

$keys = <<SCRIPT
openssl rsa -in /vagrant/salt/minion.pem -pubout > /etc/salt/pki/minion/minion.pub
cp -f /vagrant/salt/master_sign.pub /etc/salt/pki/minion
SCRIPT

$grains = <<SCRIPT
salt-call grains.setval salt_id $(awk -F ":" '/id/ {print $2}' /etc/salt/minion.d/id.conf)
salt-call grains.setval append_domain $(awk -F ":" '/append_domain/ {print $2}' /etc/salt/minion.d/id.conf)
SCRIPT


  # View the documentation for the provider you are using for more
  # information on available options.
config.vm.box_version = "2.0.22"

  config.vm.provision "shell", inline: <<-SHELL
  apt-get update -qq
  apt-get install git zip unzip pwgen -y
  adduser --disabled-password --gecos "" virl || true 
  git clone --depth 1 https://github.com/snergster/virl-salt.git /srv/salt || true
#  apt-get dist-upgrade -y
  mkdir -p /etc/salt/pki/minion
  mkdir -p /etc/salt/minion.d
  cp -f /vagrant/salt/minion /etc/salt/minion
  chmod go+w /etc/salt
  chmod go+w /etc/salt/minion
  cp -f /vagrant/id.conf /etc/salt/minion.d/id.conf
  cp -f /vagrant/salt/extra.conf /etc/salt/minion.d/extra.conf
  cp -f /vagrant/salt/minion.pem /etc/salt/pki/minion/minion.pem
  SHELL

  
    config.vm.provision :salt do |salt|

      salt.masterless = false
      salt.minion_config = "salt/minion"
      salt.bootstrap_options = "-P"
      salt.version = "2015.8"
      salt.install_type = "stable"
# If you need 32bit version comment out the line above and remove comment below
#      salt.install_type = "git"
      salt.minion_key = "salt/minion.pem"
      salt.minion_pub = "salt/minion.pub"
      salt.run_highstate = false

    end

  config.vm.provision "shell", inline: $keys
  config.vm.provision "shell", inline: $grains

  config.vm.provision "shell", privileged: false, inline: <<-SHELL
  sudo salt-call state.sls virl.terraform.install
  sudo salt-call state.sls virl.terraform.virl_packet_vagrant
  sudo salt-call state.sls virl.terraform.virl_cluster_vagrant
  /usr/local/bin/noprompt-ssh-keygen
  cp -f /vagrant/settings.tf /home/vagrant/virl_packet/settings.tf
  cp -f /vagrant/settings.tf /home/vagrant/virl_cluster/settings.tf
  #cd /home/vagrant/virl_packet; terraform apply .
  #cd /home/vagrant/virl_cluster; terraform apply .
  SHELL
end
