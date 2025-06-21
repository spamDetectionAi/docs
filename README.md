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

## Partie Back-end : Spring Boot & Web

| Dépendance                      | Rôle                                                                                   |
|--------------------------------|----------------------------------------------------------------------------------------|
| spring-boot-starter-web         | Permet de créer des applications web classiques (REST API, MVC) avec Tomcat embarqué.  |
| spring-boot-starter-websocket   | Ajoute le support des WebSockets pour la communication temps réel (ex : chat, notifications). |
| spring-boot-starter-data-jpa    | Intègre JPA avec Spring Data, pour interagir avec des bases relationnelles (ORM).      |
| spring-boot-starter-jdbc        | Fournit le support JDBC pour interagir avec les bases relationnelles via SQL pur.      |
| spring-boot-starter-webflux     | Permet de créer des applications web réactives (non-bloquantes) avec Spring WebFlux.   |
| spring-boot-starter-validation  | Active la validation des données (ex : @Valid, @NotNull, etc.).                        |

### Sécurité & Authentification

| Dépendance                            | Rôle                                                                                  |
|-------------------------------------|---------------------------------------------------------------------------------------|
| spring-boot-starter-oauth2-resource-server | Implémente un serveur de ressources OAuth2 (authentifie les requêtes via JWT ou tokens OAuth2). |
| spring-security-test                 | Fournit des utilitaires pour tester la sécurité Spring (mock utilisateur, autorisations, etc.). |
| io.jsonwebtoken:jjwt-*               | Librairie pour créer, parser et valider des JWT. Modules variés pour JWT.             |

### Base de données

| Dépendance                  | Rôle                                                          |
|-----------------------------|---------------------------------------------------------------|
| org.postgresql:postgresql    | Le driver JDBC pour se connecter à une base PostgreSQL.       |
| com.h2database:h2            | Base de données embarquée utilisée pour les tests.            |
| spring-boot-starter-data-mongodb | Support MongoDB avec Spring Data pour manipuler des documents NoSQL. |

---

## Partie Front-end : React + Vite

| Dépendance               | Rôle                                                           |
|--------------------------|----------------------------------------------------------------|
| react, react-dom          | Bibliothèques de base pour créer des interfaces utilisateurs React. |
| vite                     | Serveur de développement ultra-rapide et bundler moderne.     |
| @vitejs/plugin-react     | Plugin Vite pour supporter React (JSX, Fast Refresh, etc.).    |

### Design UI

| Dépendance          | Rôle                                                     |
|---------------------|----------------------------------------------------------|
| tailwindcss         | Framework CSS utilitaire pour créer des interfaces rapidement. |
| @tailwindcss/vite   | Plugin Vite pour intégrer Tailwind CSS facilement.       |
| tailwindcss-animate | Fournit des classes utilitaires pour les animations.    |
| tailwind-merge      | Fusionne proprement plusieurs classes Tailwind (évite les conflits CSS). |

### Formulaire et validation

| Dépendance           | Rôle                                                                 |
|----------------------|----------------------------------------------------------------------|
| react-hook-form      | Gestion légère et performante des formulaires dans React.             |
| @hookform/resolvers  | Connecteurs entre react-hook-form et des schémas de validation (zod, yup, etc.). |
| zod                  | Librairie de validation de schéma typée, bien intégrée avec TypeScript.|

### Text Editor

| Dépendance  | Rôle                              |
|-------------|-----------------------------------|
| quill       | Moteur de rich-text editor.        |
| react-quill | Wrapper React autour de Quill.     |

### Communication réseau / temps réel

| Dépendance      | Rôle                                                  |
|-----------------|-------------------------------------------------------|
| axios           | Client HTTP moderne pour faire des requêtes API.      |
| js-cookie       | Gestion facile des cookies (ex : stocker des tokens). |
| sockjs-client   | Implémentation WebSocket fallback-friendly pour STOMP.|
| stompjs         | Client STOMP pour gérer la communication temps réel.  |

### State Management

| Dépendance | Rôle                                                          |
|------------|---------------------------------------------------------------|
| zustand    | Gestion d’état simple, légère et performante dans React. Alternative moderne à Redux. |

### Thèmes & Routes

| Dépendance    | Rôle                                                   |
|---------------|--------------------------------------------------------|
| next-themes   | Gestion des thèmes clairs/sombres dans React.          |
| react-router  | Système de routage pour SPA React (version moderne 7). |

### Développement & typage

| Dépendance                                                  | Rôle                                                    |
|-------------------------------------------------------------|---------------------------------------------------------|
| typescript                                                  | Typage statique pour JavaScript.                         |
| @types/*                                                    | Déclarations de types TypeScript pour bibliothèques JS. |
| @types/stompjs, @types/sockjs-client                       | Typages pour STOMP et SockJS.                            |
| eslint, @eslint/js, typescript-eslint, eslint-plugin-react-hooks, eslint-plugin-react-refresh | Outils pour analyser, formater et corriger le code automatiquement (linting). |
| globals                                                    | Fournit des variables globales communes pour ESLint.     |

---

## Côté serveur mail : Postfix & Dovecot

### Postfix : MTA (Mail Transfer Agent)

| Protocole | Port(s)                 | Description                                                                                       |
|-----------|------------------------|-------------------------------------------------------------------------------------------------|
| SMTP      | 25 (standard), 587 (submission), 465 (SMTPS) | Utilisé pour envoyer des emails vers d'autres serveurs ou depuis un client (authentifié via 587 ou 465). |

### Dovecot : MDA & IMAP Server

| Protocole | Port(s)           | Description                                                                              |
|-----------|------------------|------------------------------------------------------------------------------------------|
| IMAP      | 143 (IMAP), 993 (IMAPS) | Permet de lire les emails à distance en laissant les mails sur le serveur (synchronisation multi-appareils). |
| POP3      | 110 (POP3), 995 (POP3S) | Permet de télécharger les mails localement (souvent suppression locale).                |
| LMTP      | Socket local ou port 24 | Utilisé par Postfix pour livrer localement les mails à Dovecot (plus moderne que procmail). |

### Interaction Postfix & Dovecot

| Direction               | Composant | Protocole                     |
|-------------------------|-----------|------------------------------|
| Envoi d’un email sortant | Postfix → Internet | SMTP (25, 465, 587)       |
| Réception d’un email entrant | Internet → Postfix | SMTP (25)                  |
| Livraison locale         | Postfix → Dovecot | LMTP                        |
| Lecture emails utilisateur | Dovecot → Client mail (Thunderbird, Outlook…) | IMAP (143/993) ou POP3 (110/995) |

