# -*- mode: ruby -*-
# vi: set ft=ruby :

$NET_PREFIX       = "172.16.124"
$DC_IP            = "#{$NET_PREFIX}.50"
$IIS_IP           = "#{$NET_PREFIX}.51"
$CLIENT_IP        = "#{$NET_PREFIX}.52"
$LAB_NET_PATTERN  = "#{$NET_PREFIX}.*"
$BASE_BOX         = "eval-win2012r2-standard-nocm-1.0.4"

Vagrant.configure(2) do |config|
  config.vm.provider "virtualbox" do |vb|
#     vb.gui = false
#     vb.customize ["modifyvm", :id, "--memory", "1024"]
     vb.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
  end

  config.vm.define "dc01" do |config|
    config.vm.box = $BASE_BOX
    config.vm.hostname = 'dc01'

    config.vm.network "public_network", bridge: "vmnet1", ip: $DC_IP

    config.vm.provision "shell", path: "provision/00_common.ps1", \
        args: [$LAB_NET_PATTERN]
    config.vm.provision "shell", path: "provision/01_install_AD.ps1"
    config.vm.provision "shell", path: "provision/02_install_forest.ps1"
    config.vm.provision "shell", path: "provision/03_populate_AD.ps1", \
        args: [$IIS_IP]

    config.vm.synced_folder "/Volumes/EXT/Downloads", "/downloads"
  end

  config.vm.define "web01" do |config|
    config.vm.box = $BASE_BOX
    config.vm.hostname = 'web01'

    config.vm.network "public_network", bridge: "vmnet1", ip: $IIS_IP
    
    config.vm.provision "shell", path: "provision/00_common.ps1", \
        args: [$LAB_NET_PATTERN]
    config.vm.provision "shell", path: "provision/06_join_domain.ps1", \
        args: [$LAB_NET_PATTERN, $DC_IP]
    config.vm.provision "shell", path: "provision/07_install_iis.ps1"

    config.vm.synced_folder "/Volumes/EXT/Downloads", "/downloads"
  end

  config.vm.define "client01" do |config|
    config.vm.box = $BASE_BOX
    config.vm.hostname = 'client01'

    config.vm.network "public_network", bridge: "vmnet1", ip: $CLIENT_IP
    
    config.vm.provision "shell", path: "provision/00_common.ps1", \
        args: [$LAB_NET_PATTERN]
    config.vm.provision "shell", path: "provision/06_join_domain.ps1", \
        args: [$LAB_NET_PATTERN, $DC_IP]
    config.vm.provision "shell", path: "provision/08_client.ps1"

    config.vm.synced_folder "/Volumes/EXT/Downloads", "/downloads"
  end
end
