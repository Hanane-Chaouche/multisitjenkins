# 📚 Plateforme WikiJS Multi-sites

![CI/CD](https://github.com/Hanane-Chaouche/wikijs-multisite/actions/workflows/deploy.yml/badge.svg)

Ce projet déploie une plateforme multi-instances de [Wiki.js](https://js.wiki/) avec Docker Compose.  
Chaque instance est isolée, avec sa propre base PostgreSQL, et accessible via un reverse proxy Nginx.  
Le tout est automatisé avec **GitHub Actions**.

---

## 📑 Sommaire

- [📁 Structure du projet](#-structure-du-projet)
- [🚀 Objectif](#-objectif)
- [🛠️ Prérequis](#️-prérequis)
- [▶️ Démarrage rapide](#-démarrage-rapide)
- [📦 Dépendances](#-dépendances)
- [📘 Instances Wiki.js](#-instances-wikijs)
- [🤖 Déploiement automatique](#-déploiement-automatique)
- [👤 Auteur](#-auteur)

---

## 📁 Structure du projet

```
instances/
├── wiki1/
├── wiki2/
└── wiki-public/
nginx/
├── docker-compose.yml
└── wikijs.conf
.github/
└── workflows/
    └── deploy.yml
README.md
```


## 🚀 Objectif

- 🧱 Déployer plusieurs instances Wiki.js isolées via Docker Compose  
- 🌐 Gérer les accès via Nginx  
- 🤖 Automatiser le déploiement avec GitHub Actions  

---

## 🛠️ Prérequis

- Docker & Docker Compose  
- VM Ubuntu (par exemple sur Azure)  
- Noms de domaines ou sous-domaines pointant vers la VM  
- Clé SSH pour GitHub Actions  

---

## ▶️ Démarrage rapide

 bash
cd wikijs-multisite

# Lancer les 3 instances de Wiki.js
docker compose -f instances/wiki1/docker-compose.yml up -d
docker compose -f instances/wiki2/docker-compose.yml up -d
docker compose -f instances/wiki-public/docker-compose.yml up -d

# Lancer le reverse proxy Nginx
docker compose -f nginx/docker-compose.yml up -d

📦 Dépendances

    Docker

    Docker Compose

    Wiki.js

    GitHub Actions

📘 Instances Wiki.js
Instance	Port interne	Description
wiki1	3001	Wiki privé du site 1
wiki2	3002	Wiki privé du site 2
wiki-public	3003	Wiki public général

🤖 Déploiement automatique

Chaque push sur la branche main déclenche un workflow GitHub Actions (.github/workflows/deploy.yml) qui :

    se connecte à la VM via SSH

    exécute git pull et redémarre les services Docker

    met à jour automatiquement Nginx et les wikis

👤 Auteur

Hanane Chaouche
Étudiante en développeuse en Mega données – Collège Bois-de-Boulogne
Projet réalisé dans un contexte DevOps avec CI/CD et documentation technique.
