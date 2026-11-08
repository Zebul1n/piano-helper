# ADR-0011 — Moteur d'inférence (si un modèle est retenu)

| Statut | Date | Exigences |
|---|---|---|
| Proposée — à confirmer par POC-01 (QO-50 décidée le 2026-11-08) | 2026-11-07, révisée le 2026-11-08 | EF-ANA-13, ENF-PERF-04, ENF-RES-01, ENF-RES-03, ENF-CONF-02, ENF-COMP-01, ENF-MAINT-01 |

## Contexte

Si l'étage différé d'[ADR-0010](0010-transcription-deux-etages.md) utilise un modèle d'apprentissage, il faut l'exécuter sur le téléphone et dans le banc, de façon reproductible, sans services Google ni télémétrie, dans le budget de taille. Les revues ont apporté trois faits nouveaux sur l'AAR officiel d'ONNX Runtime 1.31.1 :
- il **intègre la télémétrie de Microsoft, activée par défaut** (identifiant d'appareil persistant, événements en SQLite local) [VÉRIFIÉ par `juriste-conformite`, `docs/Privacy.md` v1.31.1 et inspection de l'AAR] (JUR-D01) ;
- `libonnxruntime.so` pèse **34,2 Mo** en arm64 et 40,8 Mo en x86_64, l'AAR fournit quatre ABI et aucun fichier de mentions pour ses composants inclus (dont Eigen sous MPL-2.0) [VÉRIFIÉ par `dev-natif-cpp`, `devops-release` et `juriste-conformite`] (DN-26, DEV-03, JUR-D02) ;
- ses segments sont alignés sur 16 Ko [VÉRIFIÉ par `dev-natif-cpp` et `devops-release`] ; il n'a pas de module Prefab ; ses noyaux de calcul dépendent du processeur (DN-27).

## Options comparées dans POC-01

| Option | Pour | Contre |
|---|---|---|
| A — AAR officiel d'ONNX Runtime | Prêt à l'emploi | Télémétrie (bloquant pour une version diffusée, JUR-D01) ; 34 Mo ; mentions absentes ; sources correspondantes à joindre ; binaire non construit par le projet (F-Droid) |
| B — ONNX Runtime construit par le projet (`--no_telemetry`, réduit aux opérateurs du modèle) | Pas de télémétrie ; plus petit | Scripts de construction en Python dans la chaîne de l'APK ; une quarantaine de dépendances à copier dans `third_party/` ; construction longue (JUR-D03, DN-27, avis de `devops-release` sur QO-50) |
| C — **Inférence écrite à la main** dans `dsp`/`transcribe` | Aucun binaire tiers ; 0 Mo ajouté ; identité au bit près (niveau 1 d'[ADR-0022](0022-reproductibilite-ordinateur-telephone.md)) ; construction reproductible ; F-Droid possible | Effort : 1 à 2 milliers de lignes pour Basic Pitch, CQT comprise [estimation de `dev-natif-cpp`] ; validée contre ONNX Runtime sur l'hôte ; réaliste seulement pour un petit modèle |
| D — LiteRT 2.3.2 | Léger | Voie C Android par les services Google Play |

## Décision proposée

**Arbitrage des revues** (`juriste-conformite`, `dev-natif-cpp`, `devops-release` convergent ; `expert-audio-mir` doute du modèle lui-même) :

1. l'AAR officiel (A) n'est admis **que** dans le prototype de POC-01 et la variante interne, avec `ORT_DISABLE_TELEMETRY=1` défini avant le chargement ;
2. pour les variantes bêta et publique : **C** si POC-01 retient un modèle assez petit et que l'effort est raisonnable ; sinon **B** ; **aucun moteur** si POC-01 retient le vérificateur maison pour le différé ;
3. le porteur a décidé le 2026-11-08 d'exclure l'AAR officiel des versions diffusées (QO-50, [fichier 14](../14-decisions-a-prendre.md)) ; le choix entre C, B et aucun moteur se fait au jalon J1, au vu de POC-01.

Réglages si ONNX Runtime est utilisé : `intra_op_num_threads=1`, `inter_op_num_threads=1`, `ORT_SEQUENTIAL`, `session.intra_op.allow_spinning=0`, niveau d'optimisation de graphe fixé ; `libonnxruntime4j_jni.so` exclu ; extraction de l'AAR par une tâche Gradle (DN-27).

## À confirmer par POC-01

Choix du différé (ADR-0010), facteur temps réel, taille, effort de C, écart hôte / téléphone (ε).
