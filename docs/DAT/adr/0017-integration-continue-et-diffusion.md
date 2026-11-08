# ADR-0017 — Intégration continue et diffusion

| Statut | Date | Exigences |
|---|---|---|
| Acceptée (QO-45 décidée le 2026-11-08) | 2026-11-07, révisée et acceptée le 2026-11-08 | ENF-QA-02, ENF-CONF-03, ENF-COMP-02 ; US-CNF-01 à US-CNF-04, US-QUA-02, US-QUA-03 CA4 ; QO-20, QO-35 |

## Contexte

Budget nul ; dépôt public ; diffusion d'APK sur la page de publication du dépôt ; contrôles automatiques nombreux ; émulateur pour les tests instrumentés ; clé de diffusion qui conditionne les mises à jour et l'enregistrement auprès de Google.

## Options étudiées

| Option | Faits |
|---|---|
| A — GitHub Actions + Releases | Exécuteurs standard gratuits pour les dépôts publics (x64 et arm64) ; émulateur accéléré ; environnements avec relecteur requis ; diffusions immuables [VÉRIFIÉ 2026-11-07 et par `devops-release`] |
| B — GitLab CI | 400 minutes par mois au niveau gratuit |
| C — Codeberg | CI sur demande ou en alpha |

## Décision

**Option A**, retenue par le porteur le 2026-11-08 (QO-45, [fichier 14](../14-decisions-a-prendre.md)), avec les conditions de `devops-release` :
1. exécuteurs **`ubuntu-24.04`** figés et compilateur explicite clang-18 (DEV-08) ;
2. secrets de signature dans un **environnement `release`** limité aux étiquettes `v*`, relecteur requis = le porteur ; permissions minimales des workflows (DEV-09) ;
3. branche principale protégée (DEV-18) ;
4. diffusions immuables, empreinte du certificat publiée, preuve de reconstruction hors ligne à chaque étiquette (DEV-09, DEV-17) ;
5. copie de secours du dépôt (Software Heritage ou miroir en lecture).

Chaîne détaillée : [10 §4](../10-build-ci-et-distribution.md#4-intégration-continue).

## Conséquences

- Le dépôt est publié sous le compte GitHub du porteur, dont le nom (Zebul1n) fixe aussi l'identifiant de l'application `io.github.zebul1n.pianohelper` (QO-46) : ce compte ne doit jamais être renommé.
- Dépendance à une plateforme propriétaire (Microsoft), limitée par la copie de secours du dépôt (condition 5).
- Options B et C écartées : avec elles, le banc complet et les tests instrumentés auraient dû passer sur le poste du porteur avant chaque diffusion.
