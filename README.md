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







