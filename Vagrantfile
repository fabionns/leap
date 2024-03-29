# kuberverse k8s lab provisioner
# type: kubeadm-calico-full-cluster-bootstrap
# created by Artur Scheiner - artur.scheiner@gmail.com
# dependencies: https://raw.githubusercontent.com/arturscheiner/kuberverse/master/labs/kv-k8s-cluster/common.sh
#               https://raw.githubusercontent.com/arturscheiner/kuberverse/master/labs/kv-k8s-cluster/master.sh
#               https://raw.githubusercontent.com/arturscheiner/kuberverse/master/labs/kv-k8s-cluster/worker.sh

BOX_IMAGE = "bento/ubuntu-18.04"
MASTER_COUNT = 1
WORKER_COUNT = 2
POD_CIDR = "172.18.0.0/16"
API_ADV_ADDRESS = "10.8.8.10"
KVMSG = "Kuberverse"
COMMON_SCRIPT_URL = "https://raw.githubusercontent.com/arturscheiner/kuberverse/master/labs/kv-k8s-cluster/common.sh"
MASTER_SCRIPT_URL = "https://raw.githubusercontent.com/arturscheiner/kuberverse/master/labs/kv-k8s-cluster/master.sh"
WORKER_SCRIPT_URL = "https://raw.githubusercontent.com/arturscheiner/kuberverse/master/labs/kv-k8s-cluster/worker.sh"

Vagrant.configure("2") do |config|

  # Installing the necessary packages for this provisioner  
  config.vm.provision "shell" do |s|
      s.inline = <<-SCRIPT
         mkdir -p /home/vagrant/.kv
         wget -q #{COMMON_SCRIPT_URL} -O /home/vagrant/.kv/common.sh
         chmod +x /home/vagrant/.kv/common.sh
         /home/vagrant/.kv/common.sh #{KVMSG}
      SCRIPT
  end

  (0..MASTER_COUNT-1).each do |i|
    config.vm.define "kv-master-#{i}" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.hostname = "kv-master-#{i}"
      subconfig.vm.network :private_network, ip: "10.8.8.#{i + 10}"
      subconfig.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--cpus", 2]
        vb.memory = 2048
      end

      # This if is here just to remember me to create a multi-master cluster
      # the behavior and overal configuration is different and this will require a HAProxy installation
      if i == 0

        subconfig.vm.provision "shell" do |s|
          s.inline = <<-SCRIPT
            mkdir -p /home/vagrant/.kv
            wget -q #{MASTER_SCRIPT_URL} -O /home/vagrant/.kv/master.sh
            chmod +x /home/vagrant/.kv/master.sh
            /home/vagrant/.kv/master.sh #{KVMSG} #{i} #{POD_CIDR} #{API_ADV_ADDRESS}
          SCRIPT
        end

      end

    end
  end
  
  (0..WORKER_COUNT-1).each do |i|
    config.vm.define "kv-worker-#{i}" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.hostname = "kv-worker-#{i}"
      subconfig.vm.network :private_network, ip: "10.8.8.#{i + 20}"
      subconfig.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--cpus", 2]
        vb.memory = 1024
      end

      subconfig.vm.provision "shell" do |s|
        s.inline = <<-SCRIPT
          mkdir -p /home/vagrant/.kv
          wget -q #{WORKER_SCRIPT_URL} -O /home/vagrant/.kv/worker.sh
          chmod +x /home/vagrant/.kv/worker.sh
          /home/vagrant/.kv/worker.sh #{KVMSG} #{i} #{POD_CIDR} #{API_ADV_ADDRESS}
        SCRIPT
      end

    end
  end

  config.vm.provision "shell",
   run: "always",
   inline: "swapoff -a"

end