# language: Ruby
# OS templates = boxes, OS usually Linux
Vagrant.configure("2") do |config|

  # configures OS for VM, this one is older, but stable
  config.vm.box = "ubuntu/xenial64"
  # sets private IP
  config.vm.network "private_network", ip:"192.168.10.100"

  # provisions the VM to have Nginx
  config.vm.provision "shell", path: "provision.sh"

  # put/syncs app folder from local machine to VM
  config.vm.synced_folder "app", "/home/vagrant/app"


end
