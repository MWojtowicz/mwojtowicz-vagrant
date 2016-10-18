# -*- mode: ruby -*-
# vi: set ft=ruby :

def which(cmd)
  exts = ENV['PATHEXT'] ? ENV['PATHEXT'].split(';') : ['']
  ENV['PATH'].split(File::PATH_SEPARATOR).each do |path|
    exts.each { |ext|
      exe = File.join(path, "#{cmd}#{ext}")
      return exe if File.executable? exe
    }
  end
  return nil
end

def mountDir(config, host, guest)
    if File.directory?(File.expand_path(host))
        if Vagrant::Util::Platform.bsd?
            config.vm.synced_folder host, guest, type: "nfs"
        elsif Vagrant::Util::Platform.darwin?
            config.vm.synced_folder host, guest, type: "nfs"
        elsif Vagrant::Util::Platform.linux?
          config.vm.synced_folder host, guest, id: "v-root", mount_options: ["rw", "tcp", "nolock", "noacl", "async"], type: "nfs", nfs_udp: true
        else
          if Vagrant.has_plugin?("vagrant-winnfsd")
            config.vm.synced_folder host, guest, type: "nfs"
          else
            config.vm.synced_folder host, guest, type: "virtualbox"
          end
        end
    end
    return nil
end

Vagrant.configure("2") do |config|
  config.vm.network "private_network", ip: "192.168.50.5"
  config.vm.network "private_network", type: "dhcp"
  config.vm.box = "centos/7"

  if Vagrant.has_plugin?("vagrant-hostsupdater")
    config.hostsupdater.aliases = [
        "cms.vm", "admin.cms.vm", "logs.cms.vm", "mailhog.cms.vm", "phpmyadmin.cms.vm"
    ]
    config.hostsupdater.remove_on_suspend = false
  end

  config.vm.provider "virtualbox" do |vb|
    vb.name = "CMS-Devbox"
    vb.cpus = 2
    vb.memory = "1024"
  end

  mountDir(config, "./", "/vagrant")
  mountDir(config, "../kurs-symfony", "/var/www/cms")

  if which('ansible-playbook')
    config.vm.provision "ansible" do |ansible|
      ansible.playbook = "playbook.yml"
      ansible.inventory_path = "inventories/dev"
      ansible.limit = "all"
      ansible.verbose = "vv"
    end
  else
    config.vm.provision "shell", inline: <<-SHELL
      sudo yum install epel-release -y
      sudo yum install ansible -y
      sudo cp /vagrant/inventories/dev /etc/ansible/hosts -f
      chmod 666 /etc/ansible/hosts
      cat /vagrant/files/authorized_keys >> ~/.ssh/authorized_keys
      sudo ansible-galaxy install MWojtowicz.devbox
      sudo ansible-playbook /vagrant/playbook.yml -e hostname=$1 --connection=local
    SHELL
  end
end