## Vagrant

### vagrant vbguest

`vagrant plugin install vagrant-vbguest`

`vbguest --status`

`vagrant vbguest --do install`

### vagrant install proxy

`vagrant plugin install vagrant-proxyconf`

### memory setup

    d.vm.provider "virtualbox" do |vb|
       # Display the VirtualBox GUI when booting the machine
       vb.gui = false
    
       # Customize the amount of memory on the VM:
       vb.memory = "2048"
    end

### install docker by vagrant

    d.vm.provision "docker" do |dk|
    end
    
