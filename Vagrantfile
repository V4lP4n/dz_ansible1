# -*- mode: ruby -*-
# vi: set ft=ruby :
$hostsfile_update = <<-'SCRIPT'
echo -e '192.168.56.110 control.example.com control\n192.168.56.111 node1.example.com node1\n192.168.56.112 node2.example.com node2' >> /etc/hosts
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config && systemctl restart sshd
sudo useradd ansible
echo "ansible ALL=(ALL) NOPASSWD:ALL" | sudo tee -a /etc/sudoers.d/ansible
echo password | passwd --stdin ansible
mkdir -p '/home/ansible/.ssh'
chown -R ansible:ansible /home/ansible
SCRIPT

$control_configure = <<-'SCRIPT'
#install ansible
sudo dnf update -y
sudo dnf install python3 -y
sudo dnf install python3-pip -y
sudo -u ansible pip3 install --upgrade pip --user
sudo -u ansible pip3 install ansible --user
#install git
sudo dnf install git -y
#prepare repo
cd ~
sudo -u ansible git clone https://github.com/V4lP4n/ansible-hw-3 /home/ansible/dz_ansible

#gen and copy ssh key
curl https://download-ib01.fedoraproject.org/pub/epel/8/Everything/x86_64/Packages/s/sshpass-1.06-9.el8.x86_64.rpm -o sshpass.rpm
sudo dnf install -y ./sshpass.rpm


SCRIPT

$ssh_key_setup  = <<-'SCRIPT'
ssh-keygen -b 2048 -t rsa -f /home/ansible/.ssh/id_rsa -q -N ""

chown -R ansible:ansible /home/ansible

sudo -u ansible sshpass -p 'password' ssh-copy-id ansible@node1 -o StrictHostKeyChecking=no
sudo -u ansible sshpass -p 'password' ssh-copy-id ansible@node2 -o StrictHostKeyChecking=no

SCRIPT
Vagrant.configure("2") do |config|
    config.vm.provider "libvirt" do |lv|
     
    end

    config.vm.define "control" do |control|
      control.vm.box = "centos/8"
      control.vm.hostname = "control.example.com"
      control.vm.network "private_network", ip: "192.168.56.110"
      control.vm.provision "shell", inline: $hostsfile_update
      control.vm.provision "shell", inline: $control_configure, privileged: false
      control.vm.provision "shell", inline: $ssh_key_setup
      control.vm.provider "libvirt" do |l|
        l.boot 'network'
        l.boot 'hd'
        l.cpus = 2
        l.memory = 6144      
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
