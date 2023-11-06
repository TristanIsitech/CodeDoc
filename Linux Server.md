# Connect and install to server

```BASH
#Connect to server with IP_SERVER
ssh-keygen -R IP_SERVER
ssh root@IP_SERVER

# APT
apt update
apt upgrade

# MySQL
apt install mysql-server
systemctl start mysql
systemctl enable mysql

# NGINX
apt install nginx
systemctl status nginx
systemctl enable nginx

# GIT
apt install git
git --version

# DOTNET
wget https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh
chmod +x ./dotnet-install.sh
./dotnet-install.sh --version latest
./dotnet-install.sh --version latest --runtime aspnetcore
./dotnet-install.sh --channel 7.0
export PATH=$PATH:$DOTNET_ROOT:$DOTNET_ROOT/tools
dotnet --version

# DOTNET-EF
dotnet tool install --global dotnet-ef
dotnet-ef

# NODE.JS
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
source ~/.bashrc
nvm --version
nvm ls
nvm ls-remote
nvm install [version.number]
```

# Config MySql

```BASH
# Connect to SQL
mysql -u root -p
# Quit SQL
EXIT
```

```SQL
-- ADD BDD --
CREATE DATABASE nom_de_la_base_de_donnees;
SHOW DATABASES;

-- USE DATABASE --
USE DATABASE;
SHOW TABLES;

-- ADD USER DATABASE --
CREATE USER 'nom_utilisateur'@'localhost' IDENTIFIED BY 'mot_de_passe';
GRANT ALL PRIVILEGES ON nom_base_de_donnees.* TO 'nom_utilisateur'@'localhost';
FLUSH PRIVILEGES;
```

# Config Dotnet
```BASH

```

# Config Node.js
```Bash
# Install Dépendence
npm i


```