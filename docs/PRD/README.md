# Piano Helper — Product Requirements Document (PRD)

| Élément | Valeur |
|---|---|
| Projet | Piano Helper (nom de travail) |
| Document | PRD — Product Requirements Document |
| Version | 0.4 — décisions du porteur du 2026-11-08 et modifications du DAT intégrées |
| Date | 2026-11-08 |
| Auteur | `product-owner` |
| Statut | Décisions du porteur prises le 2026-11-07 et le 2026-11-08 ([09](09-decisions-a-prendre.md), [DAT 14](../DAT/14-decisions-a-prendre.md)) ; question ouverte : QO-54 (avant la V1) ; actions restantes au porteur ([09 §2.1](09-decisions-a-prendre.md)) |
| Référence | [EDB v0.7](../EDB/README.md), [DAT v0.3](../DAT/README.md) |

## Objet

Le PRD traduit l'[expression de besoin](../EDB/README.md) en **epics et user stories** priorisées, testables et tracées, et propose un **ordre de réalisation** du MVP. Il décrit le **quoi** du point de vue du produit ; le **comment** relève du DAT (`architecte`).

Cadre (décisions de l'EDB, non remises en cause) : Android d'abord, minimum Android 13 ; open source GPL-3.0-or-later ; budget nul ; porteur unique assisté d'agents ; pas d'échéance ; matériel de test : un téléphone Android 14, l'émulateur, un piano droit acoustique ; **captation micro dans le MVP** (mode attente obligatoire, jeu continu si POC-01 concluant), MIDI dans le MVP testé par entrées simulées.

## Sommaire

| # | Fichier | Contenu |
|---|---|---|
| 01 | [Vision et objectifs produit](01-vision-et-objectifs-produit.md) | Vision, proposition de valeur, principes, objectifs, métriques, non-objectifs |
| 02 | [Epics](02-epics.md) | 25 epics, modules et exigences couverts, version cible |
| 03 | [User stories du MVP](03-user-stories-mvp.md) | 86 stories détaillées (85 fermes, 1 conditionnelle), critères étiquetés par méthode de vérification, écrans |
| 04 | [User stories V1 et V2](04-user-stories-v1-v2.md) | 71 stories résumées |
| 05 | [Plan de release](05-plan-de-release.md) | Incréments I0 à I6, branches de J1, critères de J2, registre des vérifications reportées |
| 06 | [Matrice de traçabilité](06-matrice-de-tracabilite.md) | Chaque EF et ENF → stories, générée depuis les stories (réciprocité contrôlée) |
| 07 | [Définition de prêt et de fini](07-definition-of-ready-done.md) | Story, incrément, MVP ; gravité des anomalies ; règles de travail avec les agents |
| 08 | [Questions ouvertes : suivi et arbitrages](08-questions-ouvertes.md) | Suivi de QO-23 à QO-55 ; 20 arbitrages du PO entre relecteurs, un à faire (MDAT-34) |
| 09 | [Décisions du porteur](09-decisions-a-prendre.md) | **23 décisions prises le 2026-11-07** et **11 le 2026-11-08** (QO-44 ; QO-45 à QO-53 et QO-55, détaillées dans le [DAT 14](../DAT/14-decisions-a-prendre.md)), récapitulatifs, actions restantes au porteur, protocole du test d'affichage à un seul lecteur |
| 10 | [Modifications de l'EDB](10-modifications-edb-proposees.md) | 45 modifications proposées par les relecteurs, toutes appliquées dans l'EDB v0.6 |
| — | [Revues](revues/) | Relectures de `juriste-conformite`, `pedagogue-piano`, `designer-ux`, `qa-testeur`, avec leur « Suite donnée » |

## Chiffres clés

| Version | Epics | Stories |
|---|:-:|:-:|
| MVP | 12 (EP-01 à EP-12) | 86 : 85 fermes, 1 conditionnelle (US-ECO-08, POC-01), 0 proposée |
| V1 | 8 (EP-13 à EP-20) | 45 |
| V2 | 5 (EP-21 à EP-25) | 26 |

- Toutes les exigences **M** de l'EDB v0.7 ont au moins une story ; 4 sont couvertes partiellement au MVP, avec une justification ([06 §4](06-matrice-de-tracabilite.md)). L'EDB v0.7 compte 108 EF et 54 ENF (aucune exigence ajoutée ni retirée depuis la v0.6).
- Relectures : 161 remarques et 52 cas limites ; 161 remarques intégrées (dont 3 en partie), 0 rejetée, 0 en attente ; 52 cas limites intégrés.
- Décisions du porteur : 23 prises le 2026-11-07 (recommandations validées en bloc ; QO-34 : option C ; QO-31 : saisie par un agent ; QO-27 : test fait seul, décision provisoire reconfirmée en bêta). Une question nouvelle : QO-44.
- Décisions du 2026-11-08 : recommandations validées en bloc pour QO-44 (option A), QO-45 à QO-48, QO-50, QO-52, QO-53 et QO-55 ; QO-46 : `io.github.zebul1n.pianohelper` ; QO-49 : option A, CC0 ; QO-51 : option B. Question encore ouverte : QO-54.
- Modifications du DAT appliquées aux stories : MDAT-20 à MDAT-42 ([DAT 15](../DAT/15-modifications-edb-prd-proposees.md)) ; MDAT-34 en partie (choix de `application/octet-stream` à faire par le PO avec `designer-ux`).
- Actions qui restent au porteur : ~~fournir le nom et l’e-mail~~ ✅ fournis (Ronan Launay, rlaunay@free.fr) (avant I0) ; donner le nom du compte GitHub ✅ fourni (Zebul1n) (avant I0) ; relever les libellés Android de l'autorisation du micro (avant I3) ; faire seul le test d'affichage (avant I1) ; sauvegarder hors ligne la clé de signature dès sa création ; installer WSL 2 (Ubuntu 24.04) et Android Studio selon le [DAT 10 §7](../DAT/10-build-ci-et-distribution.md#7-prérequis-du-poste-du-porteur) (avant I0 et les POC).

## Conventions

### Identifiants

| Objet | Format | Exemple |
|---|---|---|
| Epic | `EP-nn` | EP-06 |
| Code d'epic | trois lettres, unique par epic | ECO |
| User story | `US-<CODE>-nn`, où `<CODE>` est le code de l'epic | US-ECO-07 |
| Incrément du MVP | `I0` à `I6` | I3 |
| Question ou décision | `QO-nn`, numérotation continue avec l'EDB | QO-34 |
| Arbitrage du PO | `A1` à `A20` ([08 §3](08-questions-ouvertes.md)) | A1 |
| Modification de l'EDB proposée | `MOD-nn` ([10](10-modifications-edb-proposees.md)) | MOD-01 |
| Écran ajouté par le PRD | `E24` à `E33` ([03 §1.4](03-user-stories-mvp.md)) | E28 |

Les identifiants de l'EDB (EF-xxx-nn, ENF-xxx-nn, RG-nn, RGPD-nn, R-nn, H-nn, POC-nn, P1 à P9, E01 à E23) et des revues (JUR-nn, PED-nn, UX-nn, QA-nn, CL-nn) sont repris tels quels.

### Priorités, statuts et vérification

- **Priorité** : MoSCoW de l'EDB (M, S, C, W). **V / E / R** : valeur, effort, risque (É, M, F).
- **Statut d'une story MVP** : Ferme, Conditionnelle (POC), Proposée (décision du porteur ; aucune en v0.3).
- Critères en Gherkin français ; chaque critère porte une étiquette de vérification ([AUTO], [BANC], [MESURE], [TÉL], [ÉMU], [PIANO], [REVUE], [PORTEUR], [BÊTA]).
- **[HYP]** : valeur hypothétique de l'EDB ; **[PO]** : valeur proposée par le Product Owner.

## Incohérences relevées dans l'EDB

**Aucune incohérence ouverte.** L'EDB v0.5 avait corrigé les points 1, 2, 7, 8, 9 et 11 de la liste de la v0.1, ainsi que l'absence d'EF-PRA-08 au périmètre du MVP. Les cinq points qui restaient en v0.2 sont résolus par l'**EDB v0.6**, qui applique les décisions du porteur du 2026-11-07 :

| # | Incohérence (v0.2) | Résolution dans l'EDB v0.6 |
|---|---|---|
| 1 | Exigences M qui dépendaient d'exigences S ou C (EF-RES-03 / EF-RES-06 ; EF-PRA-02 CA4 / EF-PRA-07 et EF-ECO-08 ; EF-CPT-02 / EF-ANA-08 et EF-RES-12 ; EF-LEC-07 / EF-ANA-10 ; EF-IMP-06 CA2 / EF-BIB-02 ; EF-CPT-05 / EF-VIS-10 ; langues et QO-16) | Décisions QO-23 et QO-24 a à g : la part nécessaire au MVP des exigences S est précisée (EF-RES-06, EF-ECO-08, EF-ANA-08, EF-ANA-10 : mention « S (MVP …) », 04 §2 et §3) ; l'étiquette à l'import (EF-IMP-06 CA2), le style de retour (EF-CPT-02) et le choix du thème (EF-CPT-05) passent en V1 ; français et anglais au MVP (QO-16 tranchée) |
| 2 | Maquettes E12, E13, E14 avec des éléments V1 | MOD-38 : maquettes du MVP et variantes de E14 (10 §4) ; E14 rattaché à EF-RES-03 à 06 au MVP (10 §3) |
| 3 | Délai de fin de séance égal au seuil d'arrêt | Décision QO-25, MOD-06 et MOD-18 : pause (et non fin) après 7 / 6 / 5 s, reprise en jouant, fin après 30 s de pause (EF-PRA-08, 06 §4.1) |
| 4 | Mode « boucle » de pratique sans définition | Décision QO-26 : nouvelle exigence **EF-PRA-11** (boucle de pratique), citée par EF-ANA-04, EF-PRA-05, 06 §4.2 et l'entité Séance |
| 5 | Précision du rythme au micro (tolérances « exigeant » de l'ordre de l'incertitude) | Décision QO-32 et MOD-11 : tolérance et fenêtre jamais sous 2σ, niveau exigeant au micro seulement si 2σ ≤ 50 ms, marque à 1,5 × la tolérance (06 §4.1) |

Un conflit né de l'application de QO-27 avait été noté comme question nouvelle : **QO-44** (tests qui demandent plusieurs personnes avant la bêta, [09 §2.3](09-decisions-a-prendre.md)), décidée le 2026-11-08 (option A). Les incohérences relevées par le DAT (INC-01 à INC-11, NI-1 à NI-3) sont résolues par l'EDB v0.7 et le PRD v0.4 ([DAT 15](../DAT/15-modifications-edb-prd-proposees.md)).

## Historique

| Version | Date | Auteur | Modifications |
|---|---|---|---|
| 0.1 | 2026-11-07 | `product-owner` | Création : vision et métriques, 25 epics, 84 stories MVP détaillées, 71 stories V1/V2 résumées, plan de release en 7 incréments, matrice, définitions de prêt et de fini, questions QO-23 à QO-33 |
| 0.2 | 2026-11-07 | `product-owner` | Intégration des quatre relectures croisées (161 remarques, 52 cas limites) et de l'EDB v0.5 : critères réécrits et étiquetés par méthode de vérification, incrément par critère, champ « Écran » et écrans E24 à E33 ; nouvelles stories US-QUA-05 (capture de séances) et US-CNF-06 (notice de confidentialité) ; US-CNF-02 avancée en I0 ; US-PRA-08 ferme ; annexes du pédagogue reprises ; textes du juriste et du designer ; matrice générée et réciproque ; registre des vérifications reportées et critères de J2 ; 20 arbitrages entre relecteurs ; décisions consolidées (QO-23 à QO-43) dans le fichier 09 ; modifications de l'EDB proposées dans le fichier 10 |
| 0.3 | 2026-11-07 | `product-owner` | Intégration des 23 décisions du porteur du 2026-11-07 (fichier 09 : statuts, récapitulatif, actions restantes, protocole du test d'affichage à un seul lecteur) ; 6 stories proposées devenues fermes (US-ECO-09, US-ECO-10, US-ECO-11, US-PRA-09, US-RES-08, US-CNF-05) ; positions provisoires remplacées par les décisions dans les stories ; identité Ronan Launay et rlaunay@free.fr (QO-34) ; saisie des démonstrations par un agent (QO-31) ; EDB v0.6 de référence, avec les 45 modifications appliquées et la nouvelle exigence EF-PRA-11 (citée par US-PRA-09), ENF-I18N-03 citée par US-CPT-04 ; matrice mise à jour (4 exigences M partielles au lieu de 10) ; registre des vérifications reportées complété (mode d'affichage, lisibilité, premier lancement) ; suites données des revues mises à jour (161 remarques intégrées) ; incohérences de l'EDB résolues ; question nouvelle QO-44 |
| 0.4 | 2026-11-08 | `product-owner` et `architecte` | Décisions du porteur du 2026-11-08 : QO-44 décidée (option A ; US-VIS-03 CA3, US-CPT-06 CA6, registre de 05 §5) ; QO-45 à QO-53 et QO-55 reportées dans 08 (suivi) et 09 (récapitulatif, questions closes), QO-54 ouverte ; actions restantes consolidées (nom du compte GitHub, installation de WSL 2 et d'Android Studio). Application des 23 modifications proposées par le DAT (MDAT-20 à MDAT-42) : US-CNF-04 (API 33 à 37 et image 16 Ko, reconstruction hors ligne, empreinte du certificat, environnement protégé, 20 appareils, identifiant `io.github.zebul1n.pianohelper`, 64 bits), US-QUA-01 (lots L0 et L2, 48 kHz, vérité semi-automatique, dépôt de corpus séparé en CC0), US-QUA-02 (non-régression en effectifs, reproductibilité à deux niveaux), US-QUA-04 (départ à l'attaque acoustique, total mesuré, par octave), US-ECO-07 (3 s de contexte, Wilson, seuil d'octave dès sol 2, cible propre au grave), US-ECO-08 et US-CPT-03 (formule de tolérance par registre), US-ANA-09 (mode attente, QO-52), US-VIS-07 (verrouillage sur téléphone seulement), US-PRA-06, US-ECO-03, US-CPT-05, US-IMP-03 (types MIME ; choix d'`application/octet-stream` à faire), US-ECO-11, US-CNF-01 (QO-50), US-CNF-02 (avis de Verovio, LGPL), US-CNF-03 (`reuse spdx`, AAR, polices QO-53), US-ECO-02 (MIDI 2.0, CA8 et registre), US-IMP-06, US-VIS-08, US-IMP-01 (valeurs [ARCH] figées), US-ECO-05 (test de cohérence), US-LEC-01 (banque *small*), définition de fini D3, D7, D8 ; arbitrage A14 précisé ; matrice : EDB v0.7, couvertures précisées (EF-VIS-03, EF-ECO-06, EF-ANA-13, ENF-PERF-01, ENF-RES-03, ENF-COMP-06, ENF-MAINT-02), aucun lien story ↔ exigence modifié ; US-ANX-06 alignée sur ENF-MAINT-02 |
