<<<<<<< HEAD

# Change VM Provider:

https://www.vagrantup.com/docs/providers/basic_usage

# puppet-vagrant-lab
=======
# Install and Configure Puppet Server and Client nodes for Training

## Vagrant and Virtualbox/KVM for quick deployment

### Basic
Vagrant.configure("2") do |config|
  config.vm.box = "roboxes/centos8s"
end

### Custom Vagrantfile

#### puppetserver.local

```
CPUS="2"
MEMORY="4024"

Vagrant.configure("2") do |config|
  config.vm.define :puppetserver do |puppetserver|
    puppetserver.vm.box = "roboxes/centos8s"
    puppetserver.vm.hostname = "puppetserver"
    puppetserver.vm.provider :libvirt do |domain|
          domain.memory = MEMORY
          domain.cpus = CPUS
    end
  end

end
```



### DNS - FQDN
- Set /etc/hostname on Server and all Nodes
- Add Server and all nodes to all /etc/hosts

```
ex:
192.168.121.32   puppet puppet-server puppet-server.local
192.168.121.109  puppet-client1 puppet-client1.local
192.168.121.118  deployserver deployserver.local
```
oldline=$(grep puppet-master /shared/hosts)
newline="$(hostname -I)  $(hostname) $(hostname).local"
sed -i -e "s/${oldline}/${HostsLine}/g" /shared/hosts


```

### Configure firewall on puppet-server

```
firewall-cmd --permanent --new-service=puppet
firewall-cmd --permanent --service=puppet --add-port=8140/tcp
firewall-cmd --permanent --service=puppet --add-port=8140/udp
firewall-cmd --permanent --add-service puppet
firewall-cmd --reload
firewall-cmd --list-services
```

### Locations of Puppet Packages
- yum: https://yum.puppetlabs.com/
- apt: https://apt.puppetlabs.com/

### Install Puppet Repos on Server and Nodes

- yum -y localinstall https://yum.puppetlabs.com/puppet-release-el-8.noarch.rpm
- yum -y localinstall https://yum.puppetlabs.com/puppet-tools-release-el-8.noarch.rpm

### Install puppetserver
- yum install puppetserver

### Set Memory on puppetserver (ex: 3G - may not run if too low)
- vim /etc/sysconfig/puppetserver

### Install puppet clients
- yum install puppet

### Start and enable services

```
#puppetserver
systemctl enable puppetserver
systemctl start puppetserver

#server and clients
systemctl enable puppet
systemctl start puppet
```

### Run puppet agent on clients to generate certs
- sudo puppet agent -tv --server=puppet-master.local
### Will probably need to run. Won't run with sudo?:
- puppet agent -tv


### Sign client certs on puppetserver
```
puppetserver ca list --all
puppetserver ca sign --all

# or sign individually
puppetserver ca sign deployserver.local
puppetserver ca sign puppet-client1.local


# clear existing certs
puppetserver ca clean --certname deployserver.local
puppetserver ca clean --certname puppet-client1.local


```


>>>>>>> abe60ea (Added alma-linux client)
