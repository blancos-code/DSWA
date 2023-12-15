# Damn Secure Web Application
Experimentation avec DVWA et NAXSI



## Installation
Sous machine virtuelle, installer [DVWA avec NGINX](DVWA.MD)

Voici un exemple de README pour configurer et utiliser NAXSI, un Web Application Firewall (WAF), avec DVWA (Damn Vulnerable Web Application) sur un serveur Nginx. Ce guide suppose que vous avez déjà installé DVWA sur un serveur Nginx et que vous souhaitez ajouter une couche de sécurité supplémentaire avec NAXSI.

---

## Configuration de NAXSI avec DVWA sur Nginx

Ce README vous guidera à travers les étapes de configuration de NAXSI, un WAF open-source, pour sécuriser DVWA (Damn Vulnerable Web Application) hébergé sur un serveur Nginx.

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

- **Ajustez les règles** de NAXSI en fonction des logs si nécessaire pour minimiser les faux positifs.

## Surveillance et Maintenance

- **Surveillez les logs** de Nginx pour les requêtes bloquées et ajustez les règles de NAXSI en conséquence.
- **Mettez à jour régulièrement** NAXSI et Nginx pour bénéficier des dernières améliorations de sécurité.

## Remarques Importantes

- NAXSI peut générer des faux positifs, surtout au début. Il est crucial d'ajuster les règles pour votre environnement spécifique.
- DVWA est conçu pour être vulnérable. L'utilisation de NAXSI avec DVWA peut bloquer certaines de ses fonctionnalités vulnérables, ce qui est utile pour tester la capacité de NAXSI à détecter et bloquer les attaques.

---

Ce README offre un guide de base pour la configuration de NAXSI avec DVWA sur Nginx. Il peut nécessiter des ajustements en fonction de votre configuration spécifique et des exigences de votre environnement.
