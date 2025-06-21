# Maily: Système de messagerie avec détection de spam IA

## Description du projet

**Maily** est un système de messagerie en ligne, similaire à Gmail ou Outlook, avec une fonctionnalité avancée de détection de spam basée sur un modèle d'intelligence artificielle. L'objectif principal est d'offrir aux utilisateurs non seulement une messagerie classique, mais aussi un système capable de détecter automatiquement les spams et de s'adapter aux préférences personnelles de chaque utilisateur grâce à un apprentissage continu.

L'application est conçue pour être performante, scalable et sécurisée, avec une architecture moderne full-stack.

---

## Fonctionnalités principales

- Envoi et réception de mails entre utilisateurs sur le même domaine (maily-taltic)
- Gestion multi-utilisateurs avec authentification sécurisée (Spring Security + JWT)
- Détection automatique des spams via un modèle IA intégré
- Personnalisation de la détection spam par utilisateur : possibilité de reclasser un mail en spam/non spam pour réentraîner le modèle
- Support du protocole SMTP et IMAP via un serveur mail Postfix interne
- Communication en temps réel des messages grâce à WebSocket
- Stockage des données avec une architecture hybride Postgres + MongoDB
- Traduction automatique des mails dans plusieurs langues via un service séparé FastAPI
- Interface utilisateur en React avec gestion des emails, pagination, envoi et affichage
- Déploiement complet sur AWS EC2 avec CI/CD et analyse qualité de code (SonarCloud)

---

## Architecture technique

### 1. Frontend

- **Technologies** : React + Vite  
- Rôle : Interface utilisateur pour gestion des mails, envoi/réception, affichage des boîtes, et interaction avec le backend via API REST et WebSocket.

### 2. Backend

- **Technologie principale** : Spring Boot (Java)  
- Rôle :  
  - Gestion des utilisateurs (authentification, autorisation)  
  - Gestion des mails (réception, envoi, stockage)  
  - Orchestration des appels aux services (modèle IA, serveur mail)  
  - Exposition des API REST et WebSocket pour la communication en temps réel

### 3. Bases de données

- **PostgreSQL** (base relationnelle)  
  - Stockage des informations utilisateurs : emails, mots de passe, profils, métadonnées  
  - Gestion des données nécessitant des relations complexes et sécurisées

- **MongoDB** (base NoSQL)  
  - Stockage rapide et flexible des messages et pièces jointes  
  - Optimisé pour les requêtes rapides sans jointures lourdes, indispensable dans une application temps réel

### 4. Serveur mail

- **Postfix**  
  - Agent SMTP/IMAP pour l'envoi et la réception de mails selon les protocoles standards  
  - Gestion des communications entre le domaine maily-taltic et les domaines externes

### 5. Modèle IA pour détection spam

- Implémenté en Python avec **FastAPI**  
- Déployé via **Docker** (Docker Hub disponible pour test)  
- Reçoit le contenu des mails et retourne un verdict spam/non spam  
- Intégré en API que le backend Spring Boot consomme  
- Possibilité d'adapter et réentraîner le modèle en fonction des retours utilisateurs

---

## Flux fonctionnel principal

1. Un utilisateur compose un mail dans l'interface React.  
2. Le mail est envoyé via une requête HTTP ou WebSocket au backend Spring Boot.  
3. Le backend sauvegarde le mail dans MongoDB (pour les messages) et Postgres (pour les utilisateurs).  
4. Le backend appelle le modèle IA via API pour détecter si le mail est un spam.  
5. Le verdict est renvoyé au frontend et stocké.  
6. Le mail est transmis via Postfix si destiné à un autre domaine.  
7. Les mails entrants sont réceptionnés par Postfix et stockés pour affichage ultérieur.  
8. L'utilisateur peut reclasser un mail (spam/non spam), ce qui entraîne un réentrainement personnalisé du modèle IA.

---

## Pourquoi deux bases de données ?

- **PostgreSQL** est idéal pour gérer les relations complexes et les données sécurisées utilisateurs (mots de passe, email, profils).  
- **MongoDB** est choisi pour stocker les messages eux-mêmes à cause de son schéma flexible et ses performances sur les lectures/écritures rapides, particulièrement dans un contexte temps réel.  
- L'usage combiné améliore la performance globale, notamment en réduisant les temps de jointures lourdes qui seraient coûteux sur un volume important de messages.

---

## Limitations et travaux en cours

- La messagerie interne au domaine maily-taltic est fonctionnelle : envoi et réception entre utilisateurs du même domaine.  
- Envoi de mails vers d'autres domaines est bloqué à cause du verrouillage du port SMTP (port 25) sur AWS EC2, ce qui nécessite l'intégration d'un relais SMTP externe (SMTP relay).  
- Réception des mails externes est fonctionnelle, mais leur affichage en front-end est en cours de finalisation.  
- L'interface React n'est pas encore totalement responsive (optimisée pour mobiles/tablettes).  
- La traduction automatique via le service FastAPI est fonctionnelle mais peut être lente au premier appel à cause du "cold start" sur l'hébergement Render en free tier.  
- Améliorations futures prévues : optimisation de l'UI, finalisation de la réception mails externes, intégration SMTP relay, amélioration de la rapidité des traductions.

---

## Sécurité

- Utilisation de Spring Security avec JWT pour sécuriser les API backend.  
- Gestion des mots de passe stockés de manière sécurisée en base PostgreSQL.  
- Analyse de qualité et de sécurité du code automatisée via SonarCloud intégrée dans le pipeline CI/CD.

---

## Déploiement

- Backend Spring Boot déployé sur AWS EC2  
- IA modèle déployé via Docker sur Render  
- Frontend React déployé via pipeline CI/CD  
- Serveur mail Postfix configuré sur le serveur EC2  
- Pipeline CI/CD complet avec tests, qualité de code, et analyse sécurité (SonarCloud)

---

