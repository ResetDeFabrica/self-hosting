# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "debian/bookworm64"

  config.vm.define "mrm" do |mrm|
    mrm.vm.network "private_network", ip: "192.168.57.10"

    mrm.vm.provision "shell", inline: <<-SHELL
    sudo apt update
    sudo apt install -y git nginx openssl ufw
  
    sudo mkdir -p /var/www/mrm/html
    git clone https://github.com/cloudacademy/static-website-example /var/www/mrm/html
    
    sudo chown -R www-data:www-data /var/www/mrm/html
    sudo chmod -R 755 /var/www/mrm
  
    sudo cp /vagrant/files/mrm /etc/nginx/sites-available/mrm
    
sudo openssl req -x509 -nodes -days 365 \
  -newkey rsa:2048 -keyout /etc/ssl/private/mrm.key \
  -out /etc/ssl/certs/mrm.crt \
 -subj "/C=ES/ST=Andalucía/L=Granada/O=IZV/OU=WEB/CN=mrm/emailAddress=webmaster@mrm.com"
  
    sudo ln -sf /etc/nginx/sites-available/mrm /etc/nginx/sites-enabled/mrm
    sudo nginx -t
    sudo systemctl restart nginx
  
    sudo ufw allow ssh
    sudo ufw allow 'Nginx Full'
    sudo ufw delete allow 'Nginx HTTP'
    sudo ufw --force enable
  SHELL
  
  end 

end