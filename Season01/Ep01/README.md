# Ep01. Setup Kubernetes Cluster using kubeadm on CentOS 7
Follow this documentation to set up a Kubernetes cluster on __CentOS 7__ Virtual machines.

This documentation guides you in setting up a cluster with one master node and one worker node.

## Assumptions
|Role|FQDN|IP|OS|RAM|CPU|
|----|----|----|----|----|----|
|Master|kmaster.example.com|172.42.42.100|CentOS 7|2G|2|
|Worker1|kworker1.example.com|172.42.42.101|CentOS 7|1G|1|

## Get Git information

```
$ mkdir play && cd $_
$ git clone https://github.com/grtlinux/hello_kubernetes.git
$ cd hello_kubernetes/Season01/Ep01/run
```

## Make Vagrantfile and create machines

```
$ cat Vagrantfile
    # -*- mode: ruby -*-
    # vi: set ft=ruby :

    ENV['VAGRANT_NO_PARALLEL'] = 'yes'

    Vagrant.configure(2) do |config|

    # Kubernetes Master Server
    config.vm.define "kmaster" do |kmaster|
        kmaster.vm.box = "centos/7"
        kmaster.vm.hostname = "kmaster.example.com"
        kmaster.vm.network "private_network", ip: "172.42.42.100"
        kmaster.vm.provider "virtualbox" do |v|
        v.name = "kmaster"
        v.memory = 2048
        v.cpus = 2
        # Prevent VirtualBox from interfering with host audio stack
        v.customize ["modifyvm", :id, "--audio", "none"]
        end
    end

    NodeCount = 1
    # Kubernetes Worker Nodes
    (1..NodeCount).each do |i|
        config.vm.define "kworker#{i}" do |workernode|
        workernode.vm.box = "centos/7"
        workernode.vm.hostname = "kworker#{i}.example.com"
        workernode.vm.network "private_network", ip: "172.42.42.10#{i}"
        workernode.vm.provider "virtualbox" do |v|
            v.name = "kworker#{i}"
            v.memory = 1024
            v.cpus = 1
            # Prevent VirtualBox from interfering with host audio stack
            v.customize ["modifyvm", :id, "--audio", "none"]
        end
        end
    end

    end
$ vagrant up
    < wait 1 or 2 minutes >

```

## Confirm the virtual machines

```
$ vagrant status
$ vagrant box list
$ tree ~/.vagrant.d
$ vboxmanage list vms
$ vboxmanage list runningvms
```

## SSH connection

```
$ vagrant ssh kmaster
    [vagrant@kmaster ~]$ cat /etc/redhat-release
    [vagrant@kmaster ~]$ nproc
    [vagrant@kmaster ~]$ free -m
    [vagrant@kmaster ~]$ df -h
    [vagrant@kmaster ~]$ hostname -a
    [vagrant@kmaster ~]$ ip a
    [vagrant@kmaster ~]$ logout

$ vagrant ssh kworker1
    [vagrant@kworker1 ~]$ cat /etc/redhat-release
    [vagrant@kworker1 ~]$ nproc
    [vagrant@kworker1 ~]$ free -m
    [vagrant@kworker1 ~]$ df -h
    [vagrant@kworker1 ~]$ hostname -a
    [vagrant@kworker1 ~]$ ip a
    [vagrant@kworker1 ~]$ logout
$
```

## Poweroff and Delete the virtual machines

```
$ vboxmanage list vms
$ vboxmanage list runningvms
$ vagrant halt
$ vboxmanage list runningvms
$ vagrant destroy -f
$ vboxmanage list vms
$ vagrant status
```

## Remove vagrant box

```
$ vagrant box remove --all centos/7
$ vagrant box list
$ tree ~/.vagrant.d
```

---

Have Fun!!