# ADR-0010 — Transcription au micro en deux étages informés par la partition

| Statut | Date | Exigences |
|---|---|---|
| Proposée — à confirmer par POC-01 | 2026-11-07, révisée le 2026-11-08 | EF-ANA-01, EF-ANA-13, EF-ECO-04, EF-ECO-07, EF-ECO-08, EF-ECO-10, ENF-PERF-01, ENF-PERF-02, ENF-PERF-04, ENF-QA-01 |

## Contexte

Risque n° 1 (R-01). Le mode attente exige une décision en ≤ 150 ms au micro depuis l'attaque, dont environ 60 ms pour la décision elle-même en médium ([05 §3](../05-flux-temps-reel-et-performance.md#3-budgets-de-latence-par-étape)). Le compte-rendu peut arriver après la fin. L'application sait ce qui doit être joué.

## Options étudiées pour l'étage différé

| Option | Licence | Faits | Appréciation |
|---|---|---|---|
| A différé — vérificateur maison informé par la partition, **non causal** | Projet | Même code que l'étage temps réel, avec contexte de part et d'autre de chaque attaque | **Candidat de premier rang** (MIR-02) |
| B — Basic Pitch 0.4.0 | Apache-2.0 (code et poids) | 16 782 paramètres ; ONNX de 230 Ko ; 22 050 Hz ; F-mesure **70,9 %** par note sur MAESTRO dans l'article (Onsets and Frames : 95,2 %) ; anticipation propre au modèle **> 200 ms** en médium (CQT centrée de Q ≈ 51, convolutions non causales ≈ 10 trames) ; entraîné en partie sur MAESTRO (NC, QO-41) | Candidat ; qualité piano incertaine (MIR-01, MIR-02 ; [VÉRIFIÉ par `expert-audio-mir` le 2026-11-08, `models.py`, arxiv 2203.09893]) |
| D — *Onsets and Velocities* (2023) | Code GPL-3.0 ; **licence des poids non indiquée** | ≈ 3,1 M de paramètres ; F-mesure sur les attaques 96,78 % sur MAESTRO ; présenté comme utilisable en temps réel [selon `expert-audio-mir`, arxiv 2303.04485] | Candidat, sous réserve de la licence des poids (JUR) |
| C — Kong et al. | Poids CC-BY-4.0 | 172 Mo, 16 kHz, F-mesure 96,72 % sur les attaques | Référence haute et vérité terrain sur ordinateur seulement |
| Onsets and Frames | Apache-2.0 (code) | Dépôt archivé ; 76 à 109 Mo | Écarté |
| Modèles de madmom | CC BY-NC-SA 4.0 | — | Exclus (NC) |

## Décision proposée

Deux étages ([EDB 07 §7.3](../../EDB/07-contraintes-et-pistes-techniques.md#73-exécution-sur-lappareil)) :

1. **Temps réel** : vérificateur informé par la partition, spécifié au [03 §3.5](../03-noyau-natif.md#35-transcription-micro) d'après la proposition de `expert-audio-mir` (MIR-03) : détection d'attaques causale, **hypothèses concurrentes** (attendue, voisines, octaves, notes qui résonnent, sons de l'application, rien), preuve par **augmentation** d'énergie, règle d'octave sur les partielles impaires, test séquentiel, **décision asymétrique**, verdict par événement (`EventVerdict`, MIR-25). Aucun réseau de neurones ni CQT n'est nécessaire.
2. **Différé** : le meilleur des candidats A différé, B et D selon POC-01, exécuté par fenêtres **pendant** la séance ; il fait foi pour le jeu continu (EF-ANA-13 CA1). **En mode attente** (décision du porteur QO-52 du 2026-11-08, MIR-16) : le compte-rendu reprend les décisions temps réel ; le différé, facultatif dans ce mode, peut seulement rendre une décision incertaine (hors score), jamais créer une erreur (EF-ANA-13 CA2).

Le raisonnement de la v0.1 (« Basic Pitch travaille sur des fenêtres de 2 s ») était faux : les 2 s ne sont qu'un découpage d'inférence ; la vraie limite est l'anticipation du modèle (MIR-01). La conclusion (Basic Pitch inutilisable pour le mode attente) ne change pas.

## À confirmer par POC-01

Critères et règle de décision de J1 du [fichier 12](../12-specifications-techniques-des-poc.md#poc-01--transcription-au-micro) : fausses erreurs, validations indues sur ≥ 400 tentatives fausses avec borne de Wilson, non-validations, délai par octave, incertitude de datation, temps de calcul du différé, reproductibilité.

## Conséquences

- RT-02 relevé à P = 3 ([13](../13-risques-techniques-et-questions.md#1-risques-techniques)).
- Si aucun modèle n'est retenu, aucun moteur d'inférence n'entre dans l'APK ([ADR-0011](0011-moteur-d-inference-onnx-runtime.md)).
