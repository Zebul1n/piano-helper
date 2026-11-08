# 13 — Risques techniques, questions et incohérences

## 1. Risques techniques

Complètent les risques R-01 à R-16 de l'[EDB 12 §3](../EDB/12-risques-hypotheses-questions.md#3-risques). P (probabilité) et I (impact) de 1 à 3.

| ID | Risque | P | I | Crit. | Mitigation | Levée par |
|---|---|:-:|:-:|:-:|---|---|
| RT-01 | Au micro, la chaîne du mode attente ne tient pas 150 ms, surtout dans le grave | 2 | 3 | **6** | Vérificateur informé par la partition, test séquentiel, partielles élevées dans le grave ; mesure depuis l'attaque acoustique sans double comptage ([ADR-0024](adr/0024-mesure-des-latences.md)) ; cible propre au grave si la mesure l'exige (décision QO-55) | POC-01, POC-02 |
| RT-02 | Aucun étage différé n'atteint la qualité voulue (Basic Pitch : 70,9 % de F-mesure par note sur MAESTRO) | **3** | 3 | **9** | Vérificateur maison non causal en premier rang ; *Onsets and Velocities* en concurrent ; Kong et al. comme référence (MIR-02) | POC-01 |
| RT-03 | Verovio : fidélité de l'import insuffisante, ou mémoire et temps excessifs pour P30 en un système | 2 | 2 | 4 | Processus d'import, rapport d'import, repli alphaTab | POC-04 |
| RT-04 | Identité au bit près impossible sur une partie du code | 2 | 1 | 2 | Conditions de l'[ADR-0022](adr/0022-reproductibilite-ordinateur-telephone.md), test d'empreintes dès I0 | I0 |
| RT-05 | ONNX Runtime officiel : **télémétrie** (bloquant en version diffusée), 34 Mo par ABI, mentions absentes, binaire non construit par le projet. L'alignement 16 Ko est **conforme** (levé) | 3 | 2 | **6** | Exclu des versions diffusées ; inférence écrite à la main ou construction propre (décision QO-50 du 2026-11-08) | POC-01 |
| RT-06 | Latence Bluetooth et horodatage de sortie inexact | 3 | 1 | 3 | Avertissement ; métronome sonore désactivé en jeu continu ; compensation en V1 | POC-02 |
| RT-07 | Bugs temps réel dans du code produit par des agents | 2 | 3 | **6** | RealtimeSanitizer, ThreadSanitizer, files par producteur, revue | Continu |
| RT-08 | Arrêt du processus pendant une séance (mémoire, retrait d'autorisation) | 2 | 2 | 4 | Points de reprise en portée d'application, sur `ON_STOP` et toutes les deux mesures | Tests US-HIS-01 |
| RT-09 | Audio propre au constructeur (`Unprocessed` absent, flux exclusif refusé, contrôle de gain non observable) | 2 | 2 | 4 | Phase 0 de POC-01 ; détection empirique du contrôle de gain | POC-02 |
| RT-10 | Comportements d'Android 16 et 17 (orientation ignorée sur tablette, bord à bord, retour prédictif, audio en arrière-plan) ; vérification des développeurs en 2027 | 2 | 2 | 4 | [04](04-application-android.md) révisé ; tests de nuit avec drapeaux de compatibilité ; nom technique et clé en I0 | Continu |
| RT-11 | Taille de l'APK au-delà de 150 Mo | 2 | 2 | 4 | Une ABI diffusée, Verovio réduit, pas d'AAR ONNX Runtime diffusé, seuil en CI | POC-04, POC-01 |
| RT-12 | Corpus mal licencié ou contenant des voix | 1 | 3 | 3 | REUSE, revue JUR, enregistrement seul | US-QUA-01 |
| RT-13 | Trop d'outils pour un porteur seul | 2 | 2 | 4 | Python et outils hôte regroupés dans WSL 2 ; detekt reporté ; aucune technologie sans ADR | Continu |
| RT-14 | `RenderToDeviceContext` de Verovio est public mais non documenté : changement d'interface à une mise à jour | 2 | 1 | 2 | Rupture visible à la compilation ; test de non-régression du contexte de dessin ; SVG comme oracle et comme repli | POC-04 |
| RT-15 | Validations indues au micro (octave inférieure, résonance) : l'élève apprend une erreur | 2 | 3 | **6** | Règle d'octave sur les partielles impaires, preuve par augmentation d'énergie, sous-corpus de 400 tentatives fausses, borne de Wilson (MIR-03, MIR-07) | POC-01 |
| RT-16 | Fichier piégé exploitant un défaut natif : le processus d'import n'est pas un bac à sable (même UID) | 1 | 2 | 2 | Bornes internes, fuzzing ; processus isolé étudié en POC-04 | POC-04 |

## 2. Questions nouvelles pour le porteur

Depuis la v0.2, **toutes les décisions qui reviennent au porteur sont dans le [fichier 14](14-decisions-a-prendre.md)** : QO-44 (PRD), QO-45 à QO-50 (mises à jour avec l'avis des relecteurs) et QO-51 à QO-55 (nouvelles), classées par urgence. Le porteur les a tranchées le 2026-11-08, sauf **QO-54**, qui reste ouverte (DAT v0.3).

## 3. Incohérences relevées dans l'EDB et le PRD

Les corrections sont détaillées au [fichier 15](15-modifications-edb-prd-proposees.md) ; elles sont **toutes appliquées** depuis le 2026-11-08 (EDB v0.7, PRD v0.4).

| ID | Où | Constat (résumé) |
|---|---|---|
| INC-01 | US-CNF-04 CA5 | Tests en API 33 à 35 seulement ; Android 16 et 17 existent, Google Play exige `targetSdk` ≥ 36 |
| INC-02 | EDB 07 §3, EDB 11 §3 | Verovio est sous LGPL-3.0-only, pas « LGPL-3.0 » |
| INC-03 | EDB 07 §13 | L'ouverture d'un appareil MIDI passe par `MidiManager` en Kotlin |
| INC-04 | US-QUA-02 CA5 | « Écart » non défini ; définition à deux niveaux ([ADR-0022](adr/0022-reproductibilite-ordinateur-telephone.md)) |
| INC-05 | EDB 07 §7.3 | « Enregistrement de la séance » contredit la promesse sur le son |
| INC-06 | EDB 13, POC-02 | Latence MIDI réelle non mesurable sans piano numérique |
| INC-07 | ENF-MAINT-02 | Configuration distante impossible sans serveur ni INTERNET |
| INC-08 | EF-VIS-03 CA3 | Recalcul de la mise en page sans objet en bande |
| INC-09 | EDB 09, US-ECO-05 CA5 | Calibrage rattaché au profil seul |
| INC-10 | EDB 07 §4, 11 §3, QO-10 | « Upright Piano KW, 5,8 Mo » = variante small |
| **INC-11** | ENF-PERF-01, EDB 06 §8 | « De l'appui sur la touche » : la latence au micro doit partir de l'attaque acoustique (MIR-05) |
| **NI-1** | US-VIS-07 CA3 | Orientation non verrouillable sur tablette avec `targetSdk` 36 (DA-01) |
| **NI-2** | ENF-COMP-06 et US-PRA-06 CA1 | Multifenêtre : captation sans perte (EDB) contre suspension (PRD) (DA-03) |
| **NI-3** | US-ECO-03 CA6 | Le retrait de l'autorisation termine le processus (DA-05) |

## 4. Exigences difficiles à tenir

| Exigence | Difficulté | Position |
|---|---|---|
| ENF-PERF-01 micro (≤ 150 ms) | Marge faible, grave plus lent | Réalisable en médium si la mesure part de l'attaque acoustique ; le grave dépend de POC-01 ; cible propre au grave si la mesure l'exige (décision QO-55) |
| ENF-QA-01, validations indues ≤ 1 % au micro | Vérifiable seulement avec ≥ 400 tentatives fausses et une borne de Wilson | Sous-corpus L2 ajouté à POC-01 |
| US-QUA-02 CA5 au sens strict | Égalité bit à bit | Réalisable pour le code du projet (niveau 1) ; tolérance bornée pour un moteur d'inférence tiers |
| ENF-MAINT-02 au MVP | INC-07 | Résolu par l'EDB v0.7 : au MVP, paramètres embarqués et versionnés (MDAT-08) |
| POC-02 sur le MIDI réel | INC-06 | Reporté en bêta |
| US-VIS-07 CA3 sur tablette | NI-1 | Résolu par le PRD v0.4 : orientation verrouillée sur téléphone seulement, la séance survit à la rotation sur tablette (MDAT-30) |
