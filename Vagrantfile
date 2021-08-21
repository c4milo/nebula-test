Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/bionic64"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "512"
  end

  config.vm.provision "shell", inline: <<-SHELL
  	export DEBIAN_FRONTEND=noninteractive
    add-apt-repository --update ppa:nicolais/termshark
    apt-get update -y
    apt-get install traceroute -y
  SHELL

  config.vm.define "web" do |web|
    web.vm.hostname = "web"
    web.vm.network "private_network", ip: "192.168.10.10"
    web.vm.provision "shell", inline: <<-SHELL
      ip route replace default proto static scope global \
        nexthop dev eth1 via 192.168.10.1 weight 1 \
        nexthop dev eth1 via 192.168.10.2 weight 1
      sysctl -w net.ipv4.fib_multipath_hash_policy=1 # always do L4 hashing
    SHELL
  end

  config.vm.define "router1" do |router1|
    router1.vm.hostname = "router1"
    router1.vm.network "private_network", ip: "192.168.10.1"
    router1.vm.network "private_network", ip: "192.168.20.1"
    router1.vm.provision "shell", inline: <<-SHELL
      sysctl -w net.ipv4.ip_forward=1
    SHELL
  end

  config.vm.define "router2" do |router2|
    router2.vm.hostname = "router2"
    router2.vm.network "private_network", ip: "192.168.10.2"
    router2.vm.network "private_network", ip: "192.168.20.2"
    router2.vm.provision "shell", inline: <<-SHELL
      sysctl -w net.ipv4.ip_forward=1
    SHELL
  end

  config.vm.define "db" do |db|
    db.vm.hostname = "db"
    db.vm.network "private_network", ip: "192.168.20.10"
	db.vm.provision "shell", inline: <<-SHELL
      ip route replace default proto static scope global \
        nexthop dev eth1 via 192.168.20.1 weight 1 \
        nexthop dev eth1 via 192.168.20.2 weight 1
      sysctl -w net.ipv4.fib_multipath_hash_policy=1 # always do L4 hashing
    SHELL
  end
end
