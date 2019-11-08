BOX_IMAGE = "bento/centos-7.7"
NODE_COUNT = 3

Vagrant.configure("2") do |config|
    config.vm.define "loadbalancer" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.hostname = "loadbalancer"
      subconfig.vm.network :private_network, ip: "10.0.0.10"
      subconfig.vm.network "forwarded_port", guest: 80, host: 8080
      subconfig.vm.network "forwarded_port", guest: 9000, host: 9000
      subconfig.vm.network "public_network", use_dhcp_assigned_default_route: true
    end
  
  (1..NODE_COUNT).each do |j|
    config.vm.define "node#{j}" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.hostname = "node#{j}"
      subconfig.vm.network :private_network, ip: "10.0.0.#{j + 1}"
      subconfig.vm.network "public_network", use_dhcp_assigned_default_route: true
    end
  end
end
