Vagrant.configure("2") do |config|
  # Configuración común para las tres máquinas
  config.vm.box = "ubuntu/jammy64" # Puedes usar otra versión de Ubuntu si lo prefieres
  config.vm.provider "virtualbox" do |vb|
    vb.cpus = 2
    vb.memory = 768
  end

  # Servidor Apache 1 (Web 1)
  config.vm.define "web1" do |web1|
    web1.vm.hostname = "web1"
    web1.vm.network "private_network", ip: "192.168.56.10"
    web1.vm.synced_folder "web1src", "/var/www/html" # Directorio compartido
    web1.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2
      systemctl enable apache2
      systemctl start apache2
      echo "Hola desde Web 1 soy Enmanuel 2022-1341" | sudo tee /var/www/html/index.html
    SHELL
  end

  # Servidor Apache 2 (Web 2)
  config.vm.define "web2" do |web2|
    web2.vm.hostname = "web2"
    web2.vm.network "private_network", ip: "192.168.56.11"
    web2.vm.synced_folder "web2src", "/var/www/html" # Directorio compartido
    web2.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2
      systemctl enable apache2
      systemctl start apache2
      echo "Hola desde Web 2 soy Enmanuel 2022-1341" | sudo tee /var/www/html/index.html
    SHELL
  end

  # Balanceador de carga con Nginx
  config.vm.define "loadbalancer" do |lb|
    lb.vm.hostname = "loadbalancer"
    lb.vm.network "private_network", ip: "192.168.56.20"
    lb.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y nginx
      # Configuración de Nginx como balanceador de carga
      cat <<EOL > /etc/nginx/sites-available/load_balancer
      upstream backend {
        server 192.168.56.10;
        server 192.168.56.11;
      }

      server {
        listen 80;

        location / {
          proxy_pass http://backend;
          proxy_set_header Host \$host;
          proxy_set_header X-Real-IP \$remote_addr;
          proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto \$scheme;
        }
      }
      EOL
      sudo ln -s /etc/nginx/sites-available/load_balancer /etc/nginx/sites-enabled/
      sudo rm /etc/nginx/sites-enabled/default
      systemctl enable nginx
      systemctl restart nginx
    SHELL
  end
end
