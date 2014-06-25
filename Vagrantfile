# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'rbconfig'
# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"
$is_windows = (RbConfig::CONFIG['host_os'] =~ /mswin|mingw|cygwin/)

def provision(box, playbook, hosts)
  # Run the shell provisioner with a specified playbook and inventory

  playbook_path = "devops/" + playbook
  inventory_path = "devops/" + hosts

  if $is_windows
    # Provisioning configuration for shell script.
    box.vm.provision "shell" do |sh|
      sh.path = "provision.sh"
      sh.args = playbook_path + " " + inventory_path
    end
  else
    # Provisioning configuration for Ansible (for Mac/Linux hosts).
    box.vm.provision "ansible" do |ansible|
      ansible.playbook = playbook_path
      ansible.inventory_path = inventory_path
      ansible.sudo = true
    end
  end
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

    # Dev box
    config.vm.define "dev" do |dev|
        dev.vm.box = "hashicorp/precise64"
        dev.vm.hostname = "dev"

        dev.vm.network "private_network", ip: "192.168.221.3"

        dev.vm.provider "virtualbox" do |v|
          v.memory = 1024
          v.cpus = 2
          v.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]
        end

        dev.vm.synced_folder ".", "/vagrant",
          type: "nfs"
          # type: "rsync",
          # rsync__exclude: [".idea/"],
          # rsync__auto: true,
          # rsync__args: ["--verbose", "--archive", "-z", "--perms"]

        provision(dev, "dev.yml", "dev.hosts")
    end

end