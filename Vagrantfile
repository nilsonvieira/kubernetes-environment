IMAGE_NAME = "bento/ubuntu-22.04"
NUM_NODES = 2
MASTERS = 3
ETCD = 1
R_REFLETOR = 1
NET_IP="192.168.50."
INIT_IP=1

Vagrant.configure(2) do |config|

    Vagrant.configure(2) do |config|
        config.vm.provision "file", source: ".ssh/id_rsa.pub", destination: "~/.ssh/me.pub"
        config.vm.provision "shell", env:{"NET_IP" => NET_IP, "INIT_IP" => INIT_IP}, inline: <<-SCRIPT
        apt-get update -y && apt install -y vim net-tools telnet git
        cat /home/vagrant/.ssh/me.pub >> /home/vagrant/.ssh/authorized_keys
        echo "$NET_IP$((INIT_IP)) master-node" >> /etc/hosts
        echo "$NET_IP$((INIT_IP+1)) worker-node01" >> /etc/hosts
        echo "$NET_IP$((INIT_IP+2)) worker-node02" >> /etc/hosts
    SCRIPT
    end

    config.vm.box = IMAGE_NAME
    config.vm.box_check_update = true

    (1..MASTERS).each do |i|
        config.vm.define "master-#{i}" do |master|
            master.vm.hostname = "k8s-master-#{i}"
            master.vm.network "private_network", ip: NET_IP + "#{INIT_IP + 10}"
            master.vm.provider "virtualbox" do |vb|
                vb.name    = "k8s-master-#{i}"
                vb.memory = 1024
                vb.cpus = 1
            end
        end
    end

    (1..NUM_NODES).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.hostname = "k8s-node"
            node.vm.network "private_network", ip: NET_IP + "#{INIT_IP + 20}"
            node.vm.provider "virtualbox" do |vb|
                vb.name    = "k8s-node-#{i}"
                vb.memory = 2048
                vb.cpus = 2
            end
        end
    end

    (1..ETCD).each do |i|
        config.vm.define "etcd-#{i}" do |etcd|
            etcd.vm.hostname = "k8s-etcd-#{i}"
            etcd.vm.network "private_network",  ip: NET_IP + "#{INIT_IP + 30}"
            etcd.vm.provider :virtualbox do |vb|
                vb.name    = "k8s-etcd-#{i}"
                vb.memory = 1024
                vb.cpus = 1
            end
        end
    end
  

    (1..R_REFLETOR).each do |i|
        config.vm.define "calico-#{i}" do |calico|
            calico.vm.network "private_network",  ip: NET_IP + "#{INIT_IP + 40}"
            calico.vm.hostname = "k8s-calico-#{i}"
            calico.vm.provider :virtualbox do |vb|
                vb.name    = "k8s-calico-#{i}"
                vb.memory = 1024
                vb.cpus = 1
            end
        end
    end
end