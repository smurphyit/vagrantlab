# encoding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :

CHEF_SERVER_SCRIPT = <<EOF.freeze
apt-get update
apt-get -y install curl

# ensure the time is up to date -- 
echo "Setting timezone for NTP..."
sudo timedatectl set-timezone America/Halifax

## Determine if chef-server is already installed, if not download and install it.
dpkg --get-selections |grep chef-server-core
if [[ "$?" -ne "0" ]]; then
   echo "Downloading the Chef server package..."
     if [ ! -f /downloads/chef-server-core_12.17.5_amd64.deb ]; then
       wget -nv -P /downloads https://packages.chef.io/files/stable/chef-server/12.17.5/ubuntu/16.04/chef-server-core_12.17.5-1_amd64.deb
     fi
   echo "Installing Chef server..."
   sudo dpkg -i /downloads/chef-server-core_12.17.5-1_amd64.deb
fi

# reconfigure and restart services
echo "Reconfiguring Chef server..."
sudo chef-server-ctl reconfigure
echo "Restarting Chef server..."
sudo chef-server-ctl restart

# wait for services to be fully available
echo "Waiting for services..."
until (curl -D - http://localhost:8000/_status) | grep "200 OK"; do sleep 15s; done
while (curl http://localhost:8000/_status) | grep "fail"; do sleep 15s; done

# create admin user
echo "Creating a user and organization..."
sudo chef-server-ctl user-create admin Bob Admin admin@4thcoffee.com insecurepassword --filename admin.pem
sudo chef-server-ctl org-create 4thcoffee "Fourth Coffee, Inc." --association_user admin --filename 4thcoffee-validator.pem

# copy admin RSA private key to share
echo "Copying admin key to /vagrant/secrets..."
mkdir -p /vagrant/secrets
cp -f /home/vagrant/admin.pem /vagrant/secrets

<<<<<<< HEAD
# Now let's install chef-manage (so you can manage chef from the website interface)  IGNORE... Will require user-intervention
=======
# Now let's install chef-manage (so you can manage chef from the website interface)  OPTIONAL
>>>>>>> fac0cd13f0fcf1a0463747283ff78b872d22e38e
#sudo chef-server-ctl install chef-manage
#sudo chef-server-ctl reconfigure
#sudo chef-manage-ctl reconfigure

echo "Your Chef server is ready!"
EOF

NODE_SCRIPT = <<EOF.freeze
echo "Preparing node..."

## Ensure time is up to date
sudo timedatectl set-timezone America/Halifax

# Add chef server to local hosts file
echo "10.1.1.33 chef-server.test" | tee -a /etc/hosts

# Chef client is installed from the workstation, so no need to install it here
EOF

def set_hostname(server)
  server.vm.provision 'shell', inline: "hostname #{server.vm.hostname}"
end

Vagrant.configure(2) do |config|
  config.vm.define 'chef-server' do |cs|
    cs.vm.box = 'bento/ubuntu-16.04'
    cs.vm.hostname = 'chef-server.test'
    cs.vm.network 'private_network', ip: '10.1.1.33'
    cs.vm.provision 'shell', inline: CHEF_SERVER_SCRIPT.dup
    set_hostname(cs)

    cs.vm.provider 'virtualbox' do |v|
      v.memory = 4096
      v.cpus = 2
    end
  end

 config.vm.define 'node1-ubuntu' do |n|
    n.vm.box = 'bento/ubuntu-16.04'
    n.vm.hostname = 'node1-ubuntu'
    n.vm.network 'private_network', ip: '10.1.1.34'
    n.vm.provision :shell, inline: NODE_SCRIPT.dup
    set_hostname(n)
  end

 config.vm.define 'node2-ubuntu' do |n2|
    n2.vm.box = 'bento/ubuntu-16.04'
    n2.vm.hostname = 'node2-ubuntu'
    n2.vm.network 'private_network', ip: '10.1.1.35'
    n2.vm.provision :shell, inline: NODE_SCRIPT.dup
    set_hostname(n2)
 end
end

