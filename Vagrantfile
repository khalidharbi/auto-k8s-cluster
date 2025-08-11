Vagrant.configure("2") do |config|
  config.vm.box = "generic/rocky9"

  # Define control-plane nodes
  config.vm.define "control" do |node|
    node.vm.hostname = "control"
    node.vm.network "private_network",
      ip: "192.168.122.101",
      libvirt__network_name: "default"


    node.vm.provider :libvirt do |libvirt|
      libvirt.cpus = 2
      libvirt.memory = 2048
      libvirt.storage :file, size: "20G"
      libvirt.uri = "qemu:///system"
    end

    node.vm.provision "shell", inline: <<-SHELL
      mkdir -p /home/vagrant/.ssh
      echo 'ADD YOUR SSH KEY HERE' >> /home/vagrant/.ssh/authorized_keys 
      chmod 600 /home/vagrant/.ssh/authorized_keys
      chown -R vagrant:vagrant /home/vagrant/.ssh
    SHELL
  end

  # Define worker nodes
  (1..2).each do |i|
    config.vm.define "worker#{i}" do |node|
      node.vm.hostname = "worker#{i}"
      node.vm.network "private_network",
        ip: "192.168.122.#{110 + i}",
        libvirt__network_name: "default"

      node.vm.provider :libvirt do |libvirt|
        libvirt.cpus = 2
        libvirt.memory = 2048
        libvirt.storage :file, size: "20G"
        libvirt.uri = "qemu:///system"
      end

      node.vm.provision "shell", inline: <<-SHELL
        mkdir -p /home/vagrant/.ssh
        echo 'ADD YOUR SSH KEY HERE' >> /home/vagrant/.ssh/authorized_keys
        chmod 600 /home/vagrant/.ssh/authorized_keys
        chown -R vagrant:vagrant /home/vagrant/.ssh
      SHELL
    end
  end
end
