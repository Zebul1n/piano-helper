# ADR-0014 — Persistance : Room (SQLite) et fichiers internes

| Statut | Date | Exigences |
|---|---|---|
| Acceptée (révisée en v0.2) | 2026-11-07, révisée le 2026-11-08 | ENF-FIA-01, ENF-FIA-02, ENF-FIA-03, ENF-CONF-01, EF-PRO-02, EF-BIB-03, EF-CPT-06, RG-01, RG-04 |

## Contexte

Bibliothèque, séances, résultats et écarts doivent survivre aux arrêts brutaux, être migrés à chaque version et rester sur l'appareil. Le volume peut devenir important (QO-11).

## Options étudiées

| Option | Licence | Pour | Contre |
|---|---|---|---|
| A — **Room** sur SQLite (couche Kotlin) | Apache-2.0 ; SQLite domaine public | Standard Android ; migrations testées (`MigrationTestHelper`) ; schéma exporté ; requêtes réactives ; Kotlin Multiplatform depuis 2.7.0 (Android, iOS, JVM) | Couche Kotlin seulement |
| B — SQLite dans le noyau C++ | Domaine public | Partageable avec iOS au niveau C++ | Un appel JNI par requête ; outillage de migration à écrire ; réactivité à refaire |
| C — Fichiers JSON | — | Simple | Pas de transactions ; migrations et intégrité à la main |

Sources [VÉRIFIÉ 2026-11-07] : developer.android.com/jetpack/androidx/releases/room (2.8.5 du 2026-09-09 ; Kotlin Multiplatform depuis 2.7.0) ; sqlite.org/copyright.html.

## Décision

**Option A** pour les données structurées ; **fichiers** dans `filesDir` pour le fichier source, le MEI pivot et les caches de rendu, écrits de façon atomique. WAL avec `synchronous=FULL` vérifié sur la connexion d'écriture (DA-14), fin de séance en une transaction, points de reprise écrits depuis une portée d'application (DA-07), fichier de transit pour l'import (DA-17), suppression de toutes les données par `clearApplicationUserData()` (DA-15) ([06](../06-donnees-et-stockage.md)). Préférences dans une table Room (pas de seconde technologie). Sauvegarde Android désactivée ([06 §8](../06-donnees-et-stockage.md#8-sauvegarde-android-désactivée)).

## Conséquences

- Le noyau ne persiste rien lui-même, sauf les fichiers que l'application lui demande d'écrire (MEI, cache).
- Migration testée depuis chaque version diffusée (US-HIS-03).
