# Kubernetes Linux Distribution Packages Builder

### Kubernetes is in pre-production beta!
While the concepts and architecture in Kubernetes represent years of experience designing and building large scale cluster manager at Google, the Kubernetes project is still under heavy development.  Expect bugs, design and API changes as we bring it to a stable, production product this year.


## Quick Install (RedHat 7, CentOS 7, Debian 7, Ubuntu 14.x, 15.x)

* Kubernetes master:
```
curl -sSL https://get.kismatic.com/kubernetes/master.sh | sudo sh
`
* Kubernetes nodes:
```
curl -sSL https://get.kismatic.com/kubernetes/node.sh | sudo sh
```

* Requirements:
  * Install [etcd](https://github.com/coreos/etcd) (on master or a separate etcd cluster)
  * Install [docker](https://docs.docker.com/installation) (ubuntu/debian only)


##Configuration and Manual Setup
### System.d and RedHat 7 / CentOS 7 Configuration
`rpm -Uvh https://repos.kismatic.com/el/7/x86_64/kismatic-repo-el-7-1.x86_64.rpm`
Master: `sudo yum install kubernetes-master`
Nodes: `sudo yum kubernetes-node`

#### Configure Kubernetes Master

*Configure Kubernetes Master*
To configure services, edit configuration files in `/etc/kubernetes/master` or `/etc/kubernetes/node`
To override services, copy `/lib/systemd/system` to `/etc/systemd/system` to override. This allows you to customize local services without worrying about losing changes as the result of a package upgrade.


*Start services*
sudo systemctl start kube-apiserver
sudo systemctl start kube-scheduler
sudo systemctl start kube-controller-manager
sudo systemctl start kubelet

test with
`curl -L http://<master ip>:8080/version`

###### Configure Kubernetes Node

Set `--master=<master ip>` for a node in `/etc/kubernetes/node/kube-proxy.conf` and `--master=<master ip>` in `/etc/kubernetes/node/kubelet.conf`


sudo systemctl start kube-proxy
sudo systemctl start kubelet

Check services are running
`systemctl list-units -t service --all`




### Init.d and Debian 7 (wheezy)/ Ubuntu 14.x (trusty) Configuration

Install docker on [Ubuntu](https://docs.docker.com/installation/ubuntulinux/) or [Debian](https://docs.docker.com/installation/debian/) and [etcd](https://github.com/coreos/etcd)


#### Configure Kubernetes
To configure services, edit the file with the same name in `/etc/default`

Master

```bash
sudo service kube-apiserver start
sudo service kube-scheduler start
sudo service kube-controller-manager start
sudo service kubelet start
```

Configure Node
Set the `--master` for a node in `/etc/default/kube-proxy` and `--api-servers` in `/etc/default/kubelet`

```bash
sudo service kube-proxy start
sudo service kubelet start
```

## Development Build Notes
* Make sure to have fpm installed along with rpmbuild
* run `K8S_VERSION=0.20.2 ./build_kubernetes.sh`

### Vagrant Smoke tests
- CentOS
```
$ cd vagrant/centos; vagrant destroy && vagrant up
$ vagrant ssh master
$ sudo K8S_VERSION=0.20.2 /vagrant/test/master.sh
$ vagrant ssh node
$ sudo K8S_VERSION=0.20.2 /vagrant/test/node.sh
```

-Ubuntu
```
# Trusty
$ cd vagrant/ubuntu/trusty; vagrant destroy && vagrant up
$ vagrant ssh master
$ sudo K8S_VERSION=0.20.2 /vagrant/test/master.sh
$ vagrant ssh node
$ sudo K8S_VERSION=0.20.2 /vagrant/test/node.sh

#Vivid
$ cd vagrant/ubuntu/vivid; vagrant destroy && vagrant up
$ vagrant ssh master
$ sudo K8S_VERSION=0.20.2 /vagrant/test/master.sh
$ vagrant ssh node
$ sudo K8S_VERSION=0.20.2 /vagrant/test/node.sh
```

## Requirements

* ruby
* prerequisites:

     `gem install fpm`

## Authors

   * BC Broussard
   * Patrick Reilly
