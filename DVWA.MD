# Installation de DVWA avec Nginx

Ce guide vous montre comment installer et configurer DVWA (Damn Vulnerable Web Application) en utilisant le serveur web Nginx sur un système Linux.

## Prérequis

- Serveur Linux (Debian/Ubuntu ou dérivé recommandé)
- Accès root ou privilèges sudo

## Étapes d'installation

### Étape 1 : Installation des Dépendances

#### Nginx

```bash
sudo apt update
sudo apt install nginx
```

#### PHP et Extensions PHP

```bash
sudo apt install php-fpm php-mysql
```

#### MySQL

```bash
sudo apt install mysql-server
sudo mysql_secure_installation
```

### Étape 2 : Configuration de MySQL

1. **Connexion à MySQL** :

    ```bash
    sudo mysql -u root -p
    ```

2. **Création de la Base de Données et de l'Utilisateur DVWA** :

    ```sql
    CREATE DATABASE dvwa;
    CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'J3r3my!!2023';
    GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwa'@'localhost';
    FLUSH PRIVILEGES;
    EXIT;
    ```

### Étape 3 : Installation de DVWA

1. **Téléchargement et Installation** :

    ```bash
    cd /var/www/html
    sudo git clone https://github.com/ethicalhack3r/DVWA.git
    sudo chown -R www-data:www-data /var/www/html/DVWA
    ```

2. **Configuration** :

    ```bash
    sudo cp /var/www/html/DVWA/config/config.inc.php.dist /var/www/html/DVWA/config/config.inc.php
    sudo nano /var/www/html/DVWA/config/config.inc.php
    ```

    Mettre à jour les informations de connexion à la base de données dans `config.inc.php`.

### Étape 4 : Configuration de Nginx pour DVWA

1. **Création du Fichier de Configuration Nginx pour DVWA** :

    ```bash
    sudo nano /etc/nginx/sites-available/dvwa
    ```

    Ajouter la configuration suivante :

    ```nginx
    server {
        listen 80;
        server_name dvwa.local;
        root /var/www/html/DVWA;
        index index.php index.html index.htm;

        location / {
            try_files $uri $uri/ =404;
        }

        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/run/php/php7.x-fpm.sock; # Remplacer 7.x par votre version PHP
        }
    }
    ```

2. **Activation du Site et Redémarrage de Nginx** :

    ```bash
    sudo ln -s /etc/nginx/sites-available/dvwa /etc/nginx/sites-enabled/
    sudo nginx -t
    sudo systemctl restart nginx
    ```

### Étape 5 : Finalisation

- Ouvrez votre navigateur et accédez à `http://dvwa.local`.
- Suivez les instructions sur la page pour terminer la configuration de DVWA.

## Remarques Importantes

- Assurez-vous que la version de PHP dans la configuration Nginx correspond à celle installée sur votre serveur.
