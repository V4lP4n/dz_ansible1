# -*- mode: ruby -*-
# vi: set ft=ruby :
$hostsfile_update = <<-'SCRIPT'
echo -e '192.168.56.110 control.example.com control\n192.168.56.111 node1.example.com node1\n192.168.56.112 node2.example.com node2' >> /etc/hosts
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config && systemctl restart sshd
SCRIPT

$control_configure = <<-'SCRIPT'
#install ansible
sudo dnf update -y
sudo dnf install python3 -y
sudo dnf install python3-pip -y
pip3 install ansible --user
#install git
sudo dnf install git -y
#prepare repo

SCRIPT

Vagrant.configure("2") do |config|
    config.vm.provider "libvirt" do |lv|
     
    end

    config.vm.define "control" do |control|
      control.vm.box = "centos/8"
      control.vm.hostname = "control.example.com"
      control.vm.network "private_network", ip: "192.168.56.110"
      control.vm.provision "shell", inline: $hostsfile_update
      control.vm.provider "libvirt" do |l|
        l.boot 'network'
        l.boot 'hd'      
      end
    end

    config.vm.define "node1" do |node1|
    node1.vm.box = "centos/8"
    node1.vm.hostname = "node1.example.com"
    node1.vm.network "private_network", ip: "192.168.56.111"
    node1.vm.provision "shell", inline: $hostsfile_update
    node1.vm.provider "libvirt" do |l|
      l.boot 'network'
      l.boot 'hd'      
    end
  end
  config.vm.define "node2" do |node2|
    node2.vm.box = "centos/8"
    node2.vm.hostname = "node2.example.com"
    node2.vm.network "private_network", ip: "192.168.56.112"
    node2.vm.provision "shell", inline: $hostsfile_update
    node2.vm.provider "libvirt" do |l|
      l.boot 'network'
      l.boot 'hd'      
    end
  end

end
