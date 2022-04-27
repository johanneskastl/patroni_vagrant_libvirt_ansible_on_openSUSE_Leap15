# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  ###################################################################################
  # define number of nodes
  P = 3

  # provision W VMs as nodes
  (1..P).each do |i|

    # name the VMs
    config.vm.define "patroni#{i}" do |node|

      # which image to use
      node.vm.box = "opensuse/Leap-15.3.x86_64"

      # sizing of the VMs
      node.vm.provider "libvirt" do |lv|
        lv.random_hostname = true
        lv.memory = 4096
        lv.cpus = 2
      end

      # set the hostname
      node.vm.hostname = "patroni#{i}"

      # if this is the last machine
      if i == P

        #################################################
        # only target one node to not run ansible twice
        # but do not limit this to this node (set ansible.limit to all)
        node.vm.provision "ansible" do |ansible|
          ansible.compatibility_mode = "2.0"
          ansible.limit = "all"
          ansible.groups = {
            "patroni_nodes"  => [ "patroni1", "patroni2", "patroni3" ],
          }
          ansible.playbook = "ansible/playbook-vagrant.yml"

        end # node.vm.provision
      end # if-condition last machine
    end # config.vm.define nodes
  end # each-loop over all VMs
end