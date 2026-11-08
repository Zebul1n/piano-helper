# ADR-0004 — Entrée et sortie audio : Oboe (AAudio)

| Statut | Date | Exigences |
|---|---|---|
| Proposée — à confirmer par POC-02 | 2026-11-07, révisée le 2026-11-08 | ENF-PERF-01, ENF-PERF-03, ENF-PERF-10, EF-ECO-04, EF-ECO-06, EF-LEC-01 |

## Contexte

Captation non traitée, à faible latence, datée précisément ; sortie qui démarre en ≤ 150 ms et reste synchrone avec le curseur. minSdk 33 : AAudio toujours disponible.

## Options étudiées

| Option | Licence | Pour | Contre |
|---|---|---|---|
| A — Oboe 1.11.0 (2026-09-15) | Apache-2.0 | Contournements des défauts des appareils ; `InputPreset::Unprocessed` ; rééchantillonneur utilisable seul ; `FullDuplexStream` ; OboeTester | Dépendance légère |
| B — AAudio directement | Système | Aucune dépendance | Contournements à réécrire |
| C — `AudioRecord` / `AudioTrack` en Kotlin | Système | Simple | Pas temps réel |

Sources [VÉRIFIÉ 2026-11-07 et par `dev-natif-cpp` le 2026-11-08] : github.com/google/oboe (`Definitions.h`, `AudioStream.h`, `AudioStreamCallback.h`, `FullDuplexStream.h`, README du rééchantillonneur) ; developer.android.com/ndk/reference/group/audio.

## Décision proposée

**Option A**, compilée depuis les sources :

- entrée 48 kHz mono, basse latence, exclusif demandé (repli en partagé), `Unprocessed` si la propriété système l'annonce, sinon `VoiceRecognition` ; on enregistre le préréglage **demandé** et la propriété système (le préréglage appliqué n'est pas observable, DN-10) ;
- sortie ouverte tant qu'une partition est affichée **au premier plan**, fermée sur `ON_STOP` (DA-11) ;
- **déconnexion** (`AAUDIO_ERROR_DISCONNECTED`, `onErrorAfterClose`) : réouverture sur un fil non temps réel, nouvelle génération de flux, événement `StreamRestarted` (DN-08, DA-06) ;
- `getTimestamp` appelé depuis le fil d'analyse à cadence fixe ; `getXRunCount` suivi (DN-09) ; une seule référence temporelle ([ADR-0024](0024-mesure-des-latences.md)) ;
- décimation 48 → 16 kHz par un filtre en table ; rééchantillonneur d'Oboe pour 48 → 22,05 kHz et les appareils à 44,1 kHz ([ADR-0019](0019-traitement-du-signal.md)).

## À confirmer par POC-02

Latences par configuration, disponibilité de `Unprocessed`, taille des rafales, cohérence des horodatages, flux séparés contre `FullDuplexStream`, comportement en déconnexion, coût en batterie.
