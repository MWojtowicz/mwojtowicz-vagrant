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
          config.vm.synced_folder host, guest, type: "virtualbox"
        end
    end
    return nil
end

Vagrant.configure("2") do |config|
  config.vm.network "private_network", ip: "192.168.50.4"
  config.vm.box = "centos/7"

  if Vagrant.has_plugin?("vagrant-hostsupdater")
    config.hostsupdater.aliases = [
        "e1v.vm", "admin.e1v.vm", "logs.e1v.vm", "mailhog.e1v.vm",
        "deploy.e1v.vm", "techlunch.e1v.vm"
    ]
    config.hostsupdater.remove_on_suspend = false
  end

  config.vm.provider "virtualbox" do |vb|
    vb.name = "EOV-Devbox"
    vb.cpus = 2
    vb.memory = "1024"
  end

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

  config.vm.provision "shell", run: "always" do |s|
    s.inline = "sudo selinuxenabled 0"
    s.inline = "sudo setenforce 0"
  end

  mountDir(config, "./", "/vagrant")
  mountDir(config, "../rtcpiv", "/var/www/rtcpiv")
  mountDir(config, "../optrtcbin", "/opt/rtc/bin")
  mountDir(config, "../services", "/var/piv/services")
  mountDir(config, "../deployment-service", "/srv/www/deployment-service/current")
  mountDir(config, "../tech_lunch_slides", "/var/www/html/tech_lunch_slides")

  if which('ansible-playbook')
    config.vm.provision "ansible" do |ansible|
      ansible.playbook = "playbook.yml"
      ansible.inventory_path = "inventories/dev"
      ansible.limit = "all"
      ansible.verbose = "vvvv"
    end
  else
    config.vm.provision "shell", inline: <<-SHELL
      sudo yum install epel-release -y
      sudo yum update
      sudo yum install ansible -y
      sudo cp /vagrant/inventories/dev /etc/ansible/hosts -f
      chmod 666 /etc/ansible/hosts
      cat /vagrant/files/authorized_keys >> /home/vagrant/.ssh/authorized_keys
      sudo ansible-playbook /vagrant/playbook.yml -e hostname=$1 --connection=local
    SHELL
  end
end