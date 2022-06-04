# Setup Kubernetes Cluster in Ubuntu using Kubeadm


**Pre-Requistes:**
1. Ubuntu instance with  4 GB RAM - Master Node  - (with ports open to all traffic)
2. Ubuntu instance with at least  2 GB RAM - Worker Node  - (with ports open to all traffic)


**Kubernetes Setup using Kubeadm**
*Start - Execute the below commands in both Master/worker nodes.*

**Login to both instances execute the below commands:**

> sudo apt-get update -y && sudo apt-get install apt-transport-https -y

**Change to root user**

> sudo su -
> sudo curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -

cat <<EOF >/etc/apt/sources.list.d/kubernetes.list  
deb https://apt.kubernetes.io/ kubernetes-xenial main  
EOF

> sudo apt-get update

**Disable swap memory for better performance**

> swapoff -a  
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

**Enable IP tables**
*We need to enable IT tables for pod to pod communication.*

> modprobe br_netfilter  
sysctl -p  
sudo sysctl net.bridge.bridge-nf-call-iptables=1

**Install Docker on both Master and Worker nodes**  

> apt-get install docker.io -y

**Add ubuntu user to Docker group**

> usermod -aG docker ubuntu
> systemctl restart docker
> systemctl enable docker.service

Type exit to come out of root user.  

**Install Kubernetes Modules**  

> sudo apt-get install -y kubelet kubeadm kubectl kubernetes-cni
> sudo systemctl daemon-reload
> sudo systemctl start kubelet
> sudo systemctl enable kubelet.service
> sudo systemctl status docker
**End - Execute the above commands in both Master/worker nodes**

**Initialize Kubeadm on Master Node(only on Master Node)**  
  *Execute the below command as root user to initialize Kubernetes Master node.*  
> sudo su -
> kubeadm init

*Make sure you see the above message to confirm master node is up.*  
*Now type exit to exit from root user and execute below commands as normal user*  

  

> mkdir -p $HOME/.kube
> sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
> sudo chown $(id -u):$(id -g) $HOME/.kube/config


**Installing the Weave Net Add-On**  
> kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"

*It make take a few mins to execute the above command and show show the below message.
Now execute the below command to see the pods.*

> kubectl get pods --all-namespaces

