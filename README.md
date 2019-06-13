# Kubernetes_setup
## Steps to install Kubernetes Cluster
<ol>
<strong>Requirements</strong></br>

Master:</br>
2 GB RAM</br>
2 Cores of CPU </br></br>
Slave/Node:</br>
1 GB RAM</br>
1 Core of CPU</br>


</br>

----------------------------------------------------------------------------------------------------------------------------

</br>
## Install Kubernetes

The following steps have to be executed on both the master and node machines. Let’s call the the master as ‘kmaster‘ and node as ‘knode‘. </br>
<ol>
<li><strong>Change to root:</strong></li>
Because, the following set of commands need to be executed with ‘sudo’ permissions.</br>
<strong>
$ sudo su</br>
# apt-get update</br>
</strong></br>
<li><strong>Turn Off Swap Space: </strong></li>
Kubernetes doesn't support "swap".</br>
<strong># swapoff -a</strong></br>

After that you need to open the ‘fstab’ file and comment out the line which has mention of swap partition.</br>
<strong># nano /etc/fstab</strong></br> 

</br>  

![1 1](https://user-images.githubusercontent.com/39157936/59428284-707b9b00-8dfa-11e9-9d7a-02c46c04ee32.png)
</br>

Then press ‘Ctrl+X’, then press ‘Y’ and then press ‘Enter’ to Save the file.</br>

<li><strong>Update The Hostnames:</strong></li>

To change the hostname of both machines, run the below command to open the file and subsequently rename the master machine to ‘kmaster’ and your node machine to ‘knode’. </br>
<strong># nano /etc/hostname</strong></br>

</br>    

![1 2](https://user-images.githubusercontent.com/39157936/59428282-707b9b00-8dfa-11e9-97c0-76071fed81f0.png)  
</br>

Then press ‘Ctrl+X’, then press ‘Y’ and then press ‘Enter’ to Save the file. </br>   

<li><strong>Update The Hosts File With IPs Of Master & Node:</strong></li>

Run the following command on both machines to note the IP addresses of each.</br>
<strong># ifconfig</strong></br>

</br>    

![1 3](https://user-images.githubusercontent.com/39157936/59428281-6fe30480-8dfa-11e9-9b2f-848ce1dd15e9.png)  
</br>

Now go to the ‘hosts’ file on both the master and node and add an entry specifying their respective IP addresses along with their names ‘kmaster’ and ‘knode’. This is used for referencing them in the cluster. It should look like the below screenshot on both the machines. </br>
<strong># nano /etc/hosts </strong></br>

</br>    

![1 4](https://user-images.githubusercontent.com/39157936/59428280-6fe30480-8dfa-11e9-8548-789a2c513f9e.png)  
</br>

Then press ‘Ctrl+X’, then press ‘Y’ and then press ‘Enter’ to Save the file.</br>

<li><strong>Setting Static IP Addresses:</strong></li>

We will make the IP addresses used above, static for the VMs. We can do that by modifying the network interfaces file. Run the following command to open the file:</br>
<strong># nano /etc/network/interfaces</strong></br>

Now enter the following lines in the file.</br>
<strong>
auto enp0s8</br>
iface enp0s8 inet static</br>
address <IP-Address-Of-VM></br>
</strong></br>  
</br>  

![1 5](https://user-images.githubusercontent.com/39157936/59428279-6fe30480-8dfa-11e9-9fbd-972a9d1e6c9c.png)
</br>

Then press ‘Ctrl+X’, then press ‘Y’ and then press ‘Enter’ to Save the file.</br>

After this, restart your machine.</br>

<li><strong>Install OpenSSH-Server:</strong></li>
Now we have to install openshh-server. Run the following command:</br>
<strong># sudo apt-get install openssh-server</strong> </br>
</ol>
********************************************************
<li><strong>Install Docker</strong></li>

Now we have to install Docker because Docker images will be used for managing the containers in the cluster. Run the following commands: </br>

<strong># sudo su</strong></br>
<strong># apt-get update </strong></br>
<strong># apt-get install -y docker.io</strong></br>

Next we have to install these 3 essential components for setting up Kubernetes environment: kubeadm, kubectl, and kubelet.</br>

Run the following commands before installing the Kubernetes environment.</br>

<strong># apt-get update && apt-get install -y apt-transport-https curl</strong></br>
<strong># curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -</strong></br>
<strong># cat <<EOF >/etc/apt/sources.list.d/kubernetes.list</strong></br>
deb http://apt.kubernetes.io/ kubernetes-xenial main</br>
EOF</strong></br>
<strong># apt-get update</strong></br>

<li><strong>Install kubeadm, Kubelet And Kubectl </strong></li>

Now its time to install the 3 essential components. Kubelet is the lowest level component in Kubernetes. It’s responsible for what’s running on an individual machine. Kuebadm is used for administrating the Kubernetes cluster. Kubectl is used for controlling the configurations on various nodes inside the cluster.</br>

<strong># apt-get install -y kubelet kubeadm kubectl</strong></br>

<li><strong>Updating Kubernetes Configuration</strong></li>

Next, we will change the configuration file of Kubernetes. Run the following command:</br>

<strong>#nano /etc/systemd/system/kubelet.service.d/10-kubeadm.conf</strong></br>
This will open a text editor, enter the following line after the last “Environment Variable”:</br>

<strong>Environment="cgroup-driver=systemd/cgroup-driver=cgroupfs"</strong></br>  
</br>  

![1 6](https://user-images.githubusercontent.com/39157936/59428278-6f4a6e00-8dfa-11e9-9e5a-4bd6195be2be.png)  
</br>
Now press Ctrl+X, then press Y, and then press Enter to Save.</br>

---------------------------------------------------------------------------------------------------------------------------
<li><strong>Steps Only For Kubernetes Master VM (kmaster)</strong></li>
<ol>
<li><strong>All the required packages are installed on both servers. Now, it's time to configure Kubernetes Master Node.</strong></li>

First, initialize your cluster using its private IP address with the following command:</br>
<li><strong>We will now start our Kubernetes cluster from the master’s machine.</strong></li>
<strong># kubeadm init --apiserver-advertise-address=<ip-address-of-kmaster-vm> --pod-network-cidr=192.168.0.0/16</strong></br>
<strong># kubeadm init --apiserver-advertise-address 192.168.1.206 --pod-network-cidr=172.16.0.0/16</strong></br>  
</br>  

![1 7](https://user-images.githubusercontent.com/39157936/59428277-6f4a6e00-8dfa-11e9-9a3b-0aca0cfb8d34.png)  
</br>
<li><strong>As mentioned before, run the commands from the above output as a non-root user</strong></li>

<strong>$ mkdir -p $HOME/.kube</strong></br>
<strong>$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config</strong></br>
<strong>$ sudo chown $(id -u):$(id -g) $HOME/.kube/config</strong></br>  
</br>  
 
![1 8](https://user-images.githubusercontent.com/39157936/59428275-6f4a6e00-8dfa-11e9-9177-d92286388b95.png)  
</br>
<li><strong>You will notice from the previous command, that all the pods are running except one: ‘kube-dns’. For resolving this we will install a pod network. To install the CALICO pod network, run the following command:</strong></li>

<strong>$ kubectl apply -f https://docs.projectcalico.org/v3.0/getting-started/kubernetes/installation/hosted/kubeadm/1.7/calico.yaml</strong> </br>

<li><strong>install network add-on to enable the communication between the pods only on master nodes</strong></li>

<strong>$ kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml</strong></br>  
</br>  

![1 9](https://user-images.githubusercontent.com/39157936/59428274-6f4a6e00-8dfa-11e9-87c7-6f59c55ea1b5.png)
</br>  
  
<li><strong>To verify, if kubectl is working or not, run the following command:</strong></li>
<strong>$ kubectl get pods -o wide --all-namespaces</strong></br>  
</br>  

![1 10](https://user-images.githubusercontent.com/39157936/59428273-6eb1d780-8dfa-11e9-81fb-fa335eaff6d4.png)
 </br> 
  
use "kubectl get nodes" command to ensure the kubernetes master node status is ready.</br>
<strong>$ kubectl get nodes</strong></br>   
</br>  

![1 11](https://user-images.githubusercontent.com/39157936/59428272-6eb1d780-8dfa-11e9-81e7-8bc42123b54e.png)
</br>  
  
To uninstall kubernetes</br>
<strong>$ kubeadm reset</strong></br>

----------------------------------------------------------------------------------------------------------------------------
<li><strong> Installing the Kubernetes Dashboard</strong></li>

Uses of Kubernetes Dashboard</br>
<ol>
To get an overview of applications running on your cluster.</br>
To create or modify the individual Kubernetes resources, e.g. deployments, jobs, etc</br>
It provides the information on the state of Kubernetes resources in your cluster, and on any errors that may have occurred.</br>

</ol>
<ol>
<li><strong>To deploy the Kubernetes dashboard</strong></li>

<strong>kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml </strong></br>  
</br>  

![1 12](https://user-images.githubusercontent.com/39157936/59428271-6eb1d780-8dfa-11e9-99ab-bd4ddf47b795.png)  
</br>

<li><strong>Deploy heapster to enable container cluster monitoring and performance analysis on your cluster:</strong></li>

<strong>kubectl apply -f https://raw.githubusercontent.com/kubernetes/heapster/master/deploy/kube-config/influxdb/heapster.yaml </strong></br>  

</br>  

![1 13](https://user-images.githubusercontent.com/39157936/59428270-6e194100-8dfa-11e9-8985-f0f6d96d5c2f.png)
</br>  
  
<li><strong>Deploy the influxdb backend for heapster to your cluster:</strong></li>
<strong>kubectl apply -f https://raw.githubusercontent.com/kubernetes/heapster/master/deploy/kube-config/influxdb/influxdb.yaml </strong></br>   

</br>  

![1 14](https://user-images.githubusercontent.com/39157936/59428269-6e194100-8dfa-11e9-921c-52db70515437.png)  
</br>

<li><strong>Create the heapster cluster role binding for the dashboard:</strong></li>
<strong>kubectl apply -f https://raw.githubusercontent.com/kubernetes/heapster/master/deploy/kube-config/rbac/heapster-rbac.yaml </strong></br>    

</br>  

![1 15](https://user-images.githubusercontent.com/39157936/59428268-6e194100-8dfa-11e9-9cdf-a42887d901e6.png)  
</br>
</ol>
<li><strong> Connect to the Dashboard</strong></li>
<ol>
<li><strong>To connect to the Kubernetes dashboard</strong></li>
Retrieve an authentication token for the eks-admin service account. Copy the <authentication_token> value from the output. You use this token to connect to the dashboard.</br>
<strong> $ kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep eks-admin | awk '{print $1}') </strong></br>    
</br>  

![1 16](https://user-images.githubusercontent.com/39157936/59428267-6d80aa80-8dfa-11e9-99f7-ce305b6b5dbb.png)  
</br>
<li><strong>Start the kubectl proxy.</strong></li>
<strong> $ kubectl proxy</strong></br>

It will proxy the server between your machine and Kubernetes API server.</br>


<li><strong>Open the following link with a web browser to access the dashboard endpoint:</strong></li>
<strong>http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login </strong></br>


Choose Token, paste the <authentication_token> output from the previous command into the Token field, and choose SIGN IN.</br>  
</br>  

![1 17](https://user-images.githubusercontent.com/39157936/59428266-6d80aa80-8dfa-11e9-98ac-d2612b11de9f.png)  
</br>
</ol>
----------------------------------------------------------------------------------------------------------------------------

<li><strong>Steps For Only Kubernetes Node VM (knode)</strong></li>
<ol>
For trial purpose, we can create nodes in same system with the help of virtual machine.</br>
Prerequisites</br>
<strong>1.3GHz or faster 64-bit processor </strong></br>
<strong>2 GB RAM minimum/ 4GB RAM or more recommended </strong></br>
install vmware workstation player on ubuntu
<li><strong>1.Install required packages</strong></li>
<ol>
<strong>$ sudo apt update</strong></br>
<strong>$ sudo apt install build-essential</strong></br>
<strong>$ sudo apt install linux-headers-$(uname -r)</strong></br>
</ol>

<li><strong>2.Download vmware workstation player</strong></li>
<strong>$ wget https://www.vmware.com/go/getplayer-linux</strong></br>

Once the download is completed make the installation file executable using the following command:</br>

<strong>$ chmod +x getplayer-linux</strong></br>

<li><strong>3.install vmware workstation player</strong></li>
Start the Installation wizard by typing:</br>
<strong>$ sudo ./getplayer-linux</strong></br>

<li><strong>4.Accept the terms in the license agreement and click on the Next button.</strong></li>

<li><strong>5.Next, you will be asked whether you like to check for product updates on startup. Make your selection and click on the Next button.</strong></li>

<li><strong>6.VMware’s Customer Experience Improvement Program (“CEIP”) helps VMware to improve their products and services by sending anonymous system data and usage information to VMware. If you prefer not to participate in the program select No and click on the Next button</strong></li>

<li><strong>7.In next step ,If you don’t have a license key, leave the field empty and click on the Next button.</strong></li>

<li><strong>8.Next, you will see the following page informing you that the VMware Workstation Player is ready to be installed. Click on the Install button.</strong></li>

<li><strong>9.Start VMware Workstation Player</strong></li>
Create a new virtual machine</br>  

</br>  

![1 18](https://user-images.githubusercontent.com/39157936/59428264-6d80aa80-8dfa-11e9-9d0e-7cbdcba46320.png)  
</br>

</ol>

<li><strong> Open terminal in virtual system and follow the step to create user(knode) and enter command to make connection between master and node.</strong></li>
<strong>$ sudo su</strong></br>
<strong># kubeadm join 192.168.1.206:6443 --token 02p54b.p8oe045cpj3zmz2b --discovery-token-ca-cert-hash sha256:50ba20a59c9f8bc0559d4635f1ac6bb480230e173a0c08b338372d8b81fcd061 </strong></br>  

</br>  

![1 19](https://user-images.githubusercontent.com/39157936/59428263-6ce81400-8dfa-11e9-891d-0b95eaf9df7d.png)  
</br>


once worker node is joined with kubernetes master, then verify the list of nodes within the kubernetes cluster.   
<strong># kubectl get nodes</strong></br>  

</br>  

![1 20](https://user-images.githubusercontent.com/39157936/59428262-6ce81400-8dfa-11e9-98ed-0a3ffd576f53.png)  
</br> 

we have successfully configured the kubernetes cluster.</br>
kubernetes master and worker node is ready to deploy the application.</br>

</ol>
