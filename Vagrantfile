# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = '2'.freeze
VAGRANT_DEFAULT_PROVIDER = 'virtualbox'.freeze
VAGRANT_MOUNT_PATH = '/vagrant'.freeze

configure_ansible = proc do |ansible|
  ansible.install_mode = :pip
  ansible.playbook = 'ansible/playbook.yml'
  ansible.provisioning_path = VAGRANT_MOUNT_PATH
  ansible.vault_password_file = "#{VAGRANT_MOUNT_PATH}/.vault_pass"
  ansible.verbose = true
end

configure_develop = proc do |dev|
  dev.vm.hostname = 'develop.epaew'
  dev.vm.network :forwarded_port, guest: 3000, host: 3000, id: :http
  dev.vm.network :forwarded_port, guest: 3030, host: 3030, id: :https
  dev.vm.network :forwarded_port, guest: 3035, host: 3035, id: :webpack
  dev.vm.synced_folder '.', VAGRANT_MOUNT_PATH,
                       mount_options: ['dmode=775,fmode=664']

  # for write QMK firmware
  dev.vm.provider 'virtualbox' do |vb|
    vb.customize ['modifyvm', :id, '--usb', 'on']
    unless vm_exists?(:develop)
      vb.customize [
        'usbfilter', 'add', '0', '--target', :id,
        '--name', 'Arduino LLC Arduino Micro [0100]',
        '--vendorid', '0x2341', '--productid', '0x8037'
      ]
      vb.customize [
        'usbfilter', 'add', '1', '--target', :id,
        '--name', 'Arduino LLC Arduino Micro [0001]',
        '--vendorid', '0x2341', '--productid', '0x0037'
      ]
    end
  end

  dev.vm.provision 'ansible_local', &configure_ansible
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = 'ubuntu/bionic64'
  config.vm.box_check_update = true

  # common
  config.vm.provider 'virtualbox' do |vb|
    vb.gui = false
    vb.cpus = ENV['VAGRANT_CPUS'] || 1
    vb.memory = ENV['VAGRANT_MEM'] || 1024

    vb.customize ['modifyvm', :id, '--clipboard', 'bidirectional']
    vb.customize ['modifyvm', :id, '--vram', '12']
  end

  config.vm.define :develop, &configure_develop

  # config.vm.define :rails, autostart: false do |rails|
  #   rails.vm.hostname = 'rails.epaew'
  #   #rails.vm.synced_folder '.', VAGRANT_MOUNT_PATH
  # end
end

def vm_exists?(vm_name)
  Dir.exist?(".vagrant/machines/#{vm_name}")
end
