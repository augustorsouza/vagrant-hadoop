# -*- mode: ruby -*-
# vi: set ft=ruby :

memsize = ENV["MEMSIZE"] || "3072"
slaves = ENV["SLAVES"] || 2

ips = ""

# slaves + 1 master + 1 client
(slaves + 1 + 1).times do |i|
  if (i == slaves + 1) # last element
    ips += "10.211.55.10#{i}   vm-cluster-client\n"
  else
    ips += "10.211.55.10#{i}   vm-cluster-node#{i+1}\n"
  end
end

$master_script = <<SCRIPT
#!/bin/bash
cat > /etc/hosts <<EOF
127.0.0.1       localhost

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

#{ips}
EOF

apt-get install curl -y
REPOCM=${REPOCM:-cm4}
CM_REPO_HOST=${CM_REPO_HOST:-archive.cloudera.com}
CM_MAJOR_VERSION=$(echo $REPOCM | sed -e 's/cm\\([0-9]\\).*/\\1/')
CM_VERSION=$(echo $REPOCM | sed -e 's/cm\\([0-9][0-9]*\\)/\\1/')
OS_CODENAME=$(lsb_release -sc)
OS_DISTID=$(lsb_release -si | tr '[A-Z]' '[a-z]')
if [ $CM_MAJOR_VERSION -ge 4 ]; then
  cat > /etc/apt/sources.list.d/cloudera-$REPOCM.list <<EOF
deb [arch=amd64] http://$CM_REPO_HOST/cm$CM_MAJOR_VERSION/$OS_DISTID/$OS_CODENAME/amd64/cm $OS_CODENAME-$REPOCM contrib
deb-src http://$CM_REPO_HOST/cm$CM_MAJOR_VERSION/$OS_DISTID/$OS_CODENAME/amd64/cm $OS_CODENAME-$REPOCM contrib
EOF
curl -s http://$CM_REPO_HOST/cm$CM_MAJOR_VERSION/$OS_DISTID/$OS_CODENAME/amd64/cm/archive.key > key
apt-key add key
rm key
fi
apt-get update
export DEBIAN_FRONTEND=noninteractive
apt-get -q -y --force-yes install oracle-j2sdk1.6 cloudera-manager-server-db cloudera-manager-server cloudera-manager-daemons
service cloudera-scm-server-db initdb
service cloudera-scm-server-db start
service cloudera-scm-server start
SCRIPT

$slave_script = <<SCRIPT
cat > /etc/hosts <<EOF
127.0.0.1       localhost

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

#{ips}
EOF
SCRIPT

$client_script = <<SCRIPT
cat > /etc/hosts <<EOF
127.0.0.1       localhost

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

#{ips}
EOF
SCRIPT

Vagrant.configure("2") do |config|

  config.vm.define :master do |master|
    master.vm.box = "precise64"
    master.vm.provider "virtualbox" do |v|
      v.vmx["memsize"]  = memsize
    end
    master.vm.provider :virtualbox do |v|
      v.name = "vm-cluster-node1"
      v.customize ["modifyvm", :id, "--memory", memsize]
    end
    master.vm.network :private_network, ip: "10.211.55.100"
    master.vm.hostname = "vm-cluster-node1"
    master.vm.provision :shell, :inline => $master_script
  end

  slaves.times do |i|
    config.vm.define "slave#{i+1}" do |slave|
      slave.vm.box = "precise64"
      slave.vm.provider "virtualbox" do |v|
        v.vmx["memsize"]  = memsize
      end
      slave.vm.provider :virtualbox do |v|
        v.name = "vm-cluster-node#{i+2}"
        v.customize ["modifyvm", :id, "--memory", memsize]
      end
      slave.vm.network :private_network, ip: "10.211.55.10#{i+1}"
      slave.vm.hostname = "vm-cluster-node#{i+2}"
      slave.vm.provision :shell, :inline => $slave_script
    end
  end
  
  config.vm.define :client do |client|
    client.vm.box = "precise64"
    client.vm.provider "virtualbox" do |v|
      v.vmx["memsize"]  = memsize
    end
    client.vm.provider :virtualbox do |v|
      v.name = "vm-cluster-client"
      v.customize ["modifyvm", :id, "--memory", memsize]
    end
    client.vm.network :private_network, ip: "10.211.55.10#{slaves + 1}"
    client.vm.hostname = "vm-cluster-client"
    client.vm.provision :shell, :inline => $client_script
  end

end

puts %{
Please, make sure the following IPs are listed in your /etc/hosts file...
#{ips}
}