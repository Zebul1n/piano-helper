# 05 — Plan de release du MVP

Ce plan découpe le MVP en **incréments livrables** (I0 à I6). Chaque incrément produit un APK installable et apporte quelque chose que le porteur peut **essayer lui-même** avec son matériel : un téléphone Android 14, l'émulateur Android, un piano droit acoustique (pas de piano numérique, pas de tablette).

Il suit la démarche de l'[EDB 13](../EDB/13-jalons-et-demarche.md) : POC, jalon J1 (go / no-go technique), MVP, jalon J2 (« MVP utilisable de bout en bout par un testeur sans assistance »). Pas d'échéance : les incréments sont ordonnés, pas datés.

## 1. Principes d'ordonnancement

1. **Lever les risques d'abord** : l'analyse (POC-05) et le micro (POC-01) portent les risques les plus forts (R-01, R-04) ; l'outillage qualité et les protocoles de mesure sont construits en premier.
2. **Valeur visible tôt** : voir et entendre sa partition (I1) ne dépend que du rendu (POC-04) ; I1 démarre dès que POC-04 est concluant (décision [QO-30](09-decisions-a-prendre.md)).
3. **MIDI simulé avant le micro** : le moteur est mis au point avec des entrées exactes (I2), puis branché sur le micro (I3).
4. **Incréments verticaux**, testables de bout en bout.
5. **Le banc garde la porte** : à partir de I2, aucun incrément n'est accepté s'il enfreint la règle de non-régression (US-QUA-02 CA3).
6. **Mentions légales dès la première diffusion** : chaque APK, dès I0, contient les licences et le lien vers ses sources (US-CNF-02, US-CNF-04).

## 2. Vue d'ensemble

```
 Phase 1 — POC                                Phase 2 — MVP
 ─────────────────────────────────────┐       ───────────────────────────────────────────────────────────►
 POC-05 alignement ─┐                 │
 POC-04 rendu ──────┼─► I0 outillage  │
 POC-01 micro ──────┤   (en parallèle)│
 POC-02 latence ────┘                 │
                                      ▼
                                   Jalon J1 ──► I1 ──► I2 ──► I3 ──► I4 ──► I5* ──► I6 ──► Jalon J2
                                   go/no-go     voir    moteur  micro   carnet  continu  stabi-   MVP
                                                et      (MIDI   en      de      au micro lisation utilisable
                                                entendre simulé) attente travail

 * I5 n'existe que si POC-01 est concluant en jeu continu.
 I1 démarre dès que POC-04 est concluant, avant J1 (décision QO-30).
```

### Branches de J1 (EDB 13 §3)

| Résultat de POC-01 | Suite du plan |
|---|---|
| Concluant en attente et en jeu continu | I0 à I6, avec I5 ; jeu continu au micro marqué « expérimental » (décision [QO-29](09-decisions-a-prendre.md)) |
| Concluant en attente seulement | I5 supprimé ; US-ECO-08 et la partie micro de US-PRA-04 passent en V1 (US-PRV-01) ; US-PRA-09 peut être livrée en MIDI seul |
| Non concluant même en attente | Décision [QO-42](09-decisions-a-prendre.md) : si les morceaux à une main atteignent les objectifs, micro limité à ces morceaux, « expérimental », avec un I3 réduit ; sinon, reprise de la reconnaissance avant de continuer le MVP. **I4 démarre après I2 sans attendre I3.** J2 et la définition de fini du MVP sont adaptés en conséquence |

## 3. Incréments

### I0 — Outillage et socle (pendant la phase POC)

| | |
|---|---|
| **Objectif** | Mesurer avant de construire : corpus, banc, MIDI simulé, protocoles de mesure, inventaire des licences, première version diffusable avec ses mentions légales. |
| **Stories** | US-QUA-01, US-QUA-02, US-QUA-03, US-QUA-04 (CA1 : protocoles), US-CNF-02, US-CNF-03, US-CNF-04 |
| **Prérequis** | Prototype de banc (POC-05) ; décisions [QO-34, QO-35, QO-36, QO-37](09-decisions-a-prendre.md) (prises) ; **Ronan Launay et rlaunay@free.fr fournis par le porteur** (QO-34) ; clé de signature créée et sauvegardée hors ligne (QO-35) |
| **Le porteur peut tester** | Sur l'ordinateur : lancer le banc et lire son rapport. Au piano : enregistrer avec le téléphone les premières prises du corpus micro, selon les fiches d'erreurs scénarisées. Sur le téléphone : installer l'APK, ouvrir « À propos » (licences, lien vers les sources), mettre à jour sans perte. |
| **Critère de fin** | Banc exécuté dans l'intégration continue ; corpus v0 versionné ; protocoles de mesure relus par `qa-testeur` ; inventaire relu par JUR et validé par le porteur. |

