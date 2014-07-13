# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = "ubuntu1204-nova-client"

  config.vm.box_url = "http://dl.dropbox.com/u/4031118/Vagrant/ubuntu-12.04.1-server-i686-virtual.box"

  # config.vm.network :forwarded_port, guest: 80, host: 8080

  #config.vm.synced_folder "./vagrantdata", "/vagrantdata"
  config.vm.provider :virtualbox do |vb|
    vb.gui = false
  end
  
  config.vm.provision :shell, :inline => "apt-get update"
  config.vm.provision :shell, :inline => "aptitude install -y python-pip"
  config.vm.provision :shell, :inline => "sudo pip install rackspace-novaclient"

end
