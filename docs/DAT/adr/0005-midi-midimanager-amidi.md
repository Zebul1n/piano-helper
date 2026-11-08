# ADR-0005 — MIDI : découverte par MidiManager, lecture par AMidi, simulation par appareil virtuel

| Statut | Date | Exigences |
|---|---|---|
| Acceptée (révisée en v0.2) | 2026-11-07, révisée le 2026-11-08 | EF-ECO-01, EF-ECO-02, ENF-PERF-01, ENF-FIA-04 ; US-QUA-03 |

## Contexte

Le MIDI USB est au MVP mais le porteur n'a pas de piano numérique (R-16) : le chemin doit être testable par simulation, au plus près du réel.

## Options étudiées

| Option | Pour | Contre |
|---|---|---|
| A — `MidiManager` (Kotlin) pour découvrir et ouvrir, AMidi (NDK) pour lire | Messages horodatés lus en natif | L'ouverture reste en Kotlin |
| B — Tout en Kotlin puis JNI | Simple | Un saut JNI par message |
| C — Accès USB brut | — | Sans intérêt |

Faits [VÉRIFIÉ 2026-11-07 ; précisions de `dev-android` et `dev-natif-cpp` le 2026-11-08] : `AMidiDevice_fromJava` prend un `MidiDevice` ouvert par `MidiManager` ; `AMidiOutputPort_receive` est non bloquant, rend 0 ou 1 message, avec les codes `DATA` et `FLUSH` ; `getDevices()` et `registerDeviceCallback(callback, handler)` sont dépréciées depuis l'API 33.

## Décision

**Option A** :

- API non dépréciées : `getDevicesForTransport(TRANSPORT_MIDI_BYTE_STREAM)`, `registerDeviceCallback(TRANSPORT_MIDI_BYTE_STREAM, executor, callback)` ; appareils en paquets MIDI universels exclus au MVP, inscrits au registre de la bêta (DA-21) ;
- référence forte au `MidiDevice` pendant la séance ; fermeture `AMidiOutputPort_close` → `AMidiDevice_release` → `MidiDevice.close()` ;
- fil MIDI qui sonde à 1 ms **seulement en séance MIDI** (DN-12) ; analyseur MIDI à états (*running status*, SysEx, messages temps réel intercalés) (DN-11) ;
- simulation : `MidiDeviceService` de la variante interne, déclaré avec `BIND_MIDI_DEVICE_SERVICE`, le filtre et la métadonnée `device_info` ; débranchement simulé par désactivation du composant [NON VÉRIFIÉ, prototypé en I0] (DA-22).

## Conséquences

Ce qui n'est pas couvert (pilote USB, vrais claviers, MIDI 2.0) est au registre de la bêta.
