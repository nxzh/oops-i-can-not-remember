# Virtual Box

## Query

### List VMs
VBoxManage.exe list vms

### Show VM detail info
VBoxManage.exe showvminfo CentOS7_Desktop

### Show NAT networks
VBoxManage.exe list natnets

### Port forwarding
VBoxManage natnetwork modify --netname ${NATWORKWORK_NAME} --port-forward-4 "es1:ssh:[]:8085:[192.168.15.5]:9200"
