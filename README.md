# Configuration Docker pour OpenVPN Client avec Tinyproxy

Ce projet configure un client OpenVPN basé sur Docker utilisant `dperson/openvpn-client` et un proxy HTTP utilisant `vimagick/tinyproxy`. La configuration route tout le trafic proxy via le VPN, offrant un moyen sécurisé d'accéder à Internet via un proxy HTTP. Il utilise Docker Compose pour un déploiement et une gestion faciles.

## Prérequis

- **Docker** et **Docker Compose** installés sur votre système.
- Un fichier de configuration OpenVPN (`.ovpn`) ou les détails du serveur (adresse du serveur, nom d'utilisateur, mot de passe, port).
- Un répertoire local pour les fichiers de configuration VPN (par exemple, `/path/to/vpn-config/`).
- Connaissances de base sur Docker et les configurations VPN.

## Instructions de configuration

### 1. Préparer la configuration VPN
- Placez votre fichier de configuration OpenVPN dans le dossier `vpn` (par exemple, `freevpn.ovpn`) dans ce répertoire.
- Modifiez le fichier `.ovpn` :
  ```text
  auth-user-pass /vpn/freevpn.ovpn
  ```
- Créez un fichier `auth.txt` avec vos identifiants VPN :
  ```text
  votre_nom_utilisateur
  votre_mot_de_passe
  ```
- Sécurisez les fichiers :
  ```bash
  chmod 600 /path/to/vpn-config/*
  ```

### 2. Configurer Docker Compose
- Copiez le fichier `docker-compose.yml` de ce dépôt dans votre répertoire de projet.
- Mettez à jour la section `volumes` pour pointer vers votre répertoire de configuration VPN :
  ```yaml
  volumes:
    - /path/to/vpn-config:/vpn
  ```
- Facultativement, utilisez la variable d'environnement `VPN` au lieu d'un fichier `.ovpn` :
  ```yaml
  environment:
    - TZ=Europe/Paris
    - DNS=1.1.1.1
  ```

### 3. Déployer les conteneurs
- Accédez au répertoire du projet :
  ```bash
  cd /path/to/project
  ```
- Lancez les conteneurs :
  ```bash
  docker-compose up -d
  ```

### 4. Vérifier la connectivité
- Vérifiez l'état des conteneurs :
  ```bash
  docker ps
  ```
- Vérifiez la connexion VPN :
  ```bash
  docker logs vpn
  ```
  Recherchez `Initialization Sequence Completed`.
- Testez la connectivité Internet :
  ```bash
  docker exec vpn curl -s http://ifconfig.me
  docker exec tinyproxy curl -s http://ifconfig.me
  ```
  Les deux devraient retourner l'IP publique du serveur VPN.
- Testez le proxy HTTP :
  ```bash
  curl --proxy http://<host-ip>:8888 http://ifconfig.me
  ```
  Configurez un navigateur pour utiliser le proxy à `<host-ip>:8888`.

### 5. Notes de sécurité
- Restreignez l'accès à Tinyproxy :
  ```yaml
  environment:
    - ALLOWED=192.168.1.0/24
  ```
- Sécurisez les fichiers de configuration :
  ```bash
  chmod 600 /path/to/vpn-config/*
  ```
- Limitez le port `8888` avec un pare-feu :
  ```bash
  sudo ufw allow from 192.168.1.0/24 to any port 8888
  ```

## Contribuer
N'hésitez pas à soumettre des problèmes ou des demandes de tirage pour des améliorations ou des corrections de bugs.

## Licence
Ce projet est sous licence MIT.
