# ADR-0006 — Moteur de gravure : Verovio en natif, dans le processus d'import

| Statut | Date | Exigences |
|---|---|---|
| Proposée — à confirmer par POC-04 | 2026-11-07, révisée le 2026-11-08 | EF-VIS-01 à EF-VIS-05, EF-IMP-01, ENF-PERF-05, ENF-PERF-06, ENF-RES-03, ENF-RES-04, RG-03 |

## Contexte

Il faut un moteur libre de qualité, qui lise MusicXML, rende un système de longueur illimitée, fournisse la position de chaque note et de chaque mesure et s'intègre en natif.

## Options étudiées

Vérifiées le 2026-11-07.

| Option | Version | Licence | Pour | Contre |
|---|---|---|---|---|
| A — **Verovio** (C++) | 6.4.1 (2026-11-03) | LGPL-3.0-only | Gravure de qualité ; MusicXML et `.mxl` ; `breaks=none` ; contexte de dessin abstrait (`RenderToDeviceContext`) ; version JavaScript ; portable | C++20 ; environ 17 Mo (roue aarch64 complète) ; ne lit pas le MIDI ; charge toujours Bravura, par chemin de fichiers |
| B — OpenSheetMusicDisplay | 2.2.1 | BSD-3-Clause | Curseur intégré | Vue web |
| C — alphaTab | 1.9.0 | MPL-2.0 | Version Android Kotlin | Orienté guitare ; pas de contrôle Compose |
| D — VexFlow | 5.0.0 | MIT | Bas niveau | Pas de lecteur MusicXML complet |

## Décision proposée

**Option A**, avec les précisions des revues :

- Verovio est compilé dans **`libpianoimport.so`**, chargée **seulement dans le processus d'import** ; le processus principal lit le MEI pivot et la liste d'affichage sans Verovio (DN-25, [ADR-0023](0023-deux-bibliotheques-natives.md)) ;
- options `NO_HUMDRUM_SUPPORT`, `NO_ABC_SUPPORT`, `NO_PAE_SUPPORT`, `NO_GABC_SUPPORT`, `NO_EDIT_SUPPORT`, `-Os`, LTO (DN-26) ;
- polices : fichiers **Leipzig et Bravura de Verovio, sans aucune modification**, avec leurs `LICENSE.txt` et `FONTLOG.txt` ; Bravura est indispensable (Verovio construit sa table des symboles à partir d'elle, DN-24) ; ces fichiers sont déjà une forme convertie par les titulaires (JUR-D06) ; réduction seulement avec autorisation de RISM, demandée si la taille l'exige (décision QO-53) ;
- dossier `data/` extrait vers le stockage interne au premier import (DN-24) ;
- sortie par contexte de dessin du projet ([ADR-0007](0007-affichage-liste-d-affichage-compose.md)).

**Arbitrage** : `juriste-conformite` proposait de ne pas embarquer Bravura (JUR-D06 point 5) ; c'est techniquement impossible sans modifier Verovio (DN-24). Bravura est donc embarquée telle quelle, avec sa licence ; la question du nom réservé ne se pose que pour une réduction.

## À confirmer par POC-04

Fidélité sur 20 MusicXML, P30 en un seul système, ENF-PERF-05 et 06, mémoire, taille. Repli : C.

## Conséquences

Mentions de la LGPL-3.0 §4 a) à c) dans l'écran « À propos » (JUR-D04) ; composants inclus dans Verovio inscrits un par un (JUR-D05).
