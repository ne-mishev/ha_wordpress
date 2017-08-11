# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

VAGRANTFILE_API_VERSION = "2"

cluster = {
  "web-1" => { :ip => "10.0.10.11",  :cpus => 1, :mem => 1024 },
  "web-2"  => { :ip => "10.0.10.12", :cpus => 1, :mem => 1024 },
  "proxy-1"   => { :ip => "10.0.10.10", :cpus => 1, :mem => 1024 },
}

VAGRANT_VM_PROVIDER = "virtualbox"
ANSIBLE_RAW_SSH_ARGS = []

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  cluster.each_with_index do |(hostname, info), index|
    config.vm.define hostname do |cfg|
	cfg.vm.provider :virtualbox do |vb, override|
        override.vm.box = "centos/7"
        override.vm.box_url = "https://vagrantcloud.com/centos/boxes/7/versions/1707.01/providers/virtualbox.box"
        override.vm.network :private_network, ip: "#{info[:ip]}"
        override.vm.hostname = hostname

        vb.name = hostname
        vb.customize ["modifyvm", :id, "--memory", info[:mem], "--cpus", info[:cpus], "--hwvirtex", "on" ]
        end #end cfg
      
      ANSIBLE_RAW_SSH_ARGS << "-o IdentityFile=.vagrant/machines/#{hostname}/#{VAGRANT_VM_PROVIDER}/private_key"
      # provision nodes with ansible
      if index == cluster.size - 1
	cfg.vm.network "forwarded_port", guest: 80, host: 8080
        cfg.vm.provision :ansible do |ansible|

          ansible.inventory_path = "provisioning/hosts"
          ansible.playbook = "provisioning/provision.yml"
          ansible.limit = 'all'# "#{info[:ip]}" # Ansible hosts are identified by ip
	  ansible.raw_ssh_args = ANSIBLE_RAW_SSH_ARGS
        end # end provision
      end #end if
    end # end config
  end #end cluster
end #end vagrant


  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  #config.vm.network "forwarded_port", guest: 80, host: 80, host_ip: "10.0.10.10"

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
  # config.vm.synced_folder "../data", "/vagrant_data"

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
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