### I1 — Voir et entendre sa partition

| | |
|---|---|
| **Objectif** | Importer un MusicXML, le lire au pupitre et l'écouter : l'application est déjà utile comme pupitre et lecteur. |
| **Stories** | US-IMP-01, US-IMP-03, US-IMP-04, US-IMP-05, US-IMP-06, US-IMP-07, US-BIB-01, US-BIB-06, US-VIS-01 à US-VIS-08, US-LEC-01 à US-LEC-07, US-CNF-01 |
| **Prérequis** | POC-04 concluant (y compris le rendu sur un système de longueur illimitée) ; **test d'affichage au pupitre fait par le porteur seul** ([QO-27](09-decisions-a-prendre.md), protocole du fichier 09) ; décisions [QO-10, QO-30, QO-31, QO-38, QO-44](09-decisions-a-prendre.md) (prises) |
| **Le porteur peut tester** | Télécharger un MusicXML libre et l'importer depuis le navigateur ou un mail ; poser le téléphone sur le pupitre, en paysage, et lire à 60 cm ; écouter à 60 %, main droite seule, boucle sur 4 mesures, métronome visuel et sonore ; passer en mode avion. Profils tablette : sur l'émulateur. |
| **Ne peut pas tester** | Vraie tablette (bêta). |

### I2 — Le moteur juge juste (MIDI simulé)

