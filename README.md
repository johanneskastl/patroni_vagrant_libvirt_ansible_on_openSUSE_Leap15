# patroni_vagrant_libvirt_ansible_on_openSUSE_Leap15

This Vagrant setup creates all that you need to start playing with [Patroni](https://github.com/zalando/patroni/). This means we have three VMs running Patroni, postgreSQL and etcd and another VM running haproxy, loadbalancing your PostgreSQL cluster.

Patroni uses the etcd cluster, running on the same three nodes, to store the state of the PostgreSQL cluster.

Default OS is openSUSE Leap 15.3, but that can be changed in the Vagrantfile. Please beware, this might break the ansible provisioning which was only tested with openSUSE Leap 15.3.

(There is [another setup using SLES15 SP3](https://github.com/johanneskastl/patroni_vagrant_libvirt_ansible_on_SLES15) which is ~~very similar~~ almost identical to this one)

## Vagrant

0. Make sure your system meets the requirements for the VMs RAM. If not, feel free to reduce the size of the Patroni nodes (default `4096` MB).
1. You need vagrant obviously. And ansible. And git...
2. Fetch the box, per default this is `opensuse/Leap-15.3.x86_64`, using `vagrant box add opensuse/Leap-15.3.x86_64`.
3. Make sure the git submodules are fully working by issuing `git submodule init && git submodule update`
4. Run `vagrant up`
5. Open the haproxy node's IP address on port 80 to visit the haproxy statistics, including your postgresql backend.
6. Party!

## Disabling the Ansible provisioning

In case you do not want Ansible to install teleport (because you want to install it yourself), just comment out the following lines in the `Vagrantfile`:
```
        node.vm.provision "ansible" do |ansible|
          ansible.compatibility_mode = "2.0"
          ansible.limit = "all"
          ansible.groups = {
            "patroni_nodes"  => [ "patroni1", "patroni2", "patroni3" ],
            "etcd_nodes"  => [ "patroni1", "patroni2", "patroni3" ],
            "haproxy_server"  => [ "haproxy" ],
          }
          ansible.playbook = "ansible/playbook-vagrant.yml"

        end # node.vm.provision
```
