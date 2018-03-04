# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/xenial64"
  config.vm.provider "virtualbox" do |vb|
    vb.name = "jenkins.server"
    vb.gui = false
    vb.memory = "1024"
  end

  config.vm.network :private_network, ip: "192.168.42.42"
  config.vm.hostname = "jenkins.server"

  # add pub key to vm
  config.vm.provision "shell" do |s|
    ssh_pub_key = ""
    if File.file?("#{Dir.home}/.ssh/id_rsa.pub")
      ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
    else
      puts "No SSH key found. You will need to remedy this before pushing to the repository."
    end
    s.inline = <<-SHELL
    if grep -sq "#{ssh_pub_key}" /home/vagrant/.ssh/authorized_keys; then
      echo "SSH keys already provisioned."
      exit 0;
    fi
    echo "SSH key provisioning."
    mkdir -p /home/vagrant/.ssh/
    touch /home/vagrant/.ssh/authorized_keys
    echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
    chown -R vagrant:vagrant /home/vagrant
    exit 0
  SHELL
  end
  
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "ansible/playbook.yml"
    ansible.inventory_path = "ansible/inventories/dev"
    ansible.limit = 'all'
  end

  config.vm.synced_folder "./", "/vagrant", type: "nfs"
end
 
