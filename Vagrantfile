# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
# variables
NUM_MASTER_NODES = 1
NUM_WORKER_NODES = 2

IP_NW = "192.168.5."
MASTER_IP_START = 10
NODE_IP_START = 20
LB_IP_START = 30

Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/bionic64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  config.vm.box_check_update = false

  # Provision master nodes
  (1..NUM_MASTER_NODES).each do |i|
      config.vm.define "master-#{i}" do |node|
        # Name shown in the GUI
        node.vm.provider "virtualbox" do |vb|
            vb.name = "kubernetes-ha-master-#{i}"
            vb.memory = 2048
            vb.cpus = 2
        end
        node.vm.hostname = "master-#{i}"
        node.vm.network :private_network, ip: IP_NW + "#{MASTER_IP_START + i}"
        node.vm.network "forwarded_port", guest: 22, host: "#{2710 + i}"

        node.vm.provision "setup-hosts", :type => "shell", :path => "ubuntu/vagrant/setup-hosts.sh" do |s|
          s.args = ["enp0s8"]
        end

        node.vm.provision "setup-dns", type: "shell", :path => "ubuntu/update-dns.sh"

      end
  end

  # Provision worker nodes
  (1..NUM_WORKER_NODES).each do |i|
    config.vm.define "worker-#{i}" do |node|
        node.vm.provider "virtualbox" do |vb|
            vb.name = "kubernetes-ha-worker-#{i}"
            vb.memory = 512
            vb.cpus = 1
        end
        node.vm.hostname = "worker-#{i}"
        node.vm.network :private_network, ip: IP_NW + "#{NODE_IP_START + i}"
        node.vm.network "forwarded_port", guest: 22, host: "#{2720 + i}"

        node.vm.provision "setup-hosts", :type => "shell", :path => "ubuntu/vagrant/setup-hosts.sh" do |s|
          s.args = ["enp0s8"]
        end

        node.vm.provision "setup-dns", type: "shell", :path => "ubuntu/update-dns.sh"
        node.vm.provision "install-docker", type: "shell", :path => "ubuntu/install-docker.sh"
        node.vm.provision "allow-bridge-nf-traffic", :type => "shell", :path => "ubuntu/allow-bridge-nf-traffic.sh"

    end
  end

  # Provision load balancer
  config.vm.define "loadbalancer" do |node|
    node.vm.provider "virtualbox" do |vb|
        vb.name = "kubernetes-ha-lb"
        vb.memory = 512
        vb.cpus = 1
    end
    node.vm.hostname = "loadbalancer"
    node.vm.network :private_network, ip: IP_NW + "#{LB_IP_START}"
    node.vm.network "forwarded_port", guest: 22, host: 2730

    node.vm.provision "setup-hosts", :type => "shell", :path => "ubuntu/vagrant/setup-hosts.sh" do |s|
      s.args = ["enp0s8"]
    end

    node.vm.provision "setup-dns", type: "shell", :path => "ubuntu/update-dns.sh"

  end

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

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
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL

end
