$common_script = <<-SCRIPT
  #! /bin/sh
  echo "###############Running Common Script ################################"
    #ln -s /usr/share/zoneinfo/Europe/London  /etc/localtime
     apt-get update
     apt-get install curl -y
     apt-get install vim -y
     apt-get install lsof -y
     apt-get install iputils-ping -y
     curl -sfL https://get.k3s.io | sh -
     chmod +x /usr/local/bin/k3s 
     systemctl stop k3s
     rm - /var/lib/rancher/k3s/server/manifests/traefik.yaml         
 SCRIPT

$ks3_master_script = <<-SCRIPT
  #! /bin/sh  
    systemctl start k3s
    kubectl label nodes k3-master dedicated=master    
    kubectl create 
kind: Service
metadata:
  name: nginx-ingress
spec:
  type: NodePort
  ports:
    - port: 80
      nodePort: 30000
      name: http
    - port: 18080
      nodePort: 32000
      name: http-mgmt
  selector:
    app: nginx-ingress-lbapiVersion: v1
kind: Service
metadata:
  name: nginx-ingress
spec:
  type: NodePort
  ports:
    - port: 80
      nodePort: 30000
      name: http
    - port: 18080
      nodePort: 32000
      name: http-mgmt
  selector:
    app: nginx-ingress-lbps://raw.githubusercontent.com/shridharMe/IOT/master/kubernetes-dashboard.yaml  
    k3s kubectl create clusterrolebinding dashboard-admin -n kube-system --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
    k3s kubectl delete -f /var/lib/rancher/k3s/server/manifests/traefik.yaml
    #k3s  kubectl create -f /vagrant/kube/traefik/
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
    master.vm.network "public_network" , ip: "192.168.1.62"
    master.vm.provision "shell",inline: $common_script  
    master.vm.provision "shell",inline: $ks3_master_script
  end
end
