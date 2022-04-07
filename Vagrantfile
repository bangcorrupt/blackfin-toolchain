# -*- mode: ruby -*-
# vi: set ft=ruby :

# Debian Bullseye for Blackfin development
#   GNU toolchain and OpenOCD built in Debian Jessie Docker container

Vagrant.configure("2") do |config|
  config.vm.box = "debian/bullseye64"

  config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
   
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "32768"
    vb.cpus = "16"
    vb.customize ["modifyvm", :id, "--usb", "on"]
    vb.customize ["modifyvm", :id, "--usbehci", "on"]
    vb.customize ['usbfilter', 'add', '0', '--target', :id, '--name', 'Analog Devices, Inc. (White Mountain DSP) Blackfin USB Device', '--vendorid', '0x064b', '--productid', '0x0617']
    vb.customize ['usbfilter', 'add', '1', '--target', :id, '--name', 'Analog Devices, Inc. (White Mountain DSP) Blackfin USB Device', '--vendorid', '0x064b', '--productid', '0x0586']
   end

  config.vagrant.plugins = "vagrant-docker-compose"
  config.vm.provision :docker
    
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update && apt-get upgrade -y
    apt-get install -y vim git build-essential make pkg-config usbutils

    rm -rf /home/vagrant/build-bfin-tools
    git clone https://github.com/bangcorrupt/build-bfin-tools.git /home/vagrant/build-bfin-tools
    chown -R vagrant:vagrant /home/vagrant/build-bfin-tools
    
    sudo docker image build -t bangcorrupt/build-bfin-tools /home/vagrant/build-bfin-tools
    sudo docker run --rm -v /home/vagrant/build-bfin-tools/build:/home/builder bangcorrupt/build-bfin-tools
    
    sudo cp -r /home/vagrant/build-bfin-tools/build/blackfin-plus-gnu/buildscript/bfin-elf/bin/* /usr/bin
    sudo cp -r /home/vagrant/build-bfin-tools/build/blackfin-plus-gnu/buildscript/bfin-elf/lib/* /usr/lib
    sudo cp -r /home/vagrant/build-bfin-tools/build/blackfin-plus-gnu/buildscript/bfin-elf/include/* /usr/include

    rm -rf radare2
    git clone https://github.com/radareorg/radare2 /home/vagrant/radare2
    chown -R vagrant:vagrant /home/vagrant/radare2
    ( cd radare2
      sys/install.sh
    )
    r2pm init && r2pm update && r2pm -gi blackfin
  SHELL
end
