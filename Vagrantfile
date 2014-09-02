# -*- mode: ruby -*-
# vi: set ft=ruby :


Vagrant.configure("2") do |config|

    ## config.vm.box = "saucy64"
    ## config.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/saucy/current/saucy-server-cloudimg-amd64-vagrant-disk1.box"
    config.vm.box = "trusty64"
    config.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"
    #
    # eth1, this will be the endpoint
    config.vm.network :private_network, ip: "192.168.27.100"
    # eth2, this will be the OpenStack "public" network, use DevStack default
    config.vm.network :private_network, ip: "172.24.4.225", :netmask => "255.255.255.224", :auto_config => false
    config.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--memory", 4096]
       	# eth2 must be in promiscuous mode for floating IPs to be accessible
       	vb.customize ["modifyvm", :id, "--nicpromisc3", "allow-all"]
    end
    config.vm.provision :ansible do |ansible|
        ansible.host_key_checking = false
        ## ansible.raw_ssh_args = ['-o StrictHostKeyChecking=no', '-o UserKnownHostsFile=/dev/null', '-o ControlMaster=auto']
        ansible.playbook = "devstack.yaml"
        ## ansible.verbose = "v"
        ansible.verbose = "vvvv"
    end
    config.vm.provision :shell, :inline => "cd devstack; sudo -u vagrant env HOME=/home/vagrant ./stack.sh"
    config.vm.provision :shell, :inline => "ovs-vsctl add-port br-ex eth2"

    # Workaround for https://bugs.launchpad.net/devstack/+bug/1243075
    config.vm.provision :ansible do |ansible|
        ansible.host_key_checking = false
        ansible.playbook = "horizon-workaround.yaml"
        ansible.verbose = "v"
    end
end
