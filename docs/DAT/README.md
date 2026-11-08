# Piano Helper — Dossier d'architecture technique (DAT)

| Élément | Valeur |
|---|---|
| Projet | Piano Helper (nom de travail) |
| Document | Dossier d'architecture technique et décisions d'architecture (ADR) |
| Version | 0.3 — décisions du porteur du 2026-11-08 intégrées |
| Date | 2026-11-08 |
| Auteur | `architecte` |
| Statut | **À relire** : 116 remarques des cinq relecteurs intégrées ([revues](revues/)) ; **décisions du porteur prises le 2026-11-08** : 11 sur 12, QO-54 reste ouverte ([fichier 14](14-decisions-a-prendre.md)) ; **42 modifications de l'EDB et du PRD appliquées** ([fichier 15](15-modifications-edb-prd-proposees.md)) ; actions restantes au porteur ([14 §2](14-decisions-a-prendre.md#2-actions-qui-restent-au-porteur)) |
| Références | [EDB v0.7](../EDB/README.md), [PRD v0.4](../PRD/README.md), [revues du PRD](../PRD/revues/) |

## Objet

Le DAT décrit **comment** Piano Helper est construit : découpage, technologies, flux temps réel, données, tests, sécurité, construction et diffusion. Il répond aux exigences de l'EDB et aux stories du PRD, et prépare les preuves de concept POC-01 à POC-05 du jalon J1.

Ce qui dépend d'un POC est une **proposition** (« Proposée — à confirmer par POC-nn »). Ce qui revient au porteur est une **question** ([fichier 14](14-decisions-a-prendre.md)) ; le porteur les a tranchées le 2026-11-08, sauf QO-54.

## Sommaire

