# Vagrant with Docker provider

See: https://www.vagrantup.com/docs/docker/basics.html

Vagrant can run as an docker image

```ruby
Vagrant.configure("2") do |config|
  config.vm.provider "docker" do |d|
    d.image = "foo/bar"
  end
end
```

Vagrant can build and run a Dockerfile

```ruby
Vagrant.configure("2") do |config|
  config.vm.provider "docker" do |d|
    d.build_dir = "."
  end
end
```

run with

```
vagrant up --provider=docker
```
