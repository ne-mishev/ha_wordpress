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
  "db-1" => { :ip => "10.0.10.13",  :cpus => 1, :mem => 1024 },
  "db-2"  => { :ip => "10.0.10.14", :cpus => 1, :mem => 1024 },
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
	cfg.vm.network "forwarded_port", guest: 443, host: 8443
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
