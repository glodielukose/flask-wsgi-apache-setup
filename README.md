# **Déployer une Application Flask sur Apache avec WSGI**

### 1. **Table des Matières**
   - [Description](#2-description)
   - [Prérequis](#3-prérequis)
   - [Installation](#4-installation)
   - [Configuration de l'application Flask](#5-configuration-de-lenvironnement-python-et-de-lapplication-flask)
   - [Configuration d'Apache](#2-configuration-dapache)
   - [Tester l'Application](#7-tester-lapplication)
   - [Ressources Utiles](#8-ressources-utiles)
   - [Licence](#9-licence)

### 2. **Description**
  Ce guide vous montre comment déployer une application Flask sur un serveur Apache grâce à WSGI.

  * **Flask** : un micro-framework Python facilitant la création d'applications web.
  * **Apache** : un serveur web qui gère les différentes requêtes en production et les redirige vers les applications appropriées. Cependant, Apache ne peut pas traiter les requêtes Python seul ; une passerelle est nécessaire.
  * **WSGI** : une norme qui permet de connecter des applications web développées avec des frameworks comme Flask ou Django à des serveurs web tels qu'Apache ou Nginx.

  Nous allons construire pas à pas une application Flask, puis la déployer sur un serveur Apache en utilisant une passerelle WSGI.

### 3. **Prérequis**
   - Python, Flask
   - Ubuntu 22.04

### 4. **Installation**
   Commençons par installer Python, Apache et la passerelle WSGI sur notre machine via le terminal :

   - Mettez à jour les paquets :
     ```bash
     sudo apt update -y
     ```

   #### 1. Installation de Python
   Installez Python avec les commandes suivantes :
   ```bash
   sudo apt install python3 python3-pip python3-venv
   ```

   #### 2. Installation d'Apache
   - Installez Apache :
     ```bash
     sudo apt install apache2
     ```
   - Lancez Apache :
     ```bash
     sudo systemctl enable apache2 && sudo systemctl start apache2
     ```
   - Vérifiez le statut d'Apache :
     ```bash
     sudo systemctl status apache2
     ```

   #### 3. Installation de WSGI
   Installez le module WSGI pour Apache :
   ```bash
   sudo apt install libapache2-mod-wsgi-py3
   ```

### 5. **Configuration de l'Environnement Python et de l’Application Flask**
   À cette étape, nous allons créer un environnement Python et cloner le projet sur notre machine.

   #### 1. Clonage du projet
   - Accédez au dossier www :
     ```bash
     cd /var/www
     ```
   - Clonez le projet :
     ```bash
     git clone <url project>
     ```

   #### 2. Création de l'Environnement Virtuel Python
   - Accédez au répertoire du projet :
     ```bash
     cd /var/www/flask-wsgi-apache-setup
     ```
   - Créez l'environnement virtuel dans le projet :
     ```bash
     python3 -m venv flask-venv
     ```
   - Activez l'environnement :
     ```bash
     source flask-venv/bin/activate
     ```

   #### 3. Installation des dépendances
   Installez Flask :
   ```bash
   pip3 install flask
   ```

   Définissez la variable d'environnement `FLASK_APP` :
   ```bash
   export FLASK_APP=app.py
   ```

   Testez si tout fonctionne correctement :
   ```bash
   flask run --host=0.0.0.0
   ```

   Une fois le test terminé, désactivez l'environnement :
   ```bash
   deactivate
   ```

### 6. **Configuration d'Apache**
   Maintenant que notre application Flask est prête, nous devons la déployer sur un serveur Apache. Apache ne comprend pas directement le code Python, nous utiliserons donc WSGI pour servir notre application.

   #### 1. **Configuration de WSGI**
   Créez un fichier `.wsgi` dans le répertoire de l’application :
   ```bash
   nano flask-app.wsgi
   ```
   Ajoutez le contenu suivant :
   ```python
   import sys
   sys.path.insert(0, "/var/www/flask-wsgi-apache-setup")
   from app import app as application
   ```

   #### 2. **Configuration d'Apache**
   Accédez au dossier de configuration d'Apache :
   ```bash
   cd /etc/apache2/sites-available
   ```
   Créez un fichier de configuration :
   ```bash
   nano flask-wsgi-apache-setup.conf
   ```
   Ajoutez-y le contenu suivant, en remplaçant `yourdomain.com` par votre adresse IP :
   ```apache
   <VirtualHost *:80>
     ServerName yourdomain.com
     DocumentRoot /var/www/flask-wsgi-apache-setup

     WSGIDaemonProcess app user=www-data group=www-data threads=5 python-home=/var/www/flask-wsgi-apache-setup/flask-venv
     WSGIScriptAlias / /var/www/flask-wsgi-apache-setup/flask-app.wsgi

     ErrorLog ${APACHE_LOG_DIR}/flask-error.log
     CustomLog ${APACHE_LOG_DIR}/flask-access.log combined

     <Directory /var/www/flask-wsgi-apache-setup>
       WSGIProcessGroup app
       WSGIApplicationGroup %{GLOBAL}
       Require all granted
     </Directory>
   </VirtualHost>
   ```

   Redémarrez Apache pour appliquer les modifications :
   ```bash
   sudo a2dissite 000-default.conf
   sudo a2ensite flask-wsgi-apache-setup.conf
   sudo systemctl restart apache2
   ```

### 7. **Tester l'Application**
   Pour vérifier que tout fonctionne, entrez l'adresse IP dans votre navigateur.

### 8. **Ressources Utiles**
   - [Documentation Flask](https://flask.palletsprojects.com/)
   - [Documentation Apache](https://httpd.apache.org/docs/)
   - [Documentation WSGI](https://wsgi.readthedocs.io/)

### 9. **Licence**
   MIT License