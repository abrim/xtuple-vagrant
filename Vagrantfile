# -*- mode: ruby -*-
# vi: set ft=ruby :
# To customize: create xtlocal.rb in the current directory
#               containing assignments to override these defaults
# xtHostOffset is a convenience variable.
#   $ egrep "^xt(Host|VmName)" Vagrantfile > xtlocal.rb
#   then edit xtHostOffset, xtHostAddr, xtHostName, xtHostSetupFile, xtVMName in xtlocal.rb
xtHostOffset    = 0

xtBox           = "ubuntu/xenial64"
xtBoxUrl        = nil
xtForwardX11    = true
xtGuestAppPort  = 8443
xtGuestRestPort = 3000
xtGuestWebPort  = 8888
xtGui           = false
xtHostAddr      = "192.168.33.10"
xtHostAppPort   = xtGuestAppPort  + xtHostOffset
xtHostName      = "xtuple-testvm"
xtHostRestPort  = xtGuestRestPort + xtHostOffset
xtHostSetupFile = "mwdev_setup.sh"
xtHostWebPort   = xtGuestWebPort  + xtHostOffset
xtPostgresVer   = "9.3"
xtQtVer         = "5"
xtSourceDir     = "../../dev"
xtSourceMountPt = "/home/vagrant/dev"
xtVagrantVer    = ">= 1.6.4"
xtVmName        = xtHostName
xtVboxGuestAU   = true
xtVboxGuestNR   = true
xtVboxMemory    = "4096"
xtVmwareMemory  = "4096"

if File.file? "xtlocal.rb"
  IO.foreach("xtlocal.rb") { |line| eval line }
end

Vagrant.require_version xtVagrantVer

Vagrant.configure("2") do |config|
  config.vm.hostname = xtHostName

  config.vm.post_up_message = "Welcome to the xTuple Server virtual environment.
  Use the command 'vagrant ssh' to access your server."

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = xtBox

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  config.vm.box_url = xtBoxUrl

  # config.vm.provider 'vmware_fusion' do |v, override|
  #   override.vm.box     = 'precise64_fusion'
  #   override.vm.box_url = 'http://files.vagrantup.com/precise64_vmware.box'
  # end

  # Vbguest Plugin
  # We will try to autodetect this path.
  # However, if we cannot or you have a special one you may pass it like:
  # config.vbguest.iso_path = "#{ENV['HOME']}/Downloads/VBoxGuestAdditions.iso"
  # or
  # config.vbguest.iso_path = "http://company.server/VirtualBox/%{version}/VBoxGuestAdditions.iso"

  # set auto_update to false, if you do NOT want to check the correct
  # additions version when booting this machine
  config.vbguest.auto_update = xtVboxGuestAU

  # do NOT download the iso file from a webserver
  config.vbguest.no_remote = xtVboxGuestNR

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine.
  config.vm.network :forwarded_port, guest: xtGuestWebPort,  host: xtHostWebPort
  config.vm.network :forwarded_port, guest: xtGuestAppPort,  host: xtHostAppPort
  config.vm.network :forwarded_port, guest: xtGuestRestPort, host: xtHostRestPort

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network :private_network, ip: xtHostAddr

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network :public_network

  # Share a folder with the guest VM: path-on-host path-on-guest [options]
  config.vm.synced_folder xtSourceDir, xtSourceMountPt

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  config.vm.provider "virtualbox" do |v|
    # Use VBoxManage to customize the VM
    # This line disable hw virtualization and increases memory
    v.customize ["modifyvm", :id, "--memory", xtVboxMemory]

    # Via http://blog.liip.ch/archive/2012/07/25/vagrant-and-node-js-quick-tip.html
    v.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/vagrant", "1"]

    # Debug VM by booting in Gui mode
    v.gui = xtGui
    v.name = xtVmName

    # If the host CPU does not have hardware virtualization support,
    # this will disable that setting in VirtualBox - only works on 32-bit OS
    #v.customize ["modifyvm", :id, "--hwvirtex", "off"]
    #v.customize ["modifyvm", :id, "--cpus", "1"]
  end

  config.vm.provider "vmware_fusion" do |v|
    v.vmx["memsize"] = xtVmwareMemory
  end

  # This ensures that the locale is correctly set for Postgres
  config.vm.provision "shell", inline: 'update-locale LC_ALL="en_US.utf8"'

  # Run install script virtual machine is created
  # This forces the script to *not* be run as sudo
  config.vm.provision "shell", path: xtHostSetupFile, privileged: false, args: [ "-p", xtPostgresVer, "-q", xtQtVer ]

  config.ssh.forward_x11 = xtForwardX11

end
