# ADR-0012 — Alignement et classification : implémentation maison en C++

| Statut | Date | Exigences |
|---|---|---|
| Proposée — à confirmer par POC-05 | 2026-11-07, révisée le 2026-11-08 | EF-ANA-02 à EF-ANA-06, EF-ANA-08, EF-ANA-13, EF-PRA-02, EF-PRA-08, ENF-QA-01, ENF-PERF-04 |

## Contexte

L'alignement doit résister aux erreurs, hésitations, retours en arrière et reprises (R-04), appliquer les règles de l'[EDB 06](../../EDB/06-analyse-du-jeu-et-erreurs.md), tourner en temps réel et en différé, et donner **exactement** les mêmes décisions sur toutes les plateformes (tolérance nulle en MIDI).

## Options étudiées

| Option | Licence | Pour | Contre |
|---|---|---|---|
| A — Implémentation maison : suiveur en ligne + alignement différé par programmation dynamique bornée | Projet | Règles métier intégrées ; temps réel ; portable ; reproductible | Effort |
| B — parangonar / partitura | Apache-2.0 | Algorithmes publiés | Python ; référence seulement |

## Décision proposée

**Option A**, B comme référence facultative en POC-05. Instants et tolérances en **entiers**, coûts en **virgule fixe**, **règle explicite de départage des égalités** : les décisions MIDI sont identiques au bit près sur toutes les plateformes (DN-18). Le suiveur produit le résultat provisoire ; l'alignement différé fait foi en jeu continu.

## À confirmer par POC-05

Exactitude ≥ 98 %, fausses erreurs ≤ 1 % en MIDI, resynchronisation ≤ 2 mesures, temps de calcul, reproductibilité ([12](../12-specifications-techniques-des-poc.md#poc-05--alignement-et-classification-des-erreurs)).
