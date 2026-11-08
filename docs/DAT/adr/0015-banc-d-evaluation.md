# ADR-0015 — Banc d'évaluation : exécutable C++ sur corpus versionné

| Statut | Date | Exigences |
|---|---|---|
| Acceptée (révisée en v0.2) | 2026-11-07, révisée le 2026-11-08 | ENF-QA-01, ENF-QA-02, ENF-MAINT-01 ; US-QUA-01, US-QUA-02, US-QUA-05 ; QO-37 |

## Contexte

Le banc garde la porte à partir d'I2 : il doit mesurer le code embarqué, de façon reproductible, en CI (corpus public) et sur le poste (corpus privé), et rejouer le corpus micro sur le téléphone.

## Options étudiées

| Option | Pour | Contre |
|---|---|---|
| A — Exécutable C++ lié au noyau | Même code que le téléphone ; compilable pour Android ; pas de Python en CI | Métriques à implémenter |
| B — Banc Python | Écosystème MIR | Python en CI ; deux chemins |
| C — Banc dans l'application | Conditions réelles | Lent ; pas de CI |

## Décision

**Option A**, avec les précisions des revues :
- corpus audio normalisé à 48 kHz ; blocs de taille aléatoire à graine fixe ; mode attente simulé avec 3 s de contexte (DN-19, DN-07, MIR-08) ;
- version du compilateur et de l'image de CI écrites dans le rapport ; `compare` refuse de comparer des environnements différents (DEV-08) ;
- règle de non-régression **en effectifs**, avec garde par sous-corpus et même règle pour les validations indues (MIR-17, [08 §3](../08-strategie-de-test-et-banc-d-evaluation.md#3-règle-de-non-régression)) ;
- équivalence ordinateur / téléphone à deux niveaux ([ADR-0022](0022-reproductibilite-ordinateur-telephone.md)) ;
- rapports d'incrément archivés durablement (DEV-15) ;
- métriques validées une fois contre `mir_eval` 0.8.2 dans WSL 2.

## Conséquences

Python reste un outil de prototypage, confiné à WSL 2 (décision QO-48).
