# How to setup Nodes in k8s using Virtual Box in Redhat 8 (CLI)


Create Folder
```
mkdir dvd
```

Mount dvd folder for package and software

```
mount /dev/cdrom /dvd
```
Create Repo in yum
```
vi /etc/yum.repos.d/dvd.repo
```

Inside dvd.repo like this
```
[dvd]
baseurl=file:///dvd/AppStream
gpgcheck=0

[dvd2]
baseurl=file:///dvd/BaseOS
gpgcheck=0
```


Install net-tools for ip address and vim for editing
```
yum install net-tools vim -y
```
Kubeclt require in docker package
so create docker.repo
```
vim /etc/yum.repos.d/docker.repo
```

Inside docker.repo like this
```
[docker]
baseurl=https://download.docker.com/linux/centos/7/x86_64/stable/
gpgcheck=0
```
Install docker
```
yum install docker-ce -y --nobest
```
Start always running in docker
```
sytemctl start docker
sytemctl enable docker
```

lets start install k8s

```
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
EOF

# Set SELinux in permissive mode (effectively disabling it)
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
```

in shutdown then it will automatic unmount dvd 
so we need automatic mount dvd
```
vim /etc/rc.d/rc.local
```
add mount 
```
mount /dev/cdrom /dvd
```
needed execute in rc.local
```
chmod +x /etc/rc.d/rc.local
```
need remove swap in fstab
```
vim /etc/fstab/
```
add comment like this
```
#/dev/mapper/rhel-swap   swap      swap    defaults    0 0
```

install iproute 
```
yum install iproute-tc
```

now restart

make a 2 clone like slave1 and slave2 and regenerate for all networks

make host name for 3 VM's

master in vm
```
hostnamectl set-hostname master
exec bash
```
slave1 inn vm
```
hostnamectl set-hostname slave1
exec bash
```
slave2 in vm
```
hostnamectl set-hostname slave2
exec bash
```

set up Local DNS Name server
```
vim /etc/hosts
```
edit in hosts like this
``` 
192.168.43.35  master
192.168.43.151 slave1
192.168.43.156 slave2
```

same thing in slave1 and slave2 VM's
it is easier to transfer to another machine
slave1 in VM's
```
scp /etc/hosts 192.168.43.151:/etc/hosts
```
slave2 in VM's 
```
scp /etc/hosts 192.168.43.156:/etc/hosts
```

Now,
Create kubeadm ( master )
```
kubeadm init --pod-networks=10.10.1.0/16
```

add config
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

now join nodes like slave1 and slave2
```
kubeadm join 192.168.43.35:6443 --token 1z41ua.pd2l2pl5df286hcb     --discovery-token-ca-cert-hash sha256:4547a1aacde3bd0b1ff37fe27619aa858081634717b212e609dfeba96e0402be
```

## This is done 😀😀






