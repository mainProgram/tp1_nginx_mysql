Vagrant.configure("2") do |config|
  # Configuration de la machine
  config.vm.box = "ubuntu/bionic64"
  config.vm.boot_timeout = 600
  config.vm.define "nginx-mysql-server" do |server|
    server.vm.network "private_network", ip: "192.168.56.11"
    server.vm.hostname = "nginx-mysql-node"
    server.vm.provider "virtualbox" do |vb|
      vb.name = "nginx-mysql-server"
      vb.memory = "1024"
      vb.cpus = 2
      vb.gui = false  # Ensure the GUI is disabled for headless operation
      vb.customize ["modifyvm", :id, "--graphicscontroller", "vmsvga"]
    end
    # Port forwarding pour MySQL
    server.vm.network "forwarded_port", guest: 3306, host: 3307
    # Provisioning : installation de Nginx et MySQL
    server.vm.provision "shell", inline: <<-SHELL
    # Mise à jour du système
      sudo apt update
      sudo apt upgrade -y

      # Installation de Nginx
      sudo apt install nginx -y

      # Installation de MySQL
      sudo apt install mysql-server -y
      sudo systemctl start mysql
      sudo systemctl enable mysql

      # Configuration de MySQL pour accepter les connexions externes
      sudo sed -i "s/bind-address.*/bind-address = 0.0.0.0/"/etc/mysql/mysql.conf.d/mysqld.cnf
      sudo systemctl restart mysql

      # Déploiement d'une application front simple dans Nginx
      echo '<!DOCTYPE html><html><head><title>Bienvenue</title></head><body>
      <h1>Bienvenue sur le serveur Nginx !</h1></body></html>' | sudo tee /var/www/html/index.html
      sudo systemctl restart nginx
    SHELL
 end
end
