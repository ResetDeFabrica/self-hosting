# -*- mode: ruby -*-  
# vi: set ft=ruby :

# Configuración principal de Vagrant
Vagrant.configure("2") do |config|

  config.vm.box = "debian/bookworm64"

  # Se define la configuración para la máquina virtual "resetdefabrica"
  config.vm.define "resetdefabrica" do |resetdefabrica|

    # Configuración de red para la máquina virtual con una IP estática en la red privada
    resetdefabrica.vm.network "private_network", ip: "192.168.57.10"

    # Provisionamiento de la VM con un script shell
    resetdefabrica.vm.provision "shell", inline: <<-SHELL

    # Actualización de los repositorios y la instalación de paquetes necesarios
    apt update
    apt install -y git nginx openssl ufw docker.io curl apache2-utils

    # Configuración de reglas del firewall utilizando UFW (Uncomplicated Firewall)
    ufw allow ssh
    ufw allow 'Nginx Full'  # Permite el tráfico HTTP y HTTPS para nginx
    ufw allow 19999         # Permite el puerto 19999, utilizado para monitoreo (por ejemplo, Netdata)
    ufw delete allow 'Nginx HTTP'  # Elimina la regla 'Nginx HTTP' para evitar duplicados
    ufw --force enable       # Habilita el firewall con las reglas configuradas

    # Crea un directorio para alojar los archivos web
    mkdir -p /var/www/resetdefabrica/html

    # Establece permisos apropiados para los archivos del directorio web
    chown -R www-data:www-data /var/www/resetdefabrica/html
    chmod -R 755 /var/www/resetdefabrica

    # Copia la configuración de Nginx desde el directorio /vagrant al sistema
    cp /vagrant/resetdefabrica /etc/nginx/sites-available/resetdefabrica

    # Copia los archivos estáticos desde el directorio /vagrant/files al servidor web
    cp -r /vagrant/files/* /var/www/resetdefabrica/html

    # Generación de un certificado SSL autofirmado para Nginx
    openssl req -x509 -nodes -days 365 \
    -newkey rsa:2048 -keyout /etc/ssl/private/resetdefabrica.key \
    -out /etc/ssl/certs/resetdefabrica.crt \
    -subj "/C=ES/ST=Andalucía/L=Granada/O=IZV/OU=WEB/CN=resetdefabrica/emailAddress=webmaster@resetdefabrica.com"

    # Activa la configuración del sitio en Nginx creando un enlace simbólico
    ln -sf /etc/nginx/sites-available/resetdefabrica /etc/nginx/sites-enabled/resetdefabrica

    # Verificación de la configuración de Nginx
    nginx -t

    # Reinicia el servicio de Nginx para aplicar los cambios
    systemctl restart nginx

    # Instalación de ngrok para exponer servicios locales a Internet
    curl -sSL https://ngrok-agent.s3.amazonaws.com/ngrok.asc \
    | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null \
    && echo "deb https://ngrok-agent.s3.amazonaws.com buster main" \
    | sudo tee /etc/apt/sources.list.d/ngrok.list \
    && sudo apt update \
    && sudo apt install ngrok

    # Configura ngrok con el token de autenticación
    ngrok config add-authtoken 2poHYU57PsYeftaJPTjBBKJNk8i_3E3drNDvHeVrgqJbsCTSA

    # Inicia ngrok en segundo plano para exponer el servicio HTTPS
    nohup ngrok http --url=oriented-supposedly-bunny.ngrok-free.app 443 &

    # Configura y ejecuta un contenedor Docker con Uptime Kuma para monitoreo
    docker run -d --restart=always -p 3001:3001 -v uptime-kuma:/app/data --name uptime-kuma louislam/uptime-kuma:1

    # Crear usuarios y contraseñas para nginx en admin
    sh -c "echo -n 'admin:' >> /etc/nginx/.htpasswd_admin"
    sh -c "openssl passwd -apr1 'asir' >> /etc/nginx/.htpasswd_admin"

    cat /etc/nginx/.htpasswd_admin
    # Crear usuarios y contraseñas para nginx en status
    sh -c "echo -n 'sysadmin:' >> /etc/nginx/.htpasswd_status"
    sh -c "openssl passwd -apr1 'risa' >> /etc/nginx/.htpasswd_status"

    cat /etc/nginx/.htpasswd_status

  SHELL
  end 
end