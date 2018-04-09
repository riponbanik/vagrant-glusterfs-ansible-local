# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.ssh.insert_key = false
  glusterfs_version = "3.13"
  
  # Allocate Resoure
  config.vm.provider :virtualbox do |v|
    v.memory = 512
    v.cpus = 1
  end

  # Manage Host
  config.hostmanager.enabled = true
  config.hostmanager.manage_guest = true

  # Configure host   
  config.vm.provision "shell", inline: <<-SHELL
    sudo sed -i -e "\\#PasswordAuthentication no# s#PasswordAuthentication no#PasswordAuthentication yes#g" /etc/ssh/sshd_config
    sudo service sshd restart       
    sudo yum -y install net-tools
    sudo yum -y install telnet 
  SHELL
  
  # We setup three nodes to be gluster hosts
  N = 3
  (1..N).each do |i|
    config.vm.define vm_name = "glusterfs-node#{i}" do |config|
      config.cache.scope = :box
      config.vm.hostname = "#{vm_name}.lab.local"
      ip = "192.168.56.#{i+64}"
      config.vm.network :private_network, ip: ip      
           
      # Excute after the last VM is booted.    
      if i == N
        config.vm.provision :ansible_local do |ansible|
           ansible.playbook       = "playbook.yml"
           ansible.verbose        = true
           ansible.install        = true
           ansible.install_mode = "pip"
           ansible.version = "2.4.3.0"
           ansible.become = true
           ansible.become_user = "vagrant"      
           ansible.limit          = "all" # or only "nodes" group, etc.
           ansible.inventory_path = "inventory"
       end 
      end  
    end 
  end
end
