# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-18.04"

  config.vm.box_check_update = true

  config.vm.synced_folder ".", "/Vagrant", mount_options: ["dmode=775,fmode=664"]
  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.cpus = ENV['VAGRANT_CPUS'] || 1
    vb.memory = ENV['VAGRANT_MEM'] || 1024

    vb.customize ['modifyvm', :id, '--clipboard', 'bidirectional']
    vb.customize ['modifyvm', :id, '--vram', '12']

    # for write QMK firmware
    vb.customize ['modifyvm', :id, '--usb', 'on']
    vb.customize ['usbfilter', 'add', '0',
                  '--target', :id,
                  '--name', 'Arduino LLC Arduino Micro [0100]',
                  '--vendorid', '0x2341',
                  '--productid', '0x8037']
    vb.customize ['usbfilter', 'add', '1',
                  '--target', :id,
                  '--name', 'Arduino LLC Arduino Micro [0001]',
                  '--vendorid', '0x2341',
                  '--productid', '0x0037']
  end

  config.vm.provision "ansible_local" do |ansible|
    ansible.install_mode = :pip
    ansible.playbook = "ansible/playbook.yml"
    ansible.provisioning_path = "/Vagrant"
    ansible.vault_password_file = "/Vagrant/.vault_pass"
    ansible.verbose = true
  end
end
