$common_script = <<-SCRIPT
  #! /bin/sh
  echo "###############Running Common Script ################################"
    #ln -s /usr/share/zoneinfo/Europe/London  /etc/localtime

    apt-get update
    apt-get install git curl vim lsof iputils-ping net-tools -y
    curl -sfL https://get.k3s.io | sh -
    chmod +x /usr/local/bin/k3s 
    systemctl stop k3s  

    echo "###############Installing Nodeexporter ################################"
    adduser --disabled-password --gecos "" prometheus
    cd /home/prometheus
    curl -LO "https://github.com/prometheus/node_exporter/releases/download/v0.17.0/node_exporter-0.17.0.linux-386.tar.gz"
    tar -xvzf node_exporter-0.17.0.linux-386.tar.gz
    mv node_exporter-0.17.0.linux-386 node_exporter
    cd node_exporter
    chown prometheus:prometheus node_exporter

    cat  > /etc/systemd/system/node_exporter.service <<EOF
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
    kubectl apply -f https://raw.githubusercontent.com/shridharMe/IOT/master/kube/kubernetes-dashboard.yaml
    kubectl -n kube-system get service kubernetes-dashboard
    echo $(kubectl get secret -n kube-system $(kubectl get serviceaccount kubernetes-dashboard -n kube-system -o jsonpath="{.secrets[0].name}") -o jsonpath="{.data.token}" | base64 --decode)
  SCRIPT

Vagrant.configure("2") do |config| 
   config.vm.define "k3-master" do |master|
    ### Configure k3 master################################################
    master.vm.box = "envimation/ubuntu-xenial"
    master.vm.hostname = "k3-master"    
    master.vm.provider :virtualbox do |vb|
      vb.name = "k3-master"
      vb.memory = 1024       
      vb.cpus = 2
    end    
    master.vm.network "public_network"#, ip: "192.168.1.62"
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
    node.vm.network "public_network"#, ip: "192.168.1.72"
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
    node.vm.network "public_network" #, ip: "192.168.1.82"
    node.vm.provision "shell",inline: $common_script  
  end
end
