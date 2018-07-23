# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"
VAGRANT_DEFAULT_PROVIDER = "virtualbox"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "bento/ubuntu-18.04"
  config.vm.box_check_update = true

  # common
  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.cpus = ENV['VAGRANT_CPUS'] || 1
    vb.memory = ENV['VAGRANT_MEM'] || 1024

    vb.customize ['modifyvm', :id, '--clipboard', 'bidirectional']
    vb.customize ['modifyvm', :id, '--vram', '12']
  end

  config.vm.define :develop do |dev|
    dev.vm.hostname = "develop.epaew"
    dev.vm.network :forwarded_port, guest: 3030, host: 3030, id: :https
    dev.vm.synced_folder ".", "/Vagrant", mount_options: ["dmode=775,fmode=664"]

    # for write QMK firmware
    dev.vm.provider "virtualbox" do |vb|
      vb.customize ['modifyvm', :id, '--usb', 'on']
      unless vm_exists?(:develop)
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
    end

    dev.vm.provision "ansible_local" do |ansible|
      ansible.install_mode = :pip
      ansible.playbook = "ansible/playbook.yml"
      ansible.provisioning_path = "/Vagrant"
      ansible.vault_password_file = "/Vagrant/.vault_pass"
      ansible.verbose = true
    end
  end

  #config.vm.define :rails, autostart: false do |rails|
  #  rails.vm.hostname = "rails.epaew"
  #  #rails.vm.synced_folder ".", "/Vagrant"
  #end
end

def vm_exists?(vm_name)
  Dir.exists?(".vagrant/machines/#{vm_name}")
end
