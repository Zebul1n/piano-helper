# ADR-0023 — Deux bibliothèques natives : `libpianocore` et `libpianoimport`

| Statut | Date | Exigences |
|---|---|---|
| Proposée — à confirmer par POC-04 | 2026-11-08 | ENF-PERF-09, ENF-RES-03, ENF-RES-04, ENF-SEC-04 |

## Contexte

La v0.1 prévoyait une seule `libpianocore.so` contenant Verovio, chargée dans le processus principal et dans le processus d'import. Or le processus principal n'a pas besoin de Verovio (il lit le MEI pivot et la liste d'affichage en cache) ; Verovio pèse environ 17 Mo (roue aarch64 complète) ; et la phrase « liée dynamiquement à Verovio compilé dans le même `.so` » était contradictoire (DN-25).

## Options étudiées

| Option | Pour | Contre |
|---|---|---|
| A — Une seule bibliothèque | Simple ; `c++_static` possible | Verovio chargé dans le processus principal (mémoire, démarrage) |
| **B — `libpianocore.so` + `libpianoimport.so`** | Verovio seulement dans `:import` ; processus principal plus léger | Deux `.so` C++ : `c++_shared` (≈ 1 Mo) |
| C — B + `libverovio.so` à part | Remplacement de Verovio plus simple (LGPL), utile pour iOS | Une bibliothèque de plus ; inutile, la LGPL étant satisfaite par la publication des sources (JUR-D04) |

## Décision proposée

**Option B** : `libpianoimport.so` (Verovio lié statiquement, lecteurs MusicXML et MIDI, écriture du MEI, mise en page, contexte de dessin) chargée seulement dans le processus d'import ; `libpianocore.so` (session, synthèse, `dsp`, transcription, alignement, analyse, Oboe, AMidi, lecteur MEI) dans le processus principal ; `ANDROID_STL=c++_shared` ; aucune donnée C++ ne traverse la frontière entre les deux (échanges par fichiers). Le banc lie tout statiquement.

## À confirmer par POC-04

Taille des deux bibliothèques, temps de chargement, gain mémoire du processus principal.
