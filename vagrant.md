## Vagrant

### vagrant vbguest

`vagrant plugin install vagrant-vbguest`

`vbguest --status`

`vagrant vbguest --do install`

### vagrant install proxy

`vagrant plugin install vagrant-proxyconf`


### vagrant proxy setting

    d.proxy.http = "10.61.200.20:8080"
    d.proxy.https = "10.61.200.20:8080"
    d.proxy.no_proxy = "localhost,127.0.0.1"
    d.yum_proxy.http = "http://10.61.200.20:8080"

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
    
### Virtualbox extend disk capacity

    vagrant halt
    vboxmanage showhdinfo centos-7-1-1.x86_64.vmdk
    vboxmanage clonehd centos-7-1-1.x86_64.vmdk new-virtualdisk.vdi --format vdi
    vboxmanage modifyhd new-virtualdisk.vdi --resize 192000
    vboxmanage clonehd new-virtualdisk.vdi resized.vmdk --format vmdk
    mv resized.vmdk centos-7-1-1.x86_64.vmdk
    rm new-virtualdisk.vdi
    vboxmanage internalcommands sethduuid centos-7-1-1.x86_64.vmdk <old_uuid>
