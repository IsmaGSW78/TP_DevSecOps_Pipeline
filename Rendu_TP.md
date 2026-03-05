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

![alt text](image.png)

Voici le bilan des vulnérabilités détectées :

| Outil | Résultat | Vulnérabilités trouvées |
|-------|----------|-------------------------|
| **Semgrep (SAST)** | ❌ ÉCHEC | Présence de secrets hardcodés et manque de validation des requêtes. |
| **npm audit (SCA)** | ❌ ÉCHEC | Dépendances obsolètes contenant des CVE connues. |
| **Gitleaks (Secrets)** | ❌ ÉCHEC | Les fausses clés d'API ont été repérées dans l'historique du code source. |
| **Trivy (Container)** | ❌ ÉCHEC | Vulnérabilités de niveau "CRITICAL" trouvées dans l'image de base Docker. |

## Section 4 : Remédiation et Corrections
Suite à l'analyse des failles, j'ai appliqué les correctifs de sécurité suivants pour rendre l'application robuste et valider le pipeline :

### 1. Sécurisation des dépendances (SCA)
- Mise à jour des librairies obsolètes (`express`, `jsonwebtoken`) vers des versions sécurisées.
- Ajout d'outils de sécurité standards : `helmet` (sécurité des en-têtes HTTP), `express-rate-limit` (protection contre le brute-force) et `express-validator` (validation des entrées).

![alt text](image-4.png)

### 2. Sécurisation du code (SAST & Secrets)
- Suppression stricte des secrets hardcodés (clés Stripe, SendGrid, DB) remplacés par l'utilisation de variables d'environnement (`process.env`) via la librairie `dotenv`.
- Implémentation du filtrage des entrées utilisateurs lors du login pour éviter les injections.
- Création d'un fichier `.env.example` et ajout de `.env` dans le `.gitignore` pour éviter toute future fuite sur GitHub.

### 3. Durcissement du conteneur (Container Security)
- Remplacement de l'image de base `node:14` par `node:22-alpine`, une image beaucoup plus légère et exempte de vulnérabilités critiques connues.
- Création et utilisation d'un utilisateur non-root (`nodejs` avec l'ID 1001) pour appliquer le principe de moindre privilège dans le conteneur.
- Ajout d'une directive `HEALTHCHECK` pour surveiller l'état de santé du conteneur.

![alt text](image-3.png)

### 4. Gestion sécurisée de la CI/CD
- Configuration des secrets de production (`JWT_SECRET`, `ADMIN_USER`, `ADMIN_PASS`) directement dans l'interface sécurisée de GitHub (*Settings > Secrets and variables > Actions*) afin que le pipeline puisse tester l'application sans exposer les clés.

![alt text](image-2.png)

## Conclusion
Après le commit de ces corrections (`fix: Apply all security fixes`), le pipeline DevSecOps s'est exécuté avec succès. **Tous les jobs de sécurité (SAST, SCA, Secrets, Container Scan) sont passés au vert ! ✅**