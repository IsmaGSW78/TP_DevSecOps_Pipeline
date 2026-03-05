# 🎓 Rendu TP Noté - DevSecOps
**Nom :** Charni
**Prénom :** Ismaël
**Date :** 4 Mars 2026


## 🛠️ Préparation de l'environnement

## 1. Setup de l'environnement
- Initialisation du dépôt Git et création de l'arborescence (`src/`, `.github/workflows/`).
- Création de l'application Node.js vulnérable (`server.js`, `package.json`, `Dockerfile`).
- **🛡️ Fait marquant DevSecOps :** Lors du tout premier `git push`, la protection native de GitHub (*Secret Scanning Push Protection*) a bloqué l'envoi. Le scanneur a immédiatement détecté les clés d'API en clair (Stripe, SendGrid) dans le fichier `server.js`. J'ai dû autoriser manuellement ce push (ou modifier légèrement les clés) pour poursuivre le TP. Cela prouve l'efficacité du blocage en amont !

![alt text](image-1.png)

## Section 2 : Création du Pipeline DevSecOps
- Mise en place du workflow GitHub Actions via le fichier `.github/workflows/security.yml`.
- Intégration de 4 étapes d'analyse de sécurité exécutées sur un environnement `ubuntu-latest` :
  1. **SAST** (Semgrep)
  2. **SCA** (npm audit)
  3. **Recherche de secrets** (Gitleaks)
  4. **Scan de conteneur** (Trivy)

## Section 3 : Analyse des résultats (État initial)
Comme attendu, le premier lancement du pipeline s'est soldé par un échec général. Les rapports JSON ont été récupérés via les *Artifacts* de GitHub Actions. 

Voici le bilan des vulnérabilités détectées :

| Outil | Résultat | Vulnérabilités trouvées |
|-------|----------|-------------------------|
| **Semgrep (SAST)** | ❌ ÉCHEC | Présence de secrets hardcodés et manque de validation des requêtes. |
| **npm audit (SCA)** | ❌ ÉCHEC | Dépendances obsolètes contenant des CVE connues. |
| **Gitleaks (Secrets)** | ❌ ÉCHEC | Les fausses clés d'API ont été repérées dans l'historique du code source. |
| **Trivy (Container)** | ❌ ÉCHEC | Vulnérabilités de niveau "CRITICAL" trouvées dans l'image de base Docker. |

![alt text](image.png)