| | |
|---|---|
| **Objectif** | Boucle de valeur complète (préparer, jouer, être corrigé, lire le compte-rendu) sur des entrées MIDI exactes. |
| **Stories** | US-ECO-01, US-ECO-02, US-ECO-06, US-ANA-01 à US-ANA-09 (partie MIDI), US-PRA-01, US-PRA-02, US-PRA-04, US-PRA-05, US-PRA-07, US-PRA-08 (partie MIDI), US-RES-01 à US-RES-06 |
| **Prérequis** | POC-05 concluant ; décisions [QO-23, QO-24, QO-25, QO-39, QO-40](09-decisions-a-prendre.md) (prises) |
| **Le porteur peut tester** | Avec le clavier visuel ou le rejeu de fichiers MIDI : mode attente sur une démonstration, jeu continu avec erreurs injectées (chaque erreur injectée apparaît au bon endroit, aucune n'est inventée) ; démarrer en jouant une note ; s'arrêter puis reprendre en jouant ; compte-rendu, lien « Voir sur la partition », partition annotée ; palette contrôlée avec la simulation des daltonismes d'Android. |
| **Ne peut pas tester** | Vrai piano numérique (bêta). |

### I3 — Le piano droit en mode attente (micro)

| | |
|---|---|
| **Objectif** | Le porteur pratique **sur son propre piano** en mode attente, avec un retour fiable. |
| **Stories** | US-ECO-03, US-ECO-04, US-ECO-05, US-ECO-07, US-ECO-09, US-ECO-10, US-ECO-11, US-ANA-02 et US-ANA-08 (partie micro), US-PRA-02 (partie micro), US-PRA-03, US-PRA-06, US-PRA-08 (partie micro), US-RES-07, US-QUA-04 (latences), US-QUA-05 |
| **Prérequis** | **J1** : POC-01 concluant au moins en mode attente, POC-02 concluant sur le téléphone ; décisions [QO-29, QO-32, QO-41](09-decisions-a-prendre.md) (prises, appliquées au vu de POC-01) ; libellés Android de l'autorisation du micro relevés par le porteur (US-ECO-03) |
| **Le porteur peut tester** | Téléphone sur le pupitre : autorisation du micro, réglage en moins d'une minute, mode attente sur les démonstrations à une main puis à deux mains, avec et sans pédale ; 20 séances capturées avec l'enregistreur de test (US-QUA-05) et annotées à la réécoute, pour mesurer fausses erreurs, validations indues et non-validations ; appel pendant une séance ; pièce bruyante pour déclencher le message de captation douteuse ; latences selon les protocoles. |
| **Ne peut pas tester** | Autres appareils, autres pianos (R-15) : bêta. |

### I4 — Mon carnet de travail

| | |
|---|---|
| **Objectif** | L'application se comporte comme un produit : premier lancement guidé, profil, réglages, bibliothèque complète, historique fiable, import MIDI. |
| **Stories** | US-CPT-01 à US-CPT-06, US-IMP-02, US-IMP-05 (CA4), US-BIB-01 (CA1, CA4, CA7), US-BIB-02 à US-BIB-05, US-BIB-07, US-HIS-01 à US-HIS-03 |
| **Prérequis** | I2 (et I3 pour le premier lancement au micro) ; décisions [QO-24 d, e, f](09-decisions-a-prendre.md) (prises) |
| **Le porteur peut tester** | Désinstaller, réinstaller et faire le parcours P1 ; le faire faire par 3 personnes qui ne connaissent pas l'application, chronométrées par un observateur muet, est reporté aux premiers testeurs de la bêta (décision [QO-44](09-decisions-a-prendre.md)) ; arrêter de force l'application en fin de séance ; mettre à jour depuis plusieurs versions anciennes ; supprimer toutes ses données puis réinstaller (rien n'est restauré) ; importer un MIDI à une piste et régler la séparation des mains. |

### I5 — Jeu continu au micro (conditionnel)

| | |
|---|---|
| **Objectif** | Travailler aussi le rythme au piano acoustique. |
| **Stories** | US-ECO-08, US-PRA-04 (partie micro), US-PRA-09 |
| **Prérequis** | POC-01 concluant en jeu continu ; décisions [QO-26, QO-29, QO-32, QO-43](09-decisions-a-prendre.md) (prises ; QO-43 se conclut par la mesure faite dans cet incrément) |
| **Le porteur peut tester** | Jouer une démonstration en continu à 80 % avec le métronome visuel ; s'arrêter, reprendre en jouant ; répéter un passage ; capturer les séances (US-QUA-05) et vérifier les erreurs de rythme à la réécoute ; mesurer le délai du compte-rendu et choisir entre 2 s et 10 s avec progression (QO-43). |

### I6 — Stabilisation et préparation de la bêta (jusqu'à J2)

| | |
|---|---|
| **Objectif** | Atteindre J2 : MVP utilisable sans assistance, prêt pour les testeurs. |
| **Stories** | US-CPT-04 (anglais complet, lexique), US-RES-08, US-CNF-05, US-CNF-06, US-QUA-04 (campagne complète), corrections d'anomalies |
| **Prérequis** | I1 à I4 (et I5 s'il existe) acceptés ; décisions [QO-28](09-decisions-a-prendre.md) et [QO-20](09-decisions-a-prendre.md) (canal de la bêta) prises |
| **Le porteur peut tester** | Campagne de recette complète (mode avion, rotations, interruptions, accessibilité, taille de police à 130 %) ; export et relecture d'un rapport de test ; notice de confidentialité ; non-régression manuelle des incréments précédents. |

### Critères du jalon J2

J2 est atteint quand :
1. toutes les stories du périmètre MVP arrêté par le porteur (fermes, et la conditionnelle si I5 existe) sont finies ([07](07-definition-of-ready-done.md)) ;
2. **au moins 3 testeurs** qui ne connaissent pas l'application réalisent **sans intervention**, avec pour seule aide le guide d'installation écrit, le scénario de recette J2 : installer l'APK, importer un MusicXML fourni, l'écouter à 60 %, pratiquer en mode attente au micro sur un piano acoustique, ouvrir le compte-rendu ;
3. chacun répond juste à au moins 2 des 3 questions sur son compte-rendu (score, passage à retravailler, type d'erreur dominant) ;
4. le banc est au vert sur les corpus public et privé, et les cibles ENF-QA-01 sont atteintes ;
5. aucune anomalie bloquante n'est ouverte, les anomalies majeures ouvertes sont acceptées une à une par le porteur, et la campagne de mesures US-QUA-04 est complète ;
6. les notes de version listent ce qui n'est vérifié que par simulation (MIDI USB) ou sur un seul appareil et un seul piano (micro).

## 4. Récapitulatif par incrément

| Incrément | Stories (premier incrément) | Dont conditionnelles | Dépend de |
|---|:-:|:-:|---|
| I0 — Outillage | 7 | 0 | POC-05 (banc), Ronan Launay et rlaunay@free.fr (QO-34), clé de signature (QO-35) |
| I1 — Voir et entendre | 24 | 0 | POC-04, test d'affichage du porteur (QO-27) |
| I2 — Moteur (MIDI simulé) | 24 | 0 | POC-05 |
| I3 — Micro en attente | 11 | 0 | J1 (POC-01, POC-02) |
| I4 — Carnet de travail | 15 | 0 | I2 (I3) |
| I5 — Jeu continu au micro | 2 | 1 (US-ECO-08) | POC-01 (continu) |
| I6 — Stabilisation | 3 | 0 | I1 à I5 |
| **Total** | **86** | **1** | |

Depuis la v0.3, aucune story n'est « proposée » : les 6 stories qui l'étaient sont fermes (décisions QO-23, QO-26 et QO-28). US-PRA-09 (I5) reste liée à l'existence d'I5 ; si POC-01 n'est concluant qu'en mode attente, elle peut être livrée en MIDI seul (§2).

Les stories livrées en plusieurs temps sont comptées dans leur premier incrément : US-QUA-04 (I0), US-BIB-01 et US-IMP-05 (I1), US-ANA-02, US-ANA-08, US-PRA-02, US-PRA-04, US-PRA-08 (I2), US-CPT-04 (I4). Leurs critères indiquent l'incrément où ils sont vérifiés.

## 5. Registre des vérifications reportées en bêta

Ce que le porteur ne peut pas vérifier seul est vérifié par simulation au MVP (preuve jointe à la story), puis en bêta. **Un échec en bêta ouvre une anomalie bloquante pour J3.** Les notes de version de J2 listent ces points.

| Story et CA | Simulation faite au MVP | Critère de bêta | Testeurs ou appareils | Si échec |
|---|---|---|---|---|
| US-ECO-02 CA7 — MIDI USB réel | Périphérique virtuel, rejeu (US-QUA-03) | 0 note perdue sur 500 ; détection < 2 s ; plusieurs appareils ; transposition détectée | ≥ 3 pianos numériques de marques différentes | Bloquant J3 |
| US-ECO-06 CA6 — latence MIDI | Latence simulée retrouvée à ± 10 ms | Latence mesurée ≤ 30 ms [PO] | ≥ 3 pianos numériques | Bloquant J3 |
| US-PRA-03 CA5 — autre main en MIDI réel | Simulation | Aucune note de l'application prise pour du jeu | ≥ 2 pianos numériques | Bloquant J3 |
| US-ECO-07 CA11, US-ECO-08 — micro | Corpus et téléphone du porteur | Cibles ENF-QA-01 micro | ≥ 3 appareils (dont 1 d'entrée de gamme) et ≥ 3 pianos, dont un à queue et un numérique sur ses haut-parleurs | Bloquant J3 (ou fonction retirée) |
| US-ECO-11 — écouteurs | Détection de la sortie Bluetooth | Aucun clic capté avec écouteurs filaires ; avertissement Bluetooth affiché | ≥ 3 testeurs | Majeur |
| US-VIS-03 CA2 — tablette 10 pouces | Calcul sur l'émulateur | Protocole de lisibilité réussi à 70 cm | ≥ 2 tablettes | Bloquant J3 |
| US-VIS-07 CA5 — tablettes 7 et 13 pouces | Émulateur | Aucun défaut d'affichage | 1 tablette de chaque taille | Majeur |
| US-VIS-08 CA3 — entrée de gamme | Émulateur à mémoire réduite | P30 sans arrêt ; ENF-PERF-05 tenue | 1 appareil d'entrée de gamme | Majeur |
| US-QUA-04 CA6 — latences sur d'autres appareils | Téléphone du porteur | Cibles ENF-PERF au 95e centile | ≥ 3 appareils | Majeur (liste d'appareils compatibles) |
| US-HIS-03 CA5 — mises à jour | Tests de migration N−k → N | Aucune perte de données | Tous les testeurs | Bloquant J3 |
| US-CPT-06 — parcours MIDI | — | ≤ 5 min jusqu'au premier compte-rendu | ≥ 2 testeurs équipés | Majeur |
| US-VIS-02 CA1 — mode d'affichage du MVP (décision QO-27) | Test au pupitre fait par le porteur seul, avant I1 (protocole du fichier 09) | Mêmes tâches que le test du porteur ; le mode retenu ne produit aucune erreur de lecture due à la taille, montre toujours au moins une mesure entière après le curseur et reçoit un confort jugé acceptable | ≥ 3 testeurs, dont un débutant et une personne de plus de 45 ans | Réexamen de QO-27 par le porteur |
| US-VIS-03 CA3 — lisibilité à 60 cm (décision QO-44) | Protocole passé par le porteur seul en I1 | Protocole de la revue UX §3.2 : aucune erreur due à la taille chez au moins 3 lecteurs sur 4, aucun penché | ≥ 3 testeurs en plus du porteur, dont une personne de plus de 45 ans | Bloquant J3 |
| US-CPT-06 CA6 — premier lancement sans aide (décision QO-44) | Parcours fait par le porteur seul en I4 | Chacun atteint le premier compte-rendu en ≤ 5 min, sans intervention | 3 premiers testeurs qui ne connaissent pas l'application | Majeur |
| US-ECO-02 CA8 — appareils MIDI 2.0 (paquets universels) | Non pris en charge au MVP : seuls les appareils en flux d'octets MIDI 1.0 sont listés | Comportement constaté avec un appareil MIDI 2.0 consigné, pour décider de sa prise en charge en V1 | Testeurs équipés d'un tel appareil, s'il y en a | Question pour la V1 |

Autres mesures de la bêta : fausses erreurs perçues (US-RES-08) ; rétention (≥ 3 séances par semaine pendant 3 semaines), calculée sur **tous les testeurs inscrits**, les non-répondants comptés comme inactifs ([QO-28](09-decisions-a-prendre.md)).

## 6. Décisions du porteur et actions restantes par incrément

Les 23 décisions du 2026-11-07 et les 11 décisions du 2026-11-08 (QO-44 à QO-53, QO-55) sont prises ([fichier 09](09-decisions-a-prendre.md), [DAT 14](../DAT/14-decisions-a-prendre.md)). Ce tableau indique où elles s'appliquent et ce qui reste à faire.

| Avant | Décisions appliquées | Reste à faire |
|---|---|---|
| I0 | QO-34, QO-35, QO-36, QO-37, QO-45, QO-46, QO-47, QO-48, QO-49 | Le porteur fournit Ronan Launay et rlaunay@free.fr et le nom de son compte GitHub (Zebul1n) ; il installe WSL 2 et Android Studio ([DAT 10 §7](../DAT/10-build-ci-et-distribution.md#7-prérequis-du-poste-du-porteur)) ; il sauvegarde hors ligne la clé de signature dès sa création |
| I1 | QO-10, QO-27, QO-30, QO-31, QO-38, QO-44 | Le porteur fait seul le test d'affichage au pupitre |
| I2 | QO-23, QO-24, QO-25, QO-39, QO-40, QO-52 | — |
| J1 / I3 | QO-29, QO-32, QO-41, QO-42 (si le cas se produit), QO-50, QO-53 (après POC-04), QO-55 (si la mesure l'exige) | Le porteur relève les libellés Android de l'autorisation du micro ; il statue sur le jeu continu au micro au vu du rapport de POC-01 (QO-29) |
| I5, I6, bêta | QO-11, QO-20, QO-26, QO-28, QO-43 | Choix entre 2 s et 10 s au micro après la mesure d'I5 (QO-43) ; avant la V1 : compactage de l'historique (QO-11), canal public (QO-20) et question ouverte QO-54 ; réexamen des processeurs visés (QO-47) ; avant la première contribution extérieure : QO-51 reposée |
