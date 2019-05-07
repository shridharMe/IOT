$common_script = <<-SCRIPT
  #! /bin/sh
  echo "###############Running Common Script ################################"
    #ln -s /usr/share/zoneinfo/Europe/London  /etc/localtime

    apt-get update
    apt-get install git curl vim lsof iputils-ping -y
    curl -sfL https://get.k3s.io | sh -
    chmod +x /usr/local/bin/k3s 
    systemctl stop k3s  

    echo "###############Installing Nodeexporter ################################"
    adduser prometheus
    cd /home/prometheus
    curl -LO "https://github.com/prometheus/node_exporter/releases/download/v0.16.0/node_exporter-0.16.0.linux-amd64.tar.gz"
    tar -xvzf node_exporter-0.16.0.linux-amd64.tar.gz
    mv node_exporter-0.16.0.linux-amd64 node_exporter
    cd node_exporter
    chown prometheus:prometheus node_exporter

    cat  /etc/systemd/system/node_exporter.service >> EOF
    [Unit]
    Description=Node Exporter

    [Service]
    User=prometheus
    ExecStart=/home/prometheus/node_exporter/node_exporter

    [Install]
    WantedBy=default.target
    EOF
    systemctl daemon-reload
    systemctl enable node_exporter.service
    systemctl start node_exporter.service
 SCRIPT

$ks3_master_script = <<-SCRIPT
  #! /bin/sh  
    systemctl start k3s   
    kubectl  create clusterrolebinding dashboard-admin -n kube-system --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
    kubectl -n kube-system get service kubernetes-dashboard
    echo $(kubectl get secret -n kube-system $(kubectl get serviceaccount kubernetes-dashboard -n kube-system -o jsonpath="{.secrets[0].name}") -o jsonpath="{.data.token}" | base64 --decode)
    git clone https://github.com/shridharMe/content-kubernetes-prometheus-env.git /vagrant
   SCRIPT

Vagrant.configure("2") do |config| 
  config.vm.network :forwarded_port, guest:30000, host: 30000  
  config.vm.network :forwarded_port, guest: 32000, host: 32000
  config.vm.network :forwarded_port, guest: 32767, host: 32767
  config.vm.network :forwarded_port, guest: 6443, host: 6443  
  config.vm.define "k3-master" do |master|
    ### Configure k3 master################################################
    master.vm.box = "envimation/ubuntu-xenial"
    master.vm.hostname = "k3-master"    
    master.vm.provider :virtualbox do |vb|
      vb.name = "k3-master"
      vb.memory = 1024       
      vb.cpus = 2
    end    
    master.vm.network "public_network" , ip: "192.168.1.62"
    master.vm.provision "shell",inline: $common_script  
    master.vm.provision "shell",inline: $ks3_master_script
  end

    config.vm.define "iot-node1" do |node|
    ### Configure node1################################################
    node.vm.box = "envimation/ubuntu-xenial"
    node.vm.hostname = "iot-node1"    
    node.vm.provider :virtualbox do |vb|
      vb.name = "iot-node1"
      vb.memory = 512       
      vb.cpus = 2
    end    
    node.vm.network "public_network" , ip: "192.168.1.72"
    node.vm.provision "shell",inline: $common_script  
  end

    config.vm.define "iot-node2" do |node|
    ### Configure node2################################################
    node.vm.box = "envimation/ubuntu-xenial"
    node.vm.hostname = "iot-node2"    
    node.vm.provider :virtualbox do |vb|
      vb.name = "iot-node2"
      vb.memory = 512       
      vb.cpus = 2
    end    
    node.vm.network "public_network" , ip: "192.168.1.82"
    node.vm.provision "shell",inline: $common_script  
  end
end
