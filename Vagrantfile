# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version.
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.cache.auto_detect = false
  config.cache.enable :apt
  config.cache.scope = :machine

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
    ansible.limit = 'all'
    ansible.sudo = true
    ansible.extra_vars = { ansible_ssh_user: 'vagrant' }
  end

  config.vm.define "controller" do |server|
    server.vm.hostname = "controller"

    server.vm.box = "trusty64"

    %w(parallels virtualbox libvirt vmware_fusion).each do |provider|
      server.vm.provider provider do |config|
        config.memory = 2048
        config.cpus = 4
      end
    end
  end

  config.vm.define "network" do |server|
    server.vm.hostname = "network"

    server.vm.box = "trusty64"

    # Instance tunnels network (eth1)
    server.vm.network :private_network, ip: "192.168.129.5"

    # External (eth2)
    server.vm.network :public_network

  end

  config.vm.define "compute1" do |server|
    server.vm.hostname = "compute1"

    server.vm.box = "trusty64"

    # Instance tunnels network (eth1)
    server.vm.network :private_network, ip: "192.168.129.6"

    # Provider specific settings
    %w(parallels virtualbox libvirt vmware_fusion).each do |provider|
      server.vm.provider provider do |config|
        config.memory = 2048
        config.cpus = 4

        config.vmx['vhv.enable'] = 'TRUE' if provider == 'vmware_fusion'
        config.nested = true if provider == 'libvirt'
        config.customize [
          'set', :id, '--nested-virt', 'on'
        ] if provider == 'parallels'
      end
    end
  end

end
