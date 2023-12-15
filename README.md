# Damn Secure Web Application
Experimentation avec DVWA et NAXSI



## Installation
Sous machine virtuelle, installer [DVWA avec NGINX](DVWA.MD)

---

## Configuration de NAXSI avec DVWA sur Nginx
### Prérequis

- Serveur Nginx fonctionnel avec DVWA installé.
- Accès root ou privilèges sudo sur le serveur.

## Étapes d'installation et de configuration de NAXSI

### Étape 1 : Installation de NAXSI

#### Sur Ubuntu/Debian

```bash
sudo apt update
sudo apt install libnginx-mod-naxsi
```

### Étape 2 : Configuration de NAXSI pour Nginx

1. **Activez le module NAXSI pour Nginx** en ajoutant la ligne suivante dans le fichier de configuration de votre serveur (`/etc/nginx/nginx.conf`) dans la section `http` :

   ```nginx
   include /etc/nginx/naxsi_core.rules;
   ```

2. **Configurez la gestion des requêtes bloquées** en ajoutant une location spécifique dans votre configuration de serveur Nginx (habituellement située dans `/etc/nginx/sites-available/`) :

   ```nginx
   location /RequestDenied {
       return 403;
   }
   ```

3. **Activez NAXSI** pour DVWA en ajoutant ces lignes dans le bloc `server` ou `location` de votre configuration DVWA :

   ```nginx
   SecRulesEnabled;
   DeniedUrl "/RequestDenied";
   ```

   Pour un mode d'apprentissage (enregistre les violations mais ne bloque pas) :

   ```nginx
   LearningMode;
   ```

### Étape 3 : Redémarrage de Nginx

Après avoir modifié la configuration, redémarrez Nginx pour appliquer les changements :

```bash
sudo systemctl restart nginx
```

### Étape 4 : Test et Ajustement

- **Testez** votre configuration en essayant d'accéder à DVWA et en effectuant des activités normales. Vérifiez si des requêtes légitimes sont bloquées.

