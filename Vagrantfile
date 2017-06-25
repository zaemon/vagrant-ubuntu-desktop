# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box_check_update = true
  config.vm.box = "bento/ubuntu-16.04"
  config.vm.network "private_network", ip: "192.168.33.2"
  config.vm.network "forwarded_port", guest: 22, host: 2022, id:"ssh"
  config.vm.provider :virtualbox do |vb|
    vb.gui = true
    vb.customize ["modifyvm", :id, "--memory", "4096"]
    vb.customize ["modifyvm", :id, "--cpus", "4"]
    vb.customize ["modifyvm", :id, "--graphicscontroller", "vboxvga"]
    vb.customize ["modifyvm", :id, "--accelerate3d", "on"]
    vb.customize ["modifyvm", :id, "--ioapic", "on"]
    vb.customize ["modifyvm", :id, "--vram", "256"]
    vb.customize ["modifyvm", :id, "--hwvirtex", "on"]
    vb.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
  end
  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "provision/ubuntu-desktop.yml"
    ansible.inventory_path = 'inventory'
    ansible.limit = 'all'
    ansible.verbose = true
    ansible.install = true
  end
end
