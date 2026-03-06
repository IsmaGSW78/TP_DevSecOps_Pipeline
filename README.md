# 🛡️ TP DevSecOps - Pipeline CI/CD Sécurisé

![Security Status](https://github.com/IsmaGSW78/TP_DevSecOps_Pipeline/workflows/DevSecOps%20Pipeline/badge.svg)

Ce dépôt contient le rendu final du TP de DevSecOps. L'objectif de ce projet est de démontrer l'implémentation du principe de **Shift-Left Security** en sécurisant une application Node.js vulnérable à l'aide d'un pipeline d'intégration et de déploiement continus (CI/CD) sur GitHub Actions.

## 🏗️ Architecture du Pipeline de Sécurité

Chaque *push* ou *pull request* déclenche un workflow strict composé des étapes suivantes :

1. **🛠️ Build & Package** : Création d'une image Docker optimisée (Alpine, utilisateur non-root).
2. **🔍 SAST (Static Application Security Testing)** : Analyse statique du code source via **Semgrep** et **GitHub CodeQL** pour détecter les failles logiques (ex: Injections SQL).
3. **📦 SCA (Software Composition Analysis)** : Audit des dépendances avec **npm audit** pour bloquer les composants obsolètes ou vulnérables (CVE).
4. **🔑 Secret Scanning** : Analyse de l'historique Git via **Gitleaks** pour prévenir la fuite de clés d'API ou de mots de passe.
5. **🐳 Container Security** : Scan de l'image Docker avec **Trivy** pour s'assurer que l'environnement système est sain.
6. **⚡ DAST (Dynamic Application Security Testing)** : Attaque automatisée de l'application en cours d'exécution via **OWASP ZAP** pour identifier les vulnérabilités de configuration.
7. **🚦 Security Gate** : Mécanisme de blocage. Le pipeline échoue formellement (`exit 1`) si l'une des étapes précédentes détecte une faille critique.

## 🚀 Instructions d'exécution en local

Si vous souhaitez faire tourner l'application sécurisée sur votre machine, suivez ces étapes :

### 1. Prérequis
- [Docker](https://www.docker.com/) installé sur votre machine.
- Git.

### 2. Installation
Clonez le dépôt et naviguez dans le dossier :
```bash
git clone [https://github.com/IsmaGSW78/TP_DevSecOps_Pipeline.git](https://github.com/IsmaGSW78/TP_DevSecOps_Pipeline.git)
cd TP_DevSecOps_Pipeline