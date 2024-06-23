## Steps to configure K8s on AWS with Kubeadm


> [!NOTE]
> Master and worker needs a minimum of 2 GB RAM





### Reference
[Installing kubeadm in Linux](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

Complete Setup [Video](https://www.youtube.com/watch?v=UbiVOMkXjr8&t=1s) from YT


> [!NOTE]
> ### Steps (Which is working)

+ Install Docker Engine
  + on [Ubuntu](https://docs.docker.com/engine/install/ubuntu/)
+ Install CRI runtime 
  + [cri-dockerd](https://mirantis.github.io/cri-dockerd/usage/install/)
  + Download the deb file corresponding to the distro release version
  + Eg: cri-dockerd_0.3.14.3-0.ubuntu-jammy_amd64.deb
  + sudo dpkg -i cri-dockerd_0.3.14.3-0.ubuntu-jammy_amd64.deb
+ Install kubeadm, kubelet and kubectl
  + Official documentation for [Ubuntu](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
+ Install Calico on Master node
  + Officila [documentation](https://docs.tigera.io/calico/latest/getting-started/kubernetes/self-managed-onprem/onpremises)




### Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

### Add the repository to Apt sources:
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update


sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-compose -y

```

[containerd](https://github.com/Mirantis/cri-dockerd/releases)


`wget https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.14/cri-dockerd_0.3.14.3-0.ubuntu-jammy_amd64.deb`


`dpkg -i cri-dockerd_0.3.14.3-0.ubuntu-jammy_amd64.deb`


### Install kubeadm, kubelet and kubectl



```
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
sudo systemctl enable --now kubelet

```


`kubeadm init --pod-network-cidr=192.168.0.0/16 --cri-socket=unix:///var/run/cri-dockerd.sock`





### Install Calico on Master node

Select the **Manifest** tab

```
curl https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/calico.yaml -O
kubectl apply -f calico.yaml


```








> [!CAUTION]
> ### Steps (from previous installation)


`sudo apt-get update`
> apt-transport-https may be a dummy package; if so, you can skip that package
`sudo apt-get install -y apt-transport-https ca-certificates`

> If the directory `/etc/apt/keyrings` does not exist, it should be created before the curl command, read the note below.
> sudo mkdir -p -m 755 /etc/apt/keyrings
`curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg`


> This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
`echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list`
```
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

sudo systemctl enable --now kubelet


kubeadm init --pod-network-cidr=192.168.0.0/16

```

This command results an error about CRI runtime. Hence run the command with adding runtime flag

> root@Master % kubeadm init --pod-network-cidr=192.168.0.0/16
> Found multiple CRI endpoints on the host. Please define which one do you wish to use by setting the 'criSocket' field in the kubeadm configuration file: unix:///var/run/containerd/containerd.sock, unix:///var/run/cri-dockerd.sock
> To see the stack trace of this error execute with --v=5 or higher

`kubeadm init --pod-network-cidr=192.168.0.0/16 --cri-socket=unix:///var/run/cri-dockerd.sock`





> then, run the stesp mentioned in the output
```
mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

`kubectl get cs`








> [!NOTE]
> Only for Master node

# Install Calico

Oficila Calico installation reference [URL](https://docs.tigera.io/calico/latest/getting-started/kubernetes/self-managed-onprem/onpremises)

```
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/tigera-operator.yaml
curl https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/custom-resources.yaml -O
kubectl create -f custom-resources.yaml
watch kubectl get pods -n calico-system


curl https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/calico.yaml -O
kubectl apply -f calico.yaml

kubectl get pods -n kube-system
```





`kubeadm token create --print-join-command`

> use sudo when running and add --cri-socket= if necessary


> [!NOTE]
> Only for worker node's

> root@Worker-1 % kubeadm join 172.31.16.216:6443 --token lh26qj.557l2heit3uq28oi --discovery-token-ca-cert-hash sha256:ba25b7f932ca12f8de9b4afd94daaa755de4af70956c30dbbd02ff14353f6293 --cri-socket=unix:///var/run/cri-dockerd.sock
> [preflight] Running pre-flight checks
> [preflight] Reading configuration from the cluster...
> [preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
> [kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
> [kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
> [kubelet-start] Starting the kubelet
> [kubelet-check] Waiting for a healthy kubelet. This can take up to 4m0s
> [kubelet-check] The kubelet is healthy after 502.241692ms
> [kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap

> This node has joined the cluster:
> * Certificate signing request was sent to apiserver and a response was received.
> * The Kubelet was informed of the new secure connection details.


> [!NOTE]
> From Master node

> Run 'kubectl get nodes' on the control-plane to see this node join the cluster.







# Testing deployment after the installation

```

kubectl create deployment nginx --image=nginx --port=80 --replicas=2

kubectl get all

kubectl expose deployment.apps/nginx --type=NodePort

kubectl get all

```




