# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  
  config.vm.define "ipa", primary: true do |s|
    s.vm.box = "centos/7"
    s.vm.provider :virtualbox do |sb|
        sb.customize ["modifyvm", :id, "--memory", "2048"]
        sb.customize ["modifyvm", :id, "--cpus", "2"]
    end
    s.vm.hostname = 'ipa'
    s.vm.network "private_network", ip: "192.168.111.40"
    s.vm.provision "shell", inline: <<-SHELL
	sudo echo -e "[main]\ndns=none" > /etc/NetworkManager/conf.d/no-dns.conf
	sed -i '65s/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
	sudo systemctl enable firewalld
	sudo systemctl start firewalld
	sudo firewall-cmd --add-service={dns,freeipa-ldap,freeipa-ldaps} --permanent
        sudo firewall-cmd --reload
	SHELL
    s.vm.provision "ansible_local" do |ansiblesrv|
    ansiblesrv.playbook = "ansible/ipaserver.yml"
    end
  end

  config.vm.define "ipaclient" do |c|
    c.vm.box = "centos/7"
    c.vm.provider :virtualbox do |cb|
        cb.customize ["modifyvm", :id, "--memory", "1024"]
        cb.customize ["modifyvm", :id, "--cpus", "1"]
    end
    c.vm.hostname = 'ipaclient'
    c.vm.network "private_network", ip: "192.168.111.41"
    c.vm.provision "shell", inline: <<-SHELL
    	sudo systemctl enable firewalld
        sudo systemctl start firewalld
        sudo echo -e "[main]\ndns=none" > /etc/NetworkManager/conf.d/no-dns.conf
        sed -i '65s/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
    SHELL
    c.vm.provision "ansible_local" do |ansiblecl|
    ansiblecl.playbook = "ansible/ipaclient.yml"
        end
    end
  end
