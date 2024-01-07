# Damn Secure Web Application
Experimentation avec DVWA et NAXSI

## Recompilation de Nginx avec Naxsi
1. Installez les dépendances :
   ```bash
   apt install libpcre3-dev libssl-dev libxml2-dev libxslt-dev libgd-dev libgeoip-dev
   ```

2. Accédez au répertoire /opt :
   ```bash
   cd /opt
   ```

3. Téléchargez Nginx et Naxsi :
   ```bash
   wget http://nginx.org/download/nginx-1.22.1.tar.gz
   git clone --recurse-submodules https://github.com/wargio/naxsi.git
   ```

4. Extrayez l'archive Nginx :
   ```bash
   tar -xvzf nginx-1.22.1.tar.gz
   ```

5. Accédez au répertoire Nginx extrait et trouvez les arguments de configuration d'origine :
   ```bash
   cd nginx-1.22.1
   nginx -V
   ```

6. Ajustez les arguments de configuration en ajoutant des options spécifiques à Naxsi :
   ```bash
   ./configure --with-cc-opt='...' --with-ld-opt='...' --prefix=/usr/share/nginx --sbin-path=/usr/sbin/nginx --conf-path=/etc/nginx/nginx.conf --add-module=/opt/naxsi/naxsi_src/ ...
   ```

7. Arrêtez Nginx et recompilez-le :
   ```bash
   service nginx stop
   make
   make install
   ```

8. Démarrez Nginx et verrouillez-le pour éviter les écrasements par le gestionnaire de packages :
   ```bash
   service nginx start
   nginx -V
   apt-mark hold nginx
   ```

## Configuration de Naxsi WAF
1. Copiez le jeu de règles de base de Naxsi dans le répertoire de configuration de Nginx :
   ```bash
   cp /opt/naxsi/naxsi_rules/naxsi_core.rules /etc/nginx
   ```

2. Ouvrez le fichier de configuration principal de Nginx :
   ```bash
   nano /etc/nginx/nginx.conf
   ```

3. Ajoutez la ligne suivante dans le bloc http {} pour activer Naxsi :
   ```nginx
   include /etc/nginx/naxsi_core.rules;
   ```

4. Créez un fichier de règles pour votre domaine (par exemple, example.com) :
   ```bash
   nano /etc/nginx/example.com.rules
   ```

5. Collez les options Naxsi dans le fichier. Ajustez selon vos besoins et enregistrez :
   ```nginx
   # Fichier de règles d'exemple pour le vhost.
   LearningMode;
   SecRulesEnabled;
   DeniedUrl "/RequestDenied";

   ## Vérification des règles
   CheckRule "$SQL >= 8" BLOCK;
   CheckRule "$RFI >= 8" BLOCK;
   CheckRule "$TRAVERSAL >= 4" BLOCK;
   CheckRule "$EVADE >= 4" BLOCK;
   CheckRule "$XSS >= 8" BLOCK;
   ```

6. Ouvrez le fichier de configuration Nginx du domaine et incluez le fichier de règles :
   ```bash
   nano /etc/nginx/sites-available/example.com.conf
   ```

7. Ajoutez la ligne suivante dans le bloc de localisation principal :
   ```nginx
   include /etc/nginx/example.com.rules;
   ```

8. Enregistrez le fichier et redémarrez Nginx :
   ```bash
   service nginx restart
   ```
