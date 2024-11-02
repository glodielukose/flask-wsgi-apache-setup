
# **Déployer une Application Flask sur Apache avec WSGI**

### 1. **Table des Matières**
   - [Description](#3-description)
   - [Prérequis](#4-prérequis)
   - [Installation](#5-installation)
   - [Configuration de l'application Flask]()
   - [Configuration d'Apache](#7-configuration-dapache)

### 2. **Description**
  Le but de ce README est de vous montrer comment on peut deployer une application flask sur apache grace à wsgi : 

  * **Flask** : c'est un mini-framework Python qui nous simplifie la création des applications web
  * **Apache** : Apache est un serveur web qui permet gère les differentes requetes en production et les redistribuent aux applications, mais apache à lui seul ne pas gerer des requete python, on aura donc besoin d'une pacerelle
  * **WSGI** : Apache est une norme qui va nous permettre de relier des applications web fait avec des framework comme (Flask, Django) avec des serveurs web comme (Apache, NGinx)


  On va construire pas à pas une application flask et ensuite la deployer sur un serveur Apache en utilisant une pacerrelle commen WSGI

### 3. **Prérequis**
   - Python, Flask
   - Ubuntu 22.04

### 4. **Installation**
   - Pour commencer on va devoir installer Python, apache et une pacerelle wsgi sur notre machine à partir du terminal :
      * Premièrement on met à jour les paquets
         ```bash
         sudo apt update -y
         ```
      #### 1. Installation et mise en place Python
      Pour installer Python il faut taper les commandes suivantes:
      ```bash
      sudo apt install python3 python3-pip python3-venv
      ```

      #### 2. Installation de Apache
      * On installe apache avec la commande suivant :
         ```bash
         sudo apt install apache2
         ```
      * Une fois que apache est installer on va le lancer :
         ```bash
         sudo systemctl enable apache2 && sudo systemctl start apache2
         ```
      * On vérifie si apache fonctionne correctement :
         ```bash
            sudo systemctl status apache2
         ``` 
         on aura le resultat suivant :
         ```bash
         root@host:~$ sudo systemctl status apache2
         ● apache2.service - The Apache HTTP Server
            Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor prese>
            Active: active (running) since Fri 2024-11-01 22:13:30 CAT; 14h ago
               Docs: https://httpd.apache.org/docs/2.4/
            Main PID: 313822 (apache2)
               Tasks: 63 (limit: 18941)
            Memory: 32.0M
               CPU: 2.195s
            CGroup: /system.slice/apache2.service
                     ├─313822 /usr/sbin/apache2 -k start
                     ├─313823 /usr/sbin/apache2 -k start
                     ├─313824 /usr/sbin/apache2 -k start
                     └─313825 /usr/sbin/apache2 -k start

         Nov 01 22:13:29 host systemd[1]: Starting The Apache HTTP Server...
         ```
      #### 3. Installation de WSGI
      * Pour ce faire on utilise la fonction suivante :
         ```bash
         sudo apt install libapache2-mod-wsgi-py3
         ```

### 5. **Configuration de l'environnement Python et de l’Application Flask**
   À cette étape on va creer un environnement Python dans notre machine mais avant cela on va cloner le projet sur notre machine :
   #### 1. Clonage du projet sur notre machine
   * On ouvre le dossier www :
      ```bash
      cd /var/www
      ```
   * Ensuite on clone le projet dans ce repectoire :
      ```bash
      git clone <url project>
      ```
      ```bash
      git clone git@github.com:glodielukose/flask-wsgi-apache-setup.git
      ```
   * Si jamais on ne vous donne pas la permission rassurer vous d'avoir les authorisation requises:
      ```bash
      sudo shown -R user /var/www
      ```
   #### 2. Creation de l'environnement virtuelle Python
   * Premierement on entre dans le repectoire de notre projet:
      ```bash
      cd /var/www/flask-wsgi-apache-setup
      ```
   * On crée l'environnement à l'intérieure de notre projet
      ```bash
      python3 -m venv flask-venv
      ```
   * Ensuite on active l'environnemt :
      ```bash
      source flask-env/bin/activate
      ```

   #### 3. Installation des differentes dépendances 
   ```bash
   pip3 install flask
   ```

   une fois que flask est installé on definit la variable d'environnement `FLASK_APP`
   ```bash
   export FLASK_APP=app.py
   ```

   ensuite on test pour voir si tout fonctionne correctement :
   ```bash
   flask run --host=0.0.0.0
   ```
   si tout ce passe bien on aura le resultat suivant :
   ```bash
   root@host:/var/www/flask-wsgi-apache-setup$ flask run --host=0.0.0.0
   * Debug mode: off
   WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
   * Running on all addresses (0.0.0.0)
   * Running on http://127.0.0.1:5000
   * Running on http://127.0.0.1:5000
   Press CTRL+C to quit
   ```


### 6. **Configuration d'Apache**
   - Décrivez comment créer un fichier de configuration Apache pour le site.
   - Expliquez comment configurer le module WSGI pour pointer vers votre application.
     ```apache
     <VirtualHost *:80>
         ServerName votre-domaine.com
         WSGIScriptAlias / /chemin/vers/app.wsgi
         <Directory /chemin/vers/votre/application>
             Require all granted
         </Directory>
         Alias /static /chemin/vers/votre/application/static
         <Directory /chemin/vers/votre/application/static>
             Require all granted
         </Directory>
     </VirtualHost>
     ```
   - Incluez des instructions pour redémarrer Apache pour appliquer les modifications.

### 7. **Tester l'Application**
   - Montrez comment vérifier que le déploiement a bien fonctionné, en accédant à l'adresse IP ou au domaine du serveur.

### 8. **Résolution de Problèmes**
   - Mentionnez les erreurs courantes, comme des permissions ou des erreurs de chemin.
   - Proposez des solutions ou des liens vers des ressources.

### 9. **Ressources Utiles**
   - Ajoutez des liens vers la documentation officielle de Flask, Apache, et WSGI.

### 10. **Licence**
   MIT License

---

**Exemple de README :**

```markdown
# Déployer une Application Flask sur Apache avec WSGI

## Description
Ce guide explique comment déployer une application Flask sur un serveur Apache en utilisant le module WSGI pour une production simple et efficace.

## Table des Matières
- [Prérequis](#prérequis)
- [Installation](#installation)
- [Configuration de l'Application Flask](#configuration-de-lapplication-flask)
- [Configuration d'Apache](#configuration-dapache)
- [Tester l'Application](#tester-lapplication)
- [Résolution de Problèmes](#résolution-de-problèmes)
- [Ressources Utiles](#ressources-utiles)
- [Licence](#licence)

## Prérequis
- Python 3.x
- Flask
- Apache HTTP Server
- Module WSGI (mod_wsgi)
```