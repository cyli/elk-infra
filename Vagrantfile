# -*- mode: ruby -*-
# vi: set ft=ruby :

# Note: only vagrant >= 1.4.0 supported, and this requires will fail if
# Vagrant < 1.4.0
Vagrant.require_version ">=1.4.0"


# What box are we picking ?
BOX_NAME = ENV['BOX_NAME'] || "precise64"
# The url from where the 'config.vm.box' box will be fetched if it
# doesn't already exist on the user's system.
BOX_URI = ENV['BOX_URI'] || "http://files.vagrantup.com/precise64.box"

Vagrant.configure("2") do |config|
  config.vm.box = BOX_NAME
  config.vm.box_url = BOX_URI

  # make sure docker is installed
  config.vm.provision "docker"
  config.vm.provision "shell", inline: "sudo apt-get install -q -y python-pip;"
  # set up the docker user, permissions, etc.
  config.vm.provision "ansible" do |ansible|
    ansible.sudo = true
    ansible.verbose = 'vvvv'
    ansible.playbook = "ansible/elk.yml"
  end

  config.vm.provider "virtualbox" do |vb, override|
    vb.name = "dockerbox"
    vb.memory = 1024
    vb.customize ["modifyvm", :id, "--cpuexecutioncap", "50"]
  end
end
