# Vagrant basics

init Vagrant boilerplate file inside a directory:

```
vagrant init ubuntu/bionic64
```

find boxes at https://app.vagrantup.com/boxes/search

## run

inside directory with the Vagrantfile

```
vagrant up
vagrant ssh
vagrant halt
```

alternatives to `halt`: `suspend`, `destroy`

Default synced directory is the project directory (Vagrantfile root dir) under
`/vagrant`

## config

### sync folder

```
config.vm.synced_folder "../data", "/vagrant_data"
```

### provision

```ruby
config.vm.provision :shell, path: "bootstrap.sh"
config.vm.provision :shell, :inline => "cd /root/dokku && make dokku-installer"

config.vm.provision :shell, inline: <<-SHELL
	apt-get update
SHELL

config.vm.provision :shell, :privileged => false, :run => 'always', inline: <<-SHELL2
	java -jar app.jar
SHELL
```

reload provisioning

```
vagrant reload --provision
```

### config provider

```ruby
config.vm.provider "virtualbox" do |v|
	v.memory = 4096
	v.cpus = 2
	v.customize ["modifyvm", :id, "--natdnshostresolver1", "off"]
end
```

### ports and nets

```ruby
config.vm.network "forwarded_port", guest: 8529, host: 8529
config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"
```

Share running box with public internet:

```
vagrant share
```

### env vars

```ruby
BOX_NAME = ENV["BOX_NAME"] || "bento/ubuntu-14.04"

Vagrant.configure("2") do |config|
  config.vm.box = BOX_NAME
end
```
