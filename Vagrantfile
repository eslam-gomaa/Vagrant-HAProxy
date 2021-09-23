# HAproxy -- VBox
nginx_vms_number  = 2
vms_memory        = 2048
source_image      = "bento/ubuntu-18.04"

Vagrant.configure("2") do |config|
  config.ssh.username = "vagrant"
  config.ssh.password = "vagrant"

  config.vm.define "haproxy" do |haproxy|
    haproxy.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--memory", "#{vms_memory}"]
      v.customize ["modifyvm", :id, "--cpus", "2"]
      v.customize ["modifyvm", :id, "--name", "haproxy"]
    end
    haproxy.vm.box = "#{source_image}"
    haproxy.vm.hostname = "haproxy"
    haproxy.vm.network :public_network, bridge: "eth0" ,ip: "192.168.1.40"
    haproxy.vm.provision "shell", inline: $script_haproxy
  end

  (1..nginx_vms_number).each do |i|
    config.vm.define "nginx-test-#{i}" do |nginx|
      nginx.vm.provider :virtualbox do |v|
        v.customize ["modifyvm", :id, "--memory", "#{vms_memory}"]
        v.customize ["modifyvm", :id, "--cpus", "2"]
        v.customize ["modifyvm", :id, "--name", "nginx-test-#{i}"]
      end
      nginx.vm.box = "#{source_image}"
      nginx.vm.hostname = "nginx-#{i}"
      nginx.vm.network :public_network, bridge: "eth0", ip: "192.168.1.#{40 + i}"
      nginx.vm.provision "shell", inline: $script_nginx
    end
  end
end

# Scripts
$script_haproxy = <<-SCRIPT_HAPROXY
sudo add-apt-repository ppa:vbernat/haproxy-2.3
sudo apt-get update
sudo apt-get -y install haproxy

sudo systemctl start haproxy
sudo systemctl enable haproxy
sudo systemctl status haproxy
SCRIPT_HAPROXY


$script_nginx = <<-SCRIPT_NGINX
sudo su
sudo apt update
sudo apt install nginx -y
sudo echo "Hostname: $(hostname)" > /var/www/html/index.nginx-debian.html
sudo echo "IP: $(ip a s eth1 | egrep -o 'inet [0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' | cut -d' ' -f2)" >> /var/www/html/index.nginx-debian.html
sudo systemctl restart nginx
sudo systemctl enable nginx
SCRIPT_NGINX
