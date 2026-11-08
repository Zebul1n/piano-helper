# Piano Helper — Documentation projet

## Documents

| Document | Dossier | Responsable | Statut |
|---|---|---|---|
| EDB — Expression de Besoin | [EDB/](EDB/README.md) | Porteur du projet | v0.7 — décisions du porteur du 2026-11-07 et du 2026-11-08 appliquées ; 45 modifications des relecteurs du PRD et 19 du DAT (MDAT-01 à MDAT-19) appliquées ; question ouverte : QO-54 |
| PRD — Product Requirements Document | [PRD/](PRD/README.md) | `product-owner` | v0.4 — décisions du porteur du 2026-11-07 et du 2026-11-08 intégrées ([09](PRD/09-decisions-a-prendre.md)) ; 23 modifications du DAT appliquées (MDAT-20 à MDAT-42) ; actions restantes au porteur |
| Spécifications UX | `UX/` | `designer-ux` | À rédiger |
| DAT — Dossier d'Architecture Technique (+ ADR) | [DAT/](DAT/README.md) | `architecte` | v0.3 — à relire : cinq relectures intégrées (116 remarques), 24 ADR (12 acceptées, 12 proposées à confirmer par un POC) ; décisions du porteur prises le 2026-11-08 sauf QO-54 ([14](DAT/14-decisions-a-prendre.md)) ; 42 modifications de l'EDB et du PRD appliquées ([15](DAT/15-modifications-edb-prd-proposees.md)) |
| Rapports de POC | `POC/` | `expert-audio-mir` | À rédiger |
| Stratégie et plans de test | `QA/` | `qa-testeur` | À rédiger |
| Juridique (licences, RGPD, CGU) | `JURIDIQUE/` | `juriste-conformite` | À rédiger |
| Build, CI, publication | [DEVOPS/](DEVOPS/README.md) | `devops-release` | Guide d'installation du poste |

Chaque document a son propre sous-dossier, avec un `README.md` servant de sommaire. Les identifiants de l'EDB (EF-xxx-nn, ENF-xxx-nn, QO-nn, POC-nn) sont repris dans tous les documents pour assurer la traçabilité.

## Cadre du projet

- **Android d'abord** (minimum Android 13), iOS à terme (noyau métier portable).
- **Open source**, licence GPL-3.0-or-later.
- **Porteur unique**, assisté d'une équipe d'agents IA.
- **Matériel de test** : un téléphone Android 14, l'émulateur Android, un piano droit acoustique (pas de piano numérique).
- **Budget nul** : outils, composants et services gratuits et/ou open source uniquement.
- **Pas d'échéance** : la fiabilité de l'analyse prime sur la vitesse.

## Équipe d'agents

Les agents sont définis dans [`.claude/agents/`](../.claude/agents/). La session principale de Claude Code joue le rôle de **chef de projet** : elle découpe le travail, sollicite les agents, fait relire les livrables par les rôles concernés et soumet les décisions au porteur du projet.

| Agent | Rôle | Intervient sur |
|---|---|---|
| `product-owner` | Product Owner | PRD, epics, user stories, priorisation, traçabilité EDB |
| `architecte` | Architecte logiciel | DAT, ADR, choix technologiques, licences des composants (avec le juriste) |
| `expert-audio-mir` | Expert audio et informatique musicale | Transcription, alignement, détection des erreurs, corpus, POC-01 / POC-05 |
| `pedagogue-piano` | Pianiste et professeur | Justesse musicale et pédagogique, tolérances, ton des retours, morceaux de démonstration |
| `designer-ux` | Designer UX/UI | Parcours, maquettes, design system, accessibilité |
| `dev-android` | Développeur Android | Application Kotlin / Jetpack Compose, intégration JNI, permissions |
| `dev-natif-cpp` | Développeur noyau C++ | Audio temps réel, MIDI natif, rendu, inférence, analyse |
| `qa-testeur` | Responsable qualité | Stratégie de test, revue critique, campagnes, anomalies |
| `juriste-conformite` | Juriste | Licences open source, RGPD, droits des partitions, stores, CGU |
| `devops-release` | DevOps | Dépôt, build, CI gratuite, signature, distribution |

### Relectures croisées systématiques

| Livrable | Relu par |
|---|---|
| PRD | `pedagogue-piano`, `designer-ux`, `qa-testeur`, `juriste-conformite` |
| Spécifications UX | `pedagogue-piano`, `product-owner` |
| DAT et ADR | `expert-audio-mir`, `dev-android`, `dev-natif-cpp`, `juriste-conformite` (licences) |
| Rapports de POC | `qa-testeur`, `architecte` |
| Code | `qa-testeur` |

### Enchaînement

```
EDB ──► PRD ──► UX ──┐
          │          ├──► POC ──► Go / No-go ──► Développement MVP ──► Bêta ──► V1
          └──► DAT ──┘
```

Les décisions structurantes (périmètre, licence du projet, distribution, publication) restent celles du porteur du projet.
