# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box_check_update = true
  config.vm.box = "bento/ubuntu-16.04"
  config.vm.network "private_network", ip: "192.168.33.2"
  config.vm.network "forwarded_port", guest: 22, host: 2022, id:"ssh"
  config.vm.provider :virtualbox do |vb|
    vb.gui = true
    vb.customize [
      "modifyvm", :id,
      "--memory", "4096",
      "--acpi", "on",
      "--vram", "256",
      "--ioapic", "on",
      "--hpet", "on",
      "--apic", "on",
      "--x2apic", "on",
      "--hwvirtex", "on",
      "--cpus", "4",
      "--graphicscontroller", "vboxvga",
      "--accelerate3d", "on",
      "--clipboard", "bidirectional",
    ]
  end
  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "provision/ubuntu-desktop.yml"
    ansible.inventory_path = 'inventory'
    ansible.limit = 'all'
    ansible.verbose = true
    ansible.install = true
  end
end
