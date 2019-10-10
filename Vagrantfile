# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-16.04"
  config.vm.hostname = "hc-vision-elasticstack"
  config.ssh.shell = "bash"

  config.vm.network :forwarded_port, guest: 80, host: 80  # nginx --> kibana
  config.vm.network :forwarded_port, guest: 9000, host: 9000  # portainer
  config.vm.network :forwarded_port, guest: 9200, host: 9200  # elasticsearch

  config.vm.provision :shell, inline: $provisioningScript
  config.vm.provision :shell, inline: $startupScript, run: 'always'

  config.vm.provider "hyperv" do |hv|
    hv.cpus = "2"
    hv.memory = "2048"
    hv.maxmemory = "2048"
  end
end

$provisioningScript = <<SCRIPT
  #!/usr/bin/env bash

  apt-get update
  apt-get upgrade -y
  apt-get -y install \
      make \
      docker.io \
      docker-compose
      
  cd /vagrant
	docker volume create portainer_data
	docker run -d -p 9000:9000 --restart always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
  docker-compose up -d
SCRIPT

$startupScript = <<SCRIPT
  #!/usr/bin/env bash
  sudo sysctl -w vm.max_map_count=262144
  sudo docker restart $(docker ps -q)
  alias ls="ls -l"
SCRIPT
