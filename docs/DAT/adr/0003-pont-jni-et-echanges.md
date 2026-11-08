# ADR-0003 — Pont JNI et format des échanges

| Statut | Date | Exigences |
|---|---|---|
| Acceptée (révisée en v0.2) | 2026-11-07, révisée le 2026-11-08 | ENF-PERF-01, ENF-PERF-02, ENF-PERF-08, ENF-MAINT-01, ENF-COMP-01 |

## Contexte

L'application Kotlin pilote le noyau C++ et doit afficher, à chaque image, ce que le noyau a décidé, sans latence ni allocation ajoutées ; elle reçoit aussi des données volumineuses. Le portage iOS devra réutiliser la même frontière.

## Options étudiées

| Option | Pour | Contre |
|---|---|---|
| A — JNI écrit à la main | Aucun outil de plus | Code répétitif |
| B — SWIG | Liaisons générées | Outil de plus ; objets Java par appel |
| C — Sérialisation générée (FlatBuffers, Protocol Buffers) | Schéma typé | Compilateur de schéma de plus |
| D — JSON pour le non temps réel + enregistrements binaires fixes pour le temps réel | Bibliothèques simples ; lisible | JSON plus lent (sans importance hors temps réel) |

## Décision

**A + D**, avec les précisions des revues :

- **Façade C dans `core/capi/`** (`pianocore.h` : poignées opaques, codes d'erreur, tampons fournis par l'appelant) ; `jni/` ne fait que traduire JNI vers cette façade ; la façade est testée directement et servira à Swift ou Kotlin/Native (DN-15).
- **Temps réel** : le fil UI sonde le noyau à chaque image ; copie d'enregistrements de taille fixe dans un `ByteBuffer` direct alloué une fois, en-tête {version, type, taille}, petit-boutiste, aligné sur 8, adresse obtenue une fois ; aucun rappel du noyau vers Kotlin (DN-14).
- **Non temps réel** : JSON en `ByteArray` UTF-8 standard (jamais l'UTF-8 modifié de `NewStringUTF`), nlohmann/json et kotlinx.serialization (DA-19).
- **Durée de vie** : fermeture idempotente, appels concurrents interdits, `Cleaner` Kotlin en filet de sécurité ; ordre arrêt du sondage → `finish()` (fil d'arrière-plan) → libération ; `ensureLoaded()` devant tout appel natif (DA-19, DN-14).
- Le fil appelant de chaque méthode est spécifié ([03 §3.6](../03-noyau-natif.md#36-suivi-temps-réel-et-séance)).

## Conséquences

Le format des enregistrements est décrit une fois et testé dans les deux sens. Revue conjointe de `dev-android` et `dev-natif-cpp` pour toute nouvelle fonction.
