BOX_IMAGE = "ubuntu/bionic64"
BALANCER_IP_ADDRESS = "192.168.100.10"
BALANCER_PORT = 8081
WORKER_COUNT = 2

Vagrant.configure("2") do |config|
 config.vm.box = BOX_IMAGE

  config.vm.define "balancer" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "balancer"
    subconfig.vm.network "forwarded_port", guest: 80, host: BALANCER_PORT
    subconfig.vm.network :private_network, ip: BALANCER_IP_ADDRESS
    subconfig.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y nginx
      echo "#{subconfig.vm.hostname}" > /var/www/html/index.nginx-debian.html
      service nginx start
    SHELL
  end

(1..WORKER_COUNT).each do |worker_count|
    config.vm.define "worker#{worker_count}" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.hostname = "worker#{worker_count}"
      subconfig.vm.network "forwarded_port", guest: 80, host: (BALANCER_PORT + worker_count)
      subconfig.vm.network :private_network, ip: "192.168.100.#{10 + worker_count}"
      subconfig.vm.provision "shell", inline: <<-SHELL
        apt-get update
        apt-get install -y nginx
        echo "#{subconfig.vm.hostname}" > /var/www/html/index.nginx-debian.html
        service nginx start
      SHELL
    end
  end

  config.vm.provider "virtualbox" do |vb|
    # Customize the amount of memory on the VM:
    vb.memory = "512"
  end
end