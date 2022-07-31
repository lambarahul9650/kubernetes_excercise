
Vagrant.configure("2") do |config|
    
    config.vm.define "k8_master" do |k8_master|
        k8_master.vm.box = "centos/7"
        k8_master.vm.network "private_network", ip: "172.16.10.4"
        k8_master.vm.provider "virtualbox" do |vb|
            vb.memory = 2048
            vb.cpus = 2
        end
    end

    config.vm.define "worker_1" do |worker_1|
        worker_1.vm.box = "centos/7"
        worker_1.vm.network "private_network", ip: "172.16.10.5"
        worker_1.vm.provider "virtualbox" do |vb|
            vb.memory = 1024
            vb.cpus = 1
        end
    end
end

    