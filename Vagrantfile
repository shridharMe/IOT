$common_script = <<-SCRIPT
  #! /bin/sh
  echo "###############Running Common Script ################################"
     apt-get update
     apt-get install curl -y
     apt-get install vim -y
     apt-get install iputils-ping -y
      curl -sfL https://get.k3s.io | sh -
     chmod +x /usr/local/bin/k3s  
     service k3s stop
     sleep 60
  echo "###############End of common Script ################################"
 SCRIPT

$ks3_master_script = <<-SCRIPT
  #! /bin/sh  
  echo "###############Running k3 master Script ################################"
   k3s server &
  echo "######## Adding dashboard #####"
   k3s  kubectl create -f /vagrant/kubernetes-dashboard.yaml  
   k3s kubectl create clusterrolebinding dashboard-admin -n kube-system --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
  echo "###############End of k3 master Script ################################"
  SCRIPT

Vagrant.configure("2") do |config|
  #config.ssh.username = "root"
  #config.ssh.password = "vagrant"  
 
  config.vm.define "k3-master" do |master|
    ### Configure k3 master################################################
    master.vm.box = "envimation/ubuntu-xenial"
    master.vm.hostname = "k3-master"    
    master.vm.provider :virtualbox do |vb|
      vb.name = "k3-master"
      vb.memory = 1024       
      #vb.cpus = 2
    end    
    master.vm.network "public_network" #, ip: "192.168.0.17"
    master.vm.provision "shell",inline: $common_script  
    master.vm.provision "shell",inline: $ks3_master_script
  end

  ####Configuring Node 1 ##################################################
  config.vm.define "iot-node1" do |node1|
    node1.vm.box = "envimation/ubuntu-xenial"
    node1.vm.hostname = "iot-node1"
    node1.vm.network "public_network", ip: "192.168.0.18"
    node1.vm.provider :virtualbox do |vb|
      vb.name = "iot-node1"
      vb.memory = 500
      #vb.cpus = 2
    end
    node1.vm.provision "shell", inline: $common_script
  end

  ####Configuring Node 2 ##################################################
  config.vm.define "iot-node2" do |node2|
    node2.vm.box = "envimation/ubuntu-xenial"
    node2.vm.hostname = "iot-node2"
    node2.vm.network "public_network", ip: "192.168.0.19"
    node2.vm.provider :virtualbox do |vb|
      vb.name = "iot-node2"
      vb.memory = 500
    end
    node2.vm.provision "shell", inline: $common_script

  end

end