| # | Fichier | Contenu |
|---|---|---|
| 01 | [Contexte et contraintes](01-contexte-et-contraintes.md) | Cadre, contraintes, exigences architecturalement significatives |
| 02 | [Vue d'ensemble](02-vue-d-ensemble.md) | Contexte, conteneurs et modules, deux bibliothèques natives, outils, déploiement |
| 03 | [Noyau natif](03-noyau-natif.md) | Composants C++, interfaces et fil appelant, vérificateur temps réel, règles temps réel, modèle de threads |
| 04 | [Application Android](04-application-android.md) | Modules, état, changements de configuration, permissions, cycle de vie et interruptions, pont JNI, variantes |
| 05 | [Flux temps réel et performance](05-flux-temps-reel-et-performance.md) | Chaîne, budgets de latence depuis l'attaque, délai par registre, mesure, dégradation |
| 06 | [Données et stockage](06-donnees-et-stockage.md) | Modèle physique, fichiers de transit, durabilité, migrations, suppression, sauvegarde désactivée |
| 07 | [Import et rendu de partition](07-import-et-rendu-de-partition.md) | MusicXML, MIDI, modèle, identifiants, contexte de dessin, polices, bande, surimpression |
| 08 | [Stratégie de test et banc d'évaluation](08-strategie-de-test-et-banc-d-evaluation.md) | Niveaux de test, banc, non-régression en effectifs, reproductibilité à deux niveaux |
| 09 | [Sécurité, confidentialité, licences](09-securite-confidentialite-licences.md) | Permissions, télémétrie, fichiers non fiables, inventaire avec mentions à reproduire, REUSE |
| 10 | [Construction, CI et distribution](10-build-ci-et-distribution.md) | Dépôt, chaîne, signature, CI, contrôles de l'APK, diffusion, prérequis du poste |
| 11 | [Portabilité iOS](11-portabilite-ios.md) | Ce qui est conçu pour le portage ; obstacles |
| 12 | [Spécifications techniques des POC](12-specifications-techniques-des-poc.md) | POC-01 à POC-05 : protocole, mesures, règles de décision |
| 13 | [Risques techniques et questions](13-risques-techniques-et-questions.md) | Risques RT-01 à RT-16, incohérences INC-01 à INC-11 et NI-1 à NI-3 |
| 14 | [Décisions du porteur](14-decisions-a-prendre.md) | **Liste unique des décisions du porteur** : QO-44 à QO-55, décidées le 2026-11-08 sauf QO-54 ; récapitulatif ; actions qui restent au porteur |
| 15 | [Modifications de l'EDB et du PRD](15-modifications-edb-prd-proposees.md) | MDAT-01 à MDAT-42, toutes appliquées (EDB v0.7, PRD v0.4) |
| — | [adr/](adr/) | Décisions d'architecture |
| — | [revues/](revues/) | Relectures de la v0.1 et suite donnée |

## Décisions d'architecture (ADR)

| ADR | Titre | Statut | Évolution en v0.2 | Évolution en v0.3 |
|---|---|---|---|---|
| [0001](adr/0001-decoupage-application-noyau-outils.md) | Découpage : application, noyau C++ portable, outils | Acceptée | — | QO-48 décidée |
| [0002](adr/0002-langages-et-normes.md) | Langages : Kotlin et C++20 | Acceptée | Clang seul, Python dans WSL 2 | QO-48 décidée (WSL 2) |
| [0003](adr/0003-pont-jni-et-echanges.md) | Pont JNI et format des échanges | Acceptée | Façade C dans `core/capi`, durée de vie, UTF-8 | — |
| [0004](adr/0004-moteur-audio-oboe.md) | Entrée et sortie audio : Oboe | Proposée — à confirmer par POC-02 | Déconnexions, préréglage demandé, `FullDuplexStream` | — |
| [0005](adr/0005-midi-midimanager-amidi.md) | MIDI : MidiManager, AMidi, appareil virtuel | Acceptée | API non dépréciées, analyseur MIDI, sondage en séance seulement | — |
| [0006](adr/0006-moteur-de-rendu-verovio.md) | Gravure : Verovio dans le processus d'import | Proposée — à confirmer par POC-04 | Processus d'import seulement, Bravura obligatoire, polices non modifiées | QO-53 décidée (demande à RISM seulement si la taille l'exige) |
| [0007](adr/0007-affichage-liste-d-affichage-compose.md) | Affichage : contexte de dessin du projet, dessin Compose | Proposée — à confirmer par POC-04 | Option D (`RenderToDeviceContext`) proposée ; SVG comme oracle | — |
| [0008](adr/0008-modele-de-partition-et-format-pivot.md) | Modèle, pivot MEI, identifiants structurels | Proposée — à confirmer par POC-04 | — | — |
| [0009](adr/0009-synthese-tinysoundfont.md) | Synthèse : TinySoundFont | Proposée — à confirmer par POC-02 | Voix, allocations, appels dans le seul rappel | — |
| [0010](adr/0010-transcription-deux-etages.md) | Transcription en deux étages | Proposée — à confirmer par POC-01 | Vérificateur temps réel spécifié ; différé : vérificateur maison, Basic Pitch, *Onsets and Velocities* en concurrence | QO-52 décidée (option A) : en mode attente, le différé ne crée jamais d'erreur |
| [0011](adr/0011-moteur-d-inference-onnx-runtime.md) | Moteur d'inférence (si un modèle est retenu) | Proposée — à confirmer par POC-01 (QO-50 décidée) | AAR officiel exclu des versions diffusées (télémétrie, taille) ; inférence écrite à la main privilégiée | QO-50 décidée : AAR officiel exclu des versions diffusées ; choix entre C, B et aucun moteur à J1 |
| [0012](adr/0012-alignement-et-classification-maison.md) | Alignement et classification maison | Proposée — à confirmer par POC-05 | Entiers et virgule fixe | — |
| [0013](adr/0013-son-en-memoire-uniquement.md) | Le son reste en mémoire vive | Acceptée | Formulation corrigée (mémoire placée sur stockage par le système) | — |
| [0014](adr/0014-persistance-room-et-fichiers.md) | Persistance : Room et fichiers | Acceptée | `synchronous` par connexion, suppression totale | — |
| [0015](adr/0015-banc-d-evaluation.md) | Banc d'évaluation C++ | Acceptée | Règle en effectifs, environnement figé | QO-48 décidée |
| [0016](adr/0016-construction-et-dependances.md) | Construction : Gradle, CMake 4.1.2, NDK r30 | Acceptée | CMake 4.1.2, `c++_shared`, `ccache`, construction reproductible, detekt reporté | QO-50 décidée |
| [0017](adr/0017-integration-continue-et-diffusion.md) | Intégration continue et diffusion | Acceptée | `ubuntu-24.04`, environnement `release`, diffusions immuables | **Acceptée** : QO-45 décidée (GitHub) ; identifiant lié au compte (QO-46) |
| [0018](adr/0018-conformite-des-licences.md) | Conformité des licences | Acceptée | Mentions de Verovio, `reuse spdx` obligatoire, angle mort de Licensee | QO-48 décidée |
| [0019](adr/0019-traitement-du-signal.md) | Traitement du signal | Proposée — à confirmer par POC-01 | Goertzel, décimateur en table, tables précalculées | — |
| [0020](adr/0020-modele-de-threads-temps-reel.md) | Threads et temps réel | Acceptée | Une file par producteur, RealtimeSanitizer, APerformanceHint | — |
| [0021](adr/0021-import-en-processus-isole.md) | Import dans un processus séparé | Proposée — à confirmer par POC-04 | Processus privé (pas un bac à sable) ; processus isolé étudié | — |
| [0022](adr/0022-reproductibilite-ordinateur-telephone.md) | Reproductibilité ordinateur / téléphone | Acceptée | **Nouvelle** | — |
| [0023](adr/0023-deux-bibliotheques-natives.md) | Deux bibliothèques natives | Proposée — à confirmer par POC-04 | **Nouvelle** | — |
| [0024](adr/0024-mesure-des-latences.md) | Mesure des latences : départ et référence unique | Proposée — à confirmer par POC-02 | **Nouvelle** | — |

Bilan : **12 acceptées**, **12 proposées à confirmer par un POC**, **aucune en attente d'une décision du porteur** (ADR-0017 acceptée en v0.3 après QO-45). Statuts : **Acceptée** (ne dépend d'aucun POC) ; **Proposée — à confirmer par POC-nn** ; **Proposée — en attente de QO-nn** ; **Remplacée par NNNN**.

## Arbitrages entre relecteurs (v0.2)

| ID | Sujet | Positions | Arbitrage | Où |
|---|---|---|---|---|
| ARB-01 | ONNX Runtime | JUR : télémétrie, mentions et sources (bloquant) ; CPP et DEV : 34 Mo par ABI, inférence écrite à la main ; MIR : le modèle lui-même est faible sur le piano | AAR officiel réservé au prototype et à la variante interne ; versions diffusées : inférence écrite à la main, sinon construction propre, ou aucun moteur si le vérificateur maison suffit ; exclusion de l'AAR officiel décidée par le porteur le 2026-11-08 (QO-50) | [ADR-0010](adr/0010-transcription-deux-etages.md), [ADR-0011](adr/0011-moteur-d-inference-onnx-runtime.md) |
| ARB-02 | Sortie du rendu | v0.1 : convertir le SVG ; CPP : contexte de dessin du projet | Option D proposée, A en repli, choix sur mesures de POC-04 ; SVG oracle des tests | [ADR-0007](adr/0007-affichage-liste-d-affichage-compose.md) |
| ARB-03 | Reproductibilité | MIR : deux niveaux et tables ; CPP : Clang seul, options strictes, entiers ; DEV : `ubuntu-24.04` et clang-18 figés, WSL 2 | Les trois propositions sont cumulées ; la souhaitée « même version majeure que le Clang du NDK » (CPP) n'est pas retenue | [ADR-0022](adr/0022-reproductibilite-ordinateur-telephone.md) |
| ARB-04 | Point de départ des latences et double comptage | MIR (MIR-05, MIR-06) | Départ à l'attaque acoustique (INC-11) ; une seule référence temporelle, vérifiée par le test de cohérence de POC-02 | [ADR-0024](adr/0024-mesure-des-latences.md) |
| ARB-05 | Police Bravura | JUR : ne pas l'embarquer ; CPP : Verovio la charge toujours | Embarquée sans modification, avec sa licence | [ADR-0006](adr/0006-moteur-de-rendu-verovio.md) |
| ARB-06 | Version de Clang | CPP : RealtimeSanitizer (Clang ≥ 20) ; DEV : clang-18 figé | clang-18 pour le banc et les références ; tâche séparée avec Clang ≥ 20 pour RealtimeSanitizer | [03 §4.1](03-noyau-natif.md#41-outils-de-contrôle) |
| ARB-07 | Liaison de Verovio | CPP : option `libverovio.so` à part ; JUR : liaison statique conforme | Liaison statique dans `libpianoimport.so` | [ADR-0023](adr/0023-deux-bibliotheques-natives.md) |
| ARB-08 | Processus d'import | v0.1 : « isolé » ; AND : processus privé, pas un bac à sable ; CPP : inexistant sur iOS | Processus privé au MVP, limite écrite ; processus isolé étudié en POC-04 ; bornes internes du noyau | [ADR-0021](adr/0021-import-en-processus-isole.md) |
| ARB-09 | Outils du poste (QO-48) | v0.1 : Python et compilateur au choix ; DEV : WSL 2 Ubuntu 24.04 | WSL 2, décidé par le porteur le 2026-11-08 (QO-48) | [14](14-decisions-a-prendre.md) |
| ARB-10 | Tolérance de non-régression au micro | PRD : + 0,1 point ; MIR : règle en effectifs avec garde par sous-corpus | Règle en effectifs, appliquée dans l'EDB v0.7 et le PRD v0.4 (MDAT-13, MDAT-22) | [08 §3](08-strategie-de-test-et-banc-d-evaluation.md#3-règle-de-non-régression), MDAT-13 |
| ARB-11 | Rangement du corpus audio (QO-49) | v0.1 : dépôt séparé ; DEV : fichiers audio en pièces jointes des versions de ce dépôt | Variante de DEV retenue par le porteur le 2026-11-08, licence CC0 (QO-49) | [14](14-decisions-a-prendre.md) |

## Choix principaux en bref

| Domaine | Choix | Licence vérifiée |
|---|---|---|
| Interface | Kotlin 2.4, Jetpack Compose (BOM 2026.09.00) | Apache-2.0 |
| Noyau | C++20, CMake 4.1.2, NDK r30, Clang | — |
| Audio | Oboe 1.11.0 | Apache-2.0 |
| MIDI | `MidiManager` + AMidi | API système |
| Gravure | Verovio 6.4.1 (processus d'import), polices Leipzig et Bravura | LGPL-3.0-only ; OFL-1.1 |
| Synthèse | TinySoundFont, Upright Piano KW (small) | MIT ; CC0-1.0 |
| Transcription | Vérificateur maison ; différé selon POC-01 (vérificateur non causal, Basic Pitch, *Onsets and Velocities*) | Projet ; Apache-2.0 ; code GPL-3.0, poids à vérifier |
| Inférence | Aucune, ou inférence écrite à la main ; ONNX Runtime 1.31.1 seulement en prototype | — ; MIT |
| XML, ZIP, JSON | pugixml 1.16, miniz 3.1.2, nlohmann/json 3.12.0 | MIT |
| Signal | pocketfft, filtres de Goertzel, décimateur en table, dr_libs | BSD-3-Clause ; projet ; Unlicense OR MIT-0 |
| Persistance | Room 2.8.5 (SQLite) | Apache-2.0 ; domaine public |
| Tests | GoogleTest 1.18.0, RealtimeSanitizer, banc maison | BSD-3-Clause ; Apache-2.0 (LLVM) |
| Conformité | REUSE 3.3 (`reuse` 6.3.0), AboutLibraries 15.3.0, Licensee 1.14.1 | GPL-3.0-or-later (outil) ; Apache-2.0 |
| CI | GitHub Actions `ubuntu-24.04` (QO-45) | Gratuit pour les dépôts publics |
| Identifiant | `io.github.zebul1n.pianohelper` (QO-46 ; compte GitHub Zebul1n) ; APK diffusés en `arm64-v8a` (QO-47) | — |

Sources et dates : [09 §5](09-securite-confidentialite-licences.md#5-inventaire-des-composants-et-compatibilité).

## Conventions

- Identifiants repris de l'EDB et du PRD (EF-, ENF-, RG-, US-, QO-, POC-, R-). Propres au DAT : **ADR-NNNN**, **RT-nn** (risques), **INC-nn** et **NI-n** (incohérences), **ARB-nn** (arbitrages), **MDAT-nn** (modifications proposées), **QO-45** et suivantes (questions au porteur, [fichier 14](14-decisions-a-prendre.md)). **Zebul1n** : compte GitHub du porteur (QO-46, fourni le 2026-11-08). Identifiants des revues : **MIR-nn**, **DA-nn**, **DN-nn**, **JUR-Dnn**, **DEV-nn**.
- **[VÉRIFIÉ date]** : lu à la source officielle par l'architecte ; **[VÉRIFIÉ par `agent` date]** : vérifié par un relecteur ; **[NON VÉRIFIÉ]** : à vérifier ; **[ARCH]** : valeur proposée, à figer à l'entrée de la story.
- Latences au 95e centile du total mesuré, durées en médiane et maximum ([EDB 08 §1](../EDB/08-exigences-non-fonctionnelles.md#1-performance-perf)).
- Licence de la documentation : CC BY-SA 4.0 (QO-38).

## Historique

| Version | Date | Auteur | Modifications |
|---|---|---|---|
| 0.1 | 2026-11-07 | `architecte` | Création : 13 fichiers et 21 ADR ; licences vérifiées en ligne ; POC-01 à POC-05 ; questions QO-45 à QO-50 ; incohérences INC-01 à INC-10 |
| 0.2 | 2026-11-08 | `architecte` | Intégration des cinq relectures (116 remarques : `expert-audio-mir` 25, `dev-android` 29, `dev-natif-cpp` 31, `juriste-conformite` 13, `devops-release` 18) ; suite donnée à la fin de chaque revue ; 11 arbitrages ; 3 ADR nouvelles (0022 à 0024) et 20 révisées ; vérificateur temps réel spécifié ; protocole de POC-01 avec tentatives fausses et règle de Wilson ; Android 16 et 17 (orientation, multifenêtre, retrait d'autorisation, bord à bord) ; ONNX Runtime officiel exclu des versions diffusées ; deux bibliothèques natives ; contrôles de l'APK (16 Ko, symboles, taille) ; fichier 14 (12 décisions du porteur : QO-44 à QO-55) ; fichier 15 (42 modifications de l'EDB et du PRD proposées) ; INC-11, NI-1 à NI-3 |
| 0.3 | 2026-11-08 | `architecte` et `product-owner` | Décisions du porteur du 2026-11-08 intégrées (fichier 14 : statuts, récapitulatif, actions restantes) : recommandations validées en bloc pour QO-44, QO-45 à QO-48, QO-50, QO-52, QO-53, QO-55 ; QO-46 au format `io.github.zebul1n.pianohelper`, compte GitHub Zebul1n ; QO-49 : option A, CC0 ; QO-51 : option B ; QO-54 reste ouverte. ADR-0017 acceptée ; ADR-0010, ADR-0011 et les ADR qui citaient QO-48, QO-50 et QO-53 mises à jour ; remarques MIR-16, JUR-D01 et JUR-D11 intégrées (plus aucune remarque en attente) ; fichier 15 : 42 modifications appliquées dans l'EDB v0.7 et le PRD v0.4, avec l'endroit concerné ; identifiant de l'application au 10 §3 ; valeurs [ARCH] figées par le PRD (P10, P30, limites d'import, émulateur à 2 Go) |
