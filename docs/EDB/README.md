# Piano Helper — Expression de Besoin (EDB)

| Élément | Valeur |
|---|---|
| Projet | Piano Helper (nom de travail) |
| Document | Expression de Besoin |
| Version | 0.7 — décisions du porteur du 2026-11-08 (QO-44 à QO-53, QO-55) et modifications du DAT (MDAT-01 à MDAT-19) appliquées |
| Date | 2026-11-08 |
| Statut | À relire / à valider ; question ouverte : QO-54 (avant la V1) ; actions restantes au porteur en [12 §0](12-risques-hypotheses-questions.md#0-décisions-prises) |

## Objet

Ce dossier rassemble l'expression de besoin d'une application mobile d'aide à l'apprentissage du piano. L'application permet de :

1. **charger une partition** ;
2. **la visualiser et l'écouter** dans l'application ;
3. **écouter l'apprenti pianiste** pendant qu'il joue ;
4. **comparer son jeu à la partition** et lui montrer **où sont les erreurs**.

L'EDB décrit le **quoi** et le **pourquoi**. Il ne fige pas le **comment** : les pistes techniques mentionnées (fichier 07) servent à évaluer la faisabilité et les risques, pas à imposer une solution.

## Sommaire

| # | Fichier | Contenu |
|---|---|---|
| 01 | [Contexte et objectifs](01-contexte-et-objectifs.md) | Vision, problème adressé, objectifs, indicateurs de succès, hors périmètre |
| 02 | [Utilisateurs et personas](02-utilisateurs-et-personas.md) | Cibles, personas, besoins par profil |
| 03 | [Parcours utilisateurs](03-parcours-utilisateurs.md) | Scénarios de bout en bout |
| 04 | [Périmètre fonctionnel](04-perimetre-fonctionnel.md) | Carte des modules, priorisation MoSCoW, découpage MVP / V1 / V2 |
| 05 | [Exigences fonctionnelles](05-exigences-fonctionnelles.md) | Exigences détaillées par module, avec critères d'acceptation |
| 06 | [Analyse du jeu et détection des erreurs](06-analyse-du-jeu-et-erreurs.md) | Cœur métier : typologie des erreurs, tolérances, score, restitution |
| 07 | [Contraintes et pistes techniques](07-contraintes-et-pistes-techniques.md) | Formats, rendu, audio, MIDI, alignement, frameworks, POC à mener |
| 08 | [Exigences non fonctionnelles](08-exigences-non-fonctionnelles.md) | Performance, latence, compatibilité, hors ligne, accessibilité, sécurité |
| 09 | [Modèle de données](09-modele-de-donnees.md) | Entités métier et relations |
| 10 | [Écrans et ergonomie](10-ecrans-et-ergonomie.md) | Inventaire des écrans, navigation, maquettes fil de fer |
| 11 | [Juridique et conformité](11-juridique-et-conformite.md) | Droits d'auteur, RGPD, mineurs, licences logicielles |
| 12 | [Risques, hypothèses et questions ouvertes](12-risques-hypotheses-questions.md) | Ce qui reste à décider |
| 13 | [Jalons et démarche](13-jalons-et-demarche.md) | Phasage, POC prioritaires, critères de passage |
| 14 | [Glossaire](14-glossaire.md) | Vocabulaire musical et technique |

## Conventions

### Identifiants des exigences

`EF-<MODULE>-<NN>` pour les exigences fonctionnelles, `ENF-<THEME>-<NN>` pour les non fonctionnelles.

| Code module | Module |
|---|---|
| IMP | Import de partition |
| BIB | Bibliothèque |
| VIS | Visualisation de la partition |
| LEC | Lecture audio de la partition |
| PRA | Modes de pratique |
| ECO | Écoute du pianiste (captation) |
| ANA | Analyse et comparaison |
| RES | Restitution des erreurs |
| PRO | Progression et historique |
| CPT | Compte et paramètres |
| PRF | Espace professeur (V2) |

### Priorités (MoSCoW)

| Priorité | Signification |
|---|---|
| **M** — Must | Indispensable au MVP. Sans elle, le produit n'a pas de sens. |
| **S** — Should | Importante, attendue en V1, contournable au MVP. |
| **C** — Could | Souhaitable si le coût est faible. |
| **W** — Won't (now) | Explicitement écartée pour l'instant, à reconsidérer plus tard. |

### Marqueurs

- **[HYP]** : hypothèse à confirmer.
- **[QO-nn]** : renvoi à une question ouverte du fichier 12.
- **[POC-nn]** : renvoi à une preuve de concept du fichier 13.
- **S (MVP …)** ou **C (MVP …)** : exigence de priorité S ou C dont une partie, précisée entre parenthèses, est nécessaire au MVP (voir [04 §2](04-perimetre-fonctionnel.md#2-découpage-en-versions)).

## Historique

| Version | Date | Auteur | Modifications |
|---|---|---|---|
| 0.1 | 2026-11-07 | — | Création initiale |
| 0.2 | 2026-11-07 | — | Décisions QO-02 (Android d'abord) et QO-14 (porteur unique + agents, budget nul, open source) ; proposition QO-09 (Android 10) ; nouvelles questions QO-19 à QO-21 ; risques R-13, R-14 |
| 0.3 | 2026-11-07 | — | Décisions QO-09 (Android 13 minimum), QO-19 (open source, GPL-3.0-or-later), QO-21 (matériel : Android 14, émulateur, piano droit) ; proposition QO-22 (micro dans le MVP) ; risques R-15, R-16 ; vérité terrain sans piano numérique |
| 0.4 | 2026-11-07 | — | Décision QO-22 : captation micro dans le MVP (mode attente obligatoire, jeu continu si POC-01 concluant) ; critères de go/no-go ajustés |
| 0.5 | 2026-11-07 | — | Corrections issues de la rédaction du PRD : textes antérieurs à QO-22, références iOS restantes (personas Claire et Marc sur Android), périmètre des POC-01/02 adapté au matériel, EF-PRA-08 ajoutée au périmètre MVP, ENF-SEC-01 sans objet sans serveur, EF-CPT-07 en C |
| 0.6 | 2026-11-07 | `product-owner` (sur instruction du porteur) | Décisions du porteur du 2026-11-07 sur QO-10, QO-11, QO-12, QO-16, QO-20, QO-23 à QO-32 et QO-34 à QO-43 consignées (12 §0). Application des 45 modifications proposées par les relecteurs du PRD (MOD-01 à MOD-45) : score global multiplicatif, continuité rapportée à la longueur, règle « du premier coup » en mode attente, points d'orgue et ralentis non évalués, hésitation et arrêt mesurés sur l'instant attendu avec recalage, pause et reprise en jouant, tolérances au micro (2σ), accords arpégés, petites notes et ornements, sous-types d'altération, gravité des erreurs, corpus et non-régression (06) ; précisions sur EF-IMP-02, EF-BIB-05, EF-VIS-02, EF-VIS-03, EF-VIS-05, EF-LEC-03, EF-LEC-05, EF-LEC-07, EF-PRA-02, EF-PRA-03, EF-PRA-05, EF-PRA-08, EF-ECO-06 à 08, EF-ECO-10, EF-RES-01, EF-RES-03, EF-RES-06 (05) ; statistique de décision des performances, cibles du mode attente, accessibilité, lexique bilingue, sauvegarde Android (08) ; navigation du MVP, écrans E24 à E33, maquettes E12 à E14 du MVP, code des résultats, ton (10) ; critère des contenus, stratégie de licence, rapport de test, vérification des développeurs Android, identité affichée (11). Nouvelle exigence **EF-PRA-11** (boucle de pratique). Résolution des incohérences restantes : exigences M qui dépendaient d'exigences S ou C (parts nécessaires au MVP précisées), maquettes E12 à E14, délai de fin de séance, mode « boucle », précision du rythme au micro. Nouvelle question QO-44 |
| 0.7 | 2026-11-08 | `product-owner` et `architecte` (sur instruction du porteur) | Décisions du porteur du 2026-11-08 consignées (12 §0 et §1) : QO-44 à QO-53 et QO-55 (recommandations validées en bloc ; QO-46 : `io.github.zebul1n.pianohelper`, compte GitHub Zebul1n ; QO-49 : dépôt séparé, CC0 ; QO-51 : option B) ; QO-45 à QO-55 ajoutées au tableau des questions, QO-54 ouverte ; liste consolidée des actions du porteur. Application des 19 modifications proposées par le DAT (MDAT-01 à MDAT-19) : Verovio LGPL-3.0-only, messages MIDI et `MidiManager`, différé sans enregistrement, candidats de transcription (Basic Pitch 70,9 %, *Onsets and Velocities*, madmom exclu, vérificateur maison non causal), banque Upright Piano KW *small*, compte de distribution limitée à 20 appareils et compte complet à 25 USD (07, 11) ; ENF-PERF-01 mesurée depuis l'attaque de la note, cible propre au grave (QO-55), ENF-MAINT-02 sans configuration distante au MVP, ENF-COMP-06 reportée après le MVP (08) ; EF-VIS-03 CA3 en mode page, EF-ANA-13 en mode attente (QO-52, nouveau CA2), EF-ECO-06 test de cohérence des horodatages (05) ; calibrage par modèle de téléphone (09) ; critères de POC-01 (tentatives fausses, Wilson, par octave) et objectif de POC-02 (13) ; non-régression en effectifs, formule de tolérance au micro par registre, accord non synchronisé entre mains, seuil d'octave dès sol 2, règles du corpus (gamme chromatique, tentatives fausses, vérité semi-automatique, 48 kHz, dépôt séparé) (06). Décisions QO-45 à QO-53 reportées dans 07 §7.3 et §14.2, 08 ENF-RES-03, 11 §3 et §4 |
