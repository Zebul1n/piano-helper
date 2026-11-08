# 08 — Stratégie de test et banc d'évaluation

Ce fichier fixe le **cadre technique** des tests ; la stratégie et les plans détaillés relèvent de `qa-testeur` (`docs/QA/`). Décisions : [ADR-0015](adr/0015-banc-d-evaluation.md), [ADR-0022](adr/0022-reproductibilite-ordinateur-telephone.md).

## 1. Niveaux de test

| Niveau | Outil | Où | Contenu | Étiquette PRD |
|---|---|---|---|---|
| Unitaires du noyau | GoogleTest 1.18.0 (BSD-3-Clause) | CI `ubuntu-24.04` (clang-18), poste (WSL 2, même environnement) | Modèle, expansion, carte temporelle, règles de l'EDB 06, lecteurs, façade `core/capi` | [AUTO] |
| Temps réel | RealtimeSanitizer (Clang ≥ 20), ThreadSanitizer | CI (tâche dédiée) | Rappels audio et fonctions appelées ([03 §4.1](03-noyau-natif.md#41-outils-de-contrôle)) | [AUTO] |
| Reproductibilité | Empreintes des sorties sur des vecteurs de référence de `dsp`, `transcribe`, `align`, `analyse` | CI x86-64 **et** exécuteur Linux arm64 ; téléphone par `adb` | Niveau 1 de l'[ADR-0022](adr/0022-reproductibilite-ordinateur-telephone.md) | [AUTO] |
| Banc d'évaluation | `tools/bench` (C++) | CI (corpus public), poste (corpus privé), téléphone (corpus micro) | Métriques de l'[EDB 06 §9.2](../EDB/06-analyse-du-jeu-et-erreurs.md#92-métriques), non-régression | [BANC] |
| Unitaires Kotlin | JUnit, Room sur JVM | CI | Cas d'usage, migrations, rapport de test | [AUTO] |
| Instrumentés | Compose UI test sur émulateur | CI la nuit, poste | Écrans, cycle de vie, interruptions, arrêts forcés, sauvegarde, rotation et redimensionnement en séance | [AUTO], [ÉMU] |
| Captures du rendu | Comparaison avec seuil ; références produites en CI seulement ([07 §6](07-import-et-rendu-de-partition.md#6-rendu-et-mise-en-cache)) | CI | US-VIS-01 CA4 | [AUTO] |
| Contrôles de l'APK | Scripts Node.js, `zipalign`, `llvm-nm`, `aapt2` | CI | Permissions, sauvegarde, outils absents (classes **et** symboles natifs), ABI, 16 Ko, taille | [AUTO] |
| Fuzzing | libFuzzer sur les lecteurs MusicXML, `.mxl`, MIDI, MEI | CI (courtes sessions), poste | ENF-SEC-04 | [AUTO] |
| Mesures sur appareil | Variante interne, protocoles de US-QUA-04 | Téléphone | [05 §5](05-flux-temps-reel-et-performance.md#5-stratégie-de-mesure) | [MESURE] |
| Au piano | Enregistreur de séance | Porteur | US-QUA-05 | [PIANO] |

**Tests instrumentés de la nuit** (DA-26, DEV-02) : émulateurs API 33, 36 et 37 ; un profil de largeur ≥ 600 dp avec le drapeau `UNIVERSAL_RESIZABLE_BY_DEFAULT` ; une image `google_apis_ps16k` (API 36 ou 37) après vérification de `getconf PAGE_SIZE` = 16384 ; `adb shell cmd audio set-enable-hardening enable` (Android 17) ; appel simulé ; rotation, redimensionnement et changement de thème pendant une séance (aucune suspension, aucune perte). Les tests instrumentés tournent sur la variante `interne` (x86_64) ; les contrôles statiques portent sur l'APK `beta`.

## 2. Banc d'évaluation

```
pianobench run     --corpus corpus/public@v3 --params analysis-params.json --out report.json
pianobench compare --reference bench/reference/public-v3.json --candidate report.json
```

| Élément | Choix |
|---|---|
| Entrées | Corpus versionné : partitions, jeux (MIDI ; audio en FLAC **normalisé à 48 kHz**, DN-19), annotations JSON, fiches de licence |
| Exécution | Même code que l'application ; `platform/host` livre l'audio par blocs de taille aléatoire à graine fixe (DN-07) |
| Mode attente simulé | Chaque tentative est soumise **avec au moins 3 s du son qui la précède** et l'état attendu (événement courant, notes tenues) ; seule la décision prise après l'attaque de la tentative est évaluée (MIR-08) |
| Sorties | JSON (taux, effectifs, intervalles de Wilson à 95 %, ventilés par source, niveau, catégorie de pièce, type d'erreur, **octave** et condition) ; liste de chaque fausse erreur et de chaque validation indue ; tableau apparié (nouvelles fausses erreurs, fausses erreurs corrigées) ; rapport Markdown |
| Identité de l'exécution | Version du corpus, version de l'analyse, **version du compilateur et image de CI** écrites dans `report.json` ; `compare` refuse de comparer si elles diffèrent de la référence (DEV-08) |
| Références | `bench/reference/<corpus>-<version>.json`, mises à jour par un commit dédié |
| Archivage | Rapports d'incrément archivés comme pièces jointes de diffusion ou dans `bench/reports/`, jamais seulement comme artefacts de CI (90 jours au plus, DEV-15) |
| Données non commerciales (QO-37) | Script versionné, hors dépôt, jamais bloquantes |

Les métriques de transcription sont implémentées en C++ et **validées une fois** contre `mir_eval` 0.8.2 (MIT) pendant POC-01, dans WSL 2 (QO-48).

## 3. Règle de non-régression

Reprise de l'[EDB 06 §9.3](../EDB/06-analyse-du-jeu-et-erreurs.md#93-règle-de-non-régression) et de US-QUA-02 CA3, écrite **en effectifs** (MIR-17) :

| Contrôle | Règle | Échec |
|---|---|---|
| Même corpus, même environnement | Identifiants de corpus, de compilateur et d'image identiques ; sinon référence recalculée d'abord | Refus de comparer |
| MIDI | Nombre de fausses erreurs ni plus élevé au total, ni dans un fichier (tolérance nulle) | Bloquant |
| Micro, reproductibilité | Niveau 1 : sorties identiques au bit près ; niveau 2 (sorties d'un moteur d'inférence tiers) : écart ≤ ε ([ADR-0022](adr/0022-reproductibilite-ordinateur-telephone.md)) | Bloquant |
| Micro, fausses erreurs, par mode | Hausse nette ≤ ⌊0,001 × N⌋ sur le corpus agrégé de N notes (0 sous 1 000 notes, 1 de 1 000 à 1 999…) | Bloquant au-delà |
| Micro, garde par sous-corpus | Grave, aigu, pédale, notes répétées, bruit, 1,5 m, transposé : hausse nette ≤ max(2 ; ⌊0,005 × N_s⌋) | Bloquant au-delà |
| Validations indues (mode attente) | Même règle que les fausses erreurs | Bloquant au-delà |
| Cibles | Aucun taux au-dessus d'ENF-QA-01 | Bloquant |
| Nouvelles fausses erreurs | Listées (b) avec les fausses erreurs corrigées (c), même si tolérées | Information |
| Hausse acceptée | Décision du porteur citant b et c, consignée dans le commit de référence | — |

Cette écriture remplace « + 0,1 point [PO, à valider] » ; modification proposée à l'EDB 06 §9.3 et à US-QUA-02 CA3 ([fichier 15](15-modifications-edb-prd-proposees.md)).

## 4. Déterminisme et équivalence ordinateur / téléphone

Décision : [ADR-0022](adr/0022-reproductibilite-ordinateur-telephone.md) (arbitrage entre `expert-audio-mir`, `dev-natif-cpp` et `devops-release`).

| Niveau | Périmètre | Règle | Écart = anomalie |
|---|---|---|---|
| 1 | Code du projet : `dsp`, vérificateur temps réel, vérificateur différé maison, inférence écrite à la main (option C d'[ADR-0011](adr/0011-moteur-d-inference-onnx-runtime.md)), `align`, `analyse` | Sorties **identiques au bit près**, comparées par empreinte du flux d'événements et des résultats | Toute différence |
| 2 | Sorties d'un moteur d'inférence tiers (si POC-01 en retient un) | max \|p_téléphone − p_ordinateur\| ≤ ε (fixé par POC-01) ; décisions et instants comparés **en trames** | Écart au-delà de ε, ou décision changée alors que sa marge au seuil sur l'ordinateur est ≥ δ ; les changements près du seuil sont listés, non bloquants |

Conditions du niveau 1 : Clang seul (clang-18 sur `ubuntu-24.04` en CI et dans WSL 2 sur le poste ; Clang du NDK r30 sur le téléphone), `-ffp-contract=off -fno-fast-math` sur tous les modules de calcul et les sources tierces de traitement du signal, `CMAKE_CXX_EXTENSIONS OFF`, ni `-Ofast` ni `-march=native`, tables précalculées et embarquées (fenêtres, facteurs de rotation, filtres, noyaux), aucune fonction transcendante de la bibliothèque standard dans les décisions, pocketfft sans vectorisation ni fils, FTZ/DAZ fixés à l'identique, `long double` interdit, instants et coûts en entiers dans `align` et `analyse`, trames ancrées sur la position absolue. Contrôles : empreintes sur x86-64 et arm64 en CI, sur le téléphone par `adb` ; options de compilation effectives comparées entre la construction Gradle et les préréglages CMake (`compile_commands.json`, DEV-07).

Cette définition remplace celle de la v0.1 (« décision ou instant > 1 ms ») et répond à INC-04 ; modification de US-QUA-02 CA5 proposée au [fichier 15](15-modifications-edb-prd-proposees.md).

## 5. Simulation MIDI

| Niveau | Mécanisme | Couvert | Non couvert |
|---|---|---|---|
| Noyau (banc, tests) | Fichier MIDI rejoué avec erreurs, latence, gigue ; flux d'octets avec *running status*, SysEx, messages temps réel intercalés (DN-11) | Analyse, analyseur MIDI | Pile Android |
| Appareil virtuel (variante interne) | `MidiDeviceService` déclaré dans `:tools` ([04 §8](04-application-android.md#8-variantes-de-construction)) | `MidiManager`, AMidi, horodatages, plusieurs canaux, pédale à mi-course, transposition ; débranchement par désactivation du composant [NON VÉRIFIÉ, prototypé en I0] | Pilote USB, adaptateurs, vrais claviers, appareils MIDI 2.0 |
| Clavier visuel | Écran de la variante interne | Jeu au toucher | Vélocité réaliste |

Vérifications réelles : registre de la bêta ([PRD 05 §5](../PRD/05-plan-de-release.md)).

## 6. Robustesse et données

| Exigence | Test |
|---|---|
| US-HIS-01 CA2 (ENF-FIA-01) | Test instrumenté : 50 arrêts forcés du processus pendant une séance simulée et dans les 2 s qui suivent. Tâche nocturne **dédiée** pour les 10 arrêts brutaux de l'émulateur : script bash qui pilote directement `emulator` et `adb` (démarrage à froid sans cliché, `adb wait-for-device`, attente de `sys.boot_completed`, séance simulée, `adb emu kill`, redémarrage, `PRAGMA integrity_check`), hors d'`android-emulator-runner`, qui ne supporte pas l'arrêt de son émulateur (DEV-14) |
| US-HIS-03 (ENF-FIA-03) | Migration de chaque base de référence archivée ; injection de défaut |
| `synchronous=FULL` | Lecture du réglage dans une transaction d'écriture (DA-14) |
| US-IMP-06 (ENF-SEC-04) | Fichiers piégés : refus en ≤ 5 s, processus principal intact, mémoire bornée |
| US-CNF-01 | Contrôles de l'APK ; sauvegarde forcée sur émulateur |
| Déconnexions audio | Casque branché et débranché pendant la lecture et la captation (POC-02, DN-08) |

## 7. Matrice : quoi est vérifié où

| Exigence | CI | Poste (WSL 2) | Téléphone | Piano | Bêta |
|---|:-:|:-:|:-:|:-:|:-:|
| ENF-QA-01 MIDI | ● (public) | ● (privé) | | | ● |
| ENF-QA-01 micro | ● (public) | ● (privé) | ● (corpus injecté) | ● (séances capturées) | ● |
| ENF-QA-02 | ● | ● | | | |
| ENF-PERF-01 à 10 | | | ● | ● | ● |
| ENF-FIA-01, ENF-FIA-03 | ● (émulateur) | | ● | | ● |
| ENF-CONF-01, ENF-CONF-03 | ● | | ● | | |
| ENF-SEC-04 | ● | ● (fuzzing long) | | | |
