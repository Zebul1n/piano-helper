# ADR-0020 — Modèle de threads et règles temps réel

| Statut | Date | Exigences |
|---|---|---|
| Acceptée (révisée en v0.2) | 2026-11-07, révisée le 2026-11-08 | ENF-PERF-01, ENF-PERF-02, ENF-PERF-03, ENF-PERF-08, ENF-FIA-04, ENF-RES-02 |

## Contexte

Les rappels audio s'exécutent sur des fils temps réel : une allocation, un verrou ou un appel bloquant y provoque des pertes de son. Plusieurs fils produisent et consomment des données.

## Décision

- Fils : rappels d'entrée et de sortie, fil MIDI (en séance MIDI seulement), fil d'analyse temps réel (qui est aussi le fil de séance), fil différé, fil UI, fils d'arrière-plan ([03 §5](../03-noyau-natif.md#5-modèle-de-threads)) ; créés et réglés par l'interface `ThreadControl` ; sur Android, priorité par `setpriority` et **APerformanceHint** (API 33) sur le fil d'analyse (DN-13).
- **Une file SPSC par producteur** (UI → sortie, analyse → sortie, sortie → analyse pour les sons rendus) ; jamais deux producteurs sur une file SPSC (DN-01, DN-02).
- Atomiques sans verrou prouvé (`is_always_lock_free`) ; instantanés de plusieurs mots par verrou de séquence ou double tampon (DN-04).
- Débordement : le rappel rejette le bloc entrant et compte (DN-06).
- Les commandes de l'interface passent par une file vers le fil d'analyse ; `finish()` jamais sur le fil principal (DN-03).
- Les puits d'entrée et de sortie peuvent être appelés depuis le même fil (flux duplex, iOS) (DN-31).
- L'interface sonde le noyau à chaque image ; aucun appel du noyau vers Kotlin depuis un fil temps réel.

## Vérification

**RealtimeSanitizer** (Clang ≥ 20, Linux) sur les rappels annotés `[[clang::nonblocking]]`, avec `TSF_MALLOC` redirigé (DN-05) ; ThreadSanitizer ; traces Perfetto en POC-02 ; revue par `dev-natif-cpp` et `qa-testeur`.
