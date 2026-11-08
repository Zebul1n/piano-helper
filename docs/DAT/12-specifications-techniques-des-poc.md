# 12 — Spécifications techniques des POC

Ce fichier précise, pour chaque preuve de concept de l'[EDB 13 §3](../EDB/13-jalons-et-demarche.md#3-phase-1--preuves-de-concept-poc), le protocole technique, l'outillage, les mesures et les critères de sortie, **adaptés au matériel réel** : un téléphone Android 14, l'émulateur, un piano droit, pas de piano numérique, pas de tablette, pas de second téléphone (K7). Les rapports sont rédigés par `expert-audio-mir` (POC-01, POC-05) et `dev-natif-cpp` / `dev-android` (POC-02, POC-04) dans `docs/POC/`, relus par `qa-testeur` et `architecte`.

Règles communes :
- protocoles écrits **avant** l'exécution ; latences au 95e centile **du total mesuré**, durées en médiane et maximum ; résultats **par octave** au micro ;
- taux publiés avec effectifs et intervalle de Wilson à 95 % ; la décision de passage tient compte de la borne haute (MIR-07) ;
- chaque rapport sépare **mesuré** (corpus du porteur), **estimé** (extrapolation) et **supposé**, et ne vaut que pour cet appareil et ce piano (R-15) ;
- outillage hors APK dans WSL 2 (décision QO-48).

Ordre recommandé : POC-05 et POC-04 (sans matériel particulier ; ils construisent le banc et le rendu), puis la phase 0 de POC-01 (application de captation, qui préfigure POC-02), puis POC-02 et la suite de POC-01.

## POC-01 — Transcription au micro

**Objectif** (EDB) : mesurer la qualité atteignable de détection des notes d'un piano acoustique au micro du téléphone, en exploitant la partition. Décisions à confirmer : [ADR-0010](adr/0010-transcription-deux-etages.md), [ADR-0011](adr/0011-moteur-d-inference-onnx-runtime.md), [ADR-0019](adr/0019-traitement-du-signal.md). Protocole détaillé : [revue `expert-audio-mir` §6](revues/expert-audio-mir.md#6-proposition-détaillée--protocole-de-poc-01-adapté-au-matériel-réel), repris ici.

### Phase 0 — captation (1 à 2 jours)

Application minimale (préfiguration de POC-02) : Oboe à 48 kHz mono, préréglage `Unprocessed` si `PROPERTY_SUPPORT_AUDIO_SOURCE_UNPROCESSED` est présente, sinon `VoiceRecognition` ; enregistrement FLAC, préréglage demandé, propriété système et couples (position, temps) de `getTimestamp`. Relevé de la propriété `UNPROCESSED` du téléphone (son absence est un résultat, RT-09). Test de cohérence du clic émis par le haut-parleur et capté par le micro (MIR-06, [ADR-0024](adr/0024-mesure-des-latences.md)).

### Phase 1 — enregistrements (4 à 6 h de jeu réparties sur plusieurs jours)

| Lot | Contenu | Volume visé | Sert à |
|---|---|---|---|
| L0 | Bruit de fond (30 s par séance) ; gamme chromatique de la 0 à do 8, notes d'1 s, sans pédale, en *p* et en *f*, téléphone sur le pupitre et à 1,5 m | 4 × 88 notes | Gabarits, inharmonicité et accord étiré par registre, σ et délai par octave, bornes de US-ECO-07 CA6 |
| L1 | Mode attente, jeu juste, événement puis pause d'environ 1 s ; 10 pièces dont des pièces à une main (QO-42) ; avec et sans pédale | ≥ 1 000 événements | Non-validations, fausses erreurs, délai, résonance |
| L2 | Mode attente, **tentatives fausses** scénarisées : ±1 et ±2 demi-tons, octave en dessus et en dessous, note d'accord oubliée, note voisine ajoutée | **≥ 400 tentatives fausses**, dont ≥ 80 d'octave | **Validations indues** (MIR-07, bloquant), règle d'octave |
| L3 | Jeu continu au métronome entendu **dans un écouteur filaire**, tempo lent, juste puis avec erreurs | ≥ 1 000 notes | Jeu continu, incertitude de datation du différé, QO-29 |
| L4 | Conditions : bruit (fichier CC0), jeu trop doux, 1,5 m, métronome par le haut-parleur, autre main jouée par l'application, bruits mécaniques ; traitements « voix » en **prises alternées courtes et identiques** (MIR-24) | ≥ 200 notes par condition | US-ECO-07 CA8 et CA10, US-ECO-10, US-ECO-11 |
| L5 | Versions transposées de −20 et +24 cents dérivées de L1 et L3, vérité terrain temporelle recalculée avec le même rapport (MIR-23) | Dérivé | US-ECO-09 CA1 (limite : pas de désaccord inégal) |

Fiche par prise (annexe F de PED) ; le porteur enregistre seul (US-QUA-05 CA4) ; audio normalisé à 48 kHz (DN-19).

### Phase 2 — vérité terrain semi-automatique (MIR-09)

1. Hauteurs : partition + fiche de la prise.
2. Modèle de Kong et al. (ordinateur seulement) sur chaque prise, aligné sur la partition.
3. Désaccords vérifiés à la réécoute par le porteur sur un spectrogramme ; annotation douteuse retirée.
4. Sous-corpus manuel : ≥ 300 attaques annotées deux fois à au moins une semaine d'intervalle ; il mesure l'accord du porteur avec lui-même (95e centile ≤ 10 ms) et l'écart Kong ↔ annotation ; si ce dernier est ≤ 10 ms au 95e centile, les attaques de Kong font référence partout.
5. Usage des sorties de Kong comme annotations : à confirmer par `juriste-conformite` (poids CC-BY-4.0).

### Phase 3 — approches comparées

| Approche | Où | Mode | Remarque |
|---|---|---|---|
| A temps réel : vérificateur informé par la partition ([03 §3.5](03-noyau-natif.md#35-transcription-micro)), C++ | Banc, téléphone par `adb` | Attente, retour en direct | Seule approche compatible avec le budget de 60 ms |
| A différé : même vérificateur, **non causal** | Banc, téléphone | Compte-rendu | Candidat **de premier rang** (MIR-02) |
| B : Basic Pitch 0.4.0 interprété avec la partition | Python (référence), puis C++ (ONNX Runtime ou inférence écrite à la main) | Compte-rendu | F-mesure publiée de 70,9 % par note sur MAESTRO (MIR-02) ; anticipation > 200 ms (MIR-01) |
| C : Kong et al. | Ordinateur seulement | Référence haute, vérité terrain | 172 Mo : non embarquable |
| D : *Onsets and Velocities* | Ordinateur d'abord | Compte-rendu, éventuellement temps réel | Environ 3,1 M de paramètres ; code GPL-3.0 ; **licence des poids à vérifier par JUR avant tout usage** |

Pour B, si le modèle est retenu : comparaison de l'inférence écrite à la main (option C d'ADR-0011) avec ONNX Runtime à ε près, et chiffrage de l'effort ; les deux chaînes reçoivent **le même fichier à 22,05 kHz** (MIR-14, MIR-22). Mode attente simulé avec 3 s de contexte (MIR-08). Réglage sur 5 pièces, évaluation sur les 5 autres, puis inversion.

### Phase 4 — mesures et règle de décision de J1

| Mesure | Ventilation | Règle de J1 |
|---|---|---|
| Fausses erreurs (attente, continu) | Mode, octave, condition, pédale | Valeur ≤ 5 % **et** borne haute de Wilson ≤ 7,5 % |
| Validations indues (L2) | Type de tentative, octave | Valeur ≤ 1 % **et** borne haute de Wilson ≤ 1,5 % |
| Non-validations | Octave | Valeur ≤ 5 % |
| Délai de décision (de l'attaque de référence à la décision) | Octave ; 95e centile | ≤ 60 ms en médium (do 3 à do 6) ; autres registres publiés ; si seul le grave dépasse, cible propre au grave fixée au vu des mesures (décision QO-55) |
| Incertitude de datation : biais b_r, σ_r robuste (1,4826 × écart absolu médian, part de l'annotation retirée), u_r = 97,5e centile | Registre, **étage qui produit le compte-rendu** | Alimente QO-32 selon la formule de MDAT-14, appliquée dans l'[EDB 06 §4.1](../EDB/06-analyse-du-jeu-et-erreurs.md#41-niveaux-dexigence) (MIR-10) |
| Analyse différée sur le téléphone | Approche | 1 min de jeu en ≤ 10 s (EDB 13) ; facteur temps réel ≤ 0,2 visé |
| Reproductibilité | Approche | Niveau 1 ou 2 de l'[ADR-0022](adr/0022-reproductibilite-ordinateur-telephone.md) ; ε et δ fixés |
| Taille, mémoire | Approche | ENF-RES-03, ENF-RES-04 |
| Diapason | L5 | + 0,5 point au plus (US-ECO-09 CA1) |

Avec ces effectifs, 0 validation indue sur 400 tentatives donne une borne haute de Wilson de 0,95 % ; 0 sur 300 donnerait 1,26 % (calcul de `expert-audio-mir`). La règle de J1 complète les critères de l'EDB 13 : modification proposée au [fichier 15](15-modifications-edb-prd-proposees.md). Confirmation en I3 sur les 20 séances de US-ECO-07 CA2.

**Livrable.** Rapport : résultats par approche, mode, octave et condition ; recommandation d'approche (temps réel et différé) ; σ ; branche de J1 proposée ; effort d'industrialisation, y compris l'option C d'ADR-0011.

## POC-02 — Latence et captation sur l'appareil

**Objectif** (EDB) : mesurer les latences et la stabilité de la captation. Décisions à confirmer : [ADR-0004](adr/0004-moteur-audio-oboe.md), [ADR-0009](adr/0009-synthese-tinysoundfont.md), [ADR-0024](adr/0024-mesure-des-latences.md).

**Adaptation au matériel** : la latence du MIDI USB ou Bluetooth réel ne peut pas être mesurée (R-16) ; la chaîne MIDI est mesurée depuis l'appareil virtuel, la partie USB est reportée en bêta (INC-06). Vérification externe par la webcam de l'ordinateur.

| Élément | Spécification |
|---|---|
| Application de mesure | Prototype de la variante interne : flux Oboe d'entrée et de sortie (séparés **et** `FullDuplexStream`, DN-31), appareil MIDI virtuel, écran qui change d'état à chaque décision, instrumentation du [05 §5.1](05-flux-temps-reel-et-performance.md#51-instrumentation-interne-variante-interne) |
| Outils | OboeTester (aller-retour, « Tap To Tone ») ; Perfetto ; webcam et micro de l'ordinateur ; état thermique journalisé |
| Configurations | Flux exclusif et partagé ; préréglages `Unprocessed`, `VoiceRecognition`, `Generic` ; tampons minimal et ×2 ; haut-parleur, écouteurs filaires, Bluetooth si disponible ; sondage MIDI à 1, 2 et 5 ms ; avec et sans APerformanceHint |

| Mesure | Exigence | Critère de sortie |
|---|---|---|
| **Test de cohérence des horodatages** : clic émis par le haut-parleur, capté par le micro ; écart entre instant de sortie et instant de captation | ADR-0024 | Écart ≈ 0,1 ms attendu ; sinon décalage mesuré et horodatage d'entrée abandonné |
| Taille réelle des rafales du rappel d'entrée | E3 | Mesurée par configuration (DN-07, MIR-04) |
| Chaîne micro hors décision (E1, E2 résiduelle, E3, E6, E7) | ENF-PERF-01 micro | ≤ 90 ms au 95e centile du total (laisse 60 ms à la décision) |
| Chaîne MIDI depuis l'appareil virtuel | ENF-PERF-01 MIDI, ENF-PERF-02 | ≤ 90 ms au 95e centile (laisse 10 ms à l'USB réel) |
| Délai décision → présentation | DA-08 | ≤ 1 image + composition ; ordre `postVsyncCallback` / image Compose relevé |
| Son / curseur | ENF-PERF-03 | ≤ 30 ms au 95e centile |
| Démarrage de la lecture | ENF-PERF-10 | ≤ 150 ms au 95e centile, aussi au premier appui après un retour au premier plan (DA-11) |
| Stabilité, batterie, échauffement | ENF-RES-01, ENF-RES-02 | 45 min : pas de bloc perdu, latences du début et de la fin à 10 % près ; consommation sur 1 h ; coût du sondage MIDI et d'APerformanceHint |
| Interruptions et déconnexions | ENF-FIA-04 | Appel simulé, arrière-plan, bascule du micro (`isClientSilenced`, DA-04), **casque branché et débranché** pendant la lecture et la captation (`StreamRestarted`, DN-08) : aucune erreur non gérée |
| Préréglage et contrôle de gain | US-ECO-07 CA10 | Propriété système relevée ; détection empirique du contrôle de gain (DN-10) |

**Livrable.** Tableau des latences par configuration, configuration recommandée, résultat du test de cohérence, limites de la mesure.

## POC-03 — Reconnaissance de partition (OMR)

Hors MVP (V1). Spécification à détailler avant la V1 : pas de serveur payant ; options de l'EDB (Audiveris, AGPL-3.0 ; oemer, MIT) **non vérifiées** ; critères de l'EDB 13 ; corpus de 20 PDF produits à partir des MusicXML libres du corpus.

## POC-04 — Rendu de partition et curseur

**Objectif** (EDB) : valider le rendu, ses performances, le curseur et la surimpression. Décisions à confirmer : [ADR-0006](adr/0006-moteur-de-rendu-verovio.md), [ADR-0007](adr/0007-affichage-liste-d-affichage-compose.md), [ADR-0008](adr/0008-modele-de-partition-et-format-pivot.md), [ADR-0021](adr/0021-import-en-processus-isole.md), [ADR-0023](adr/0023-deux-bibliotheques-natives.md). Démarre en premier : I1 en dépend (QO-30).

| Élément | Spécification |
|---|---|
| Prototype | Kotlin/Compose ; `libpianoimport.so` (Verovio 6.4.1, options `NO_*`, `-Os`) dans un processus `:import` ; `libpianocore.so` sans Verovio dans le processus principal ; bande en un seul système ; liste d'affichage ; paliers ; curseur ; coloration par identifiant ; zoom par mise à l'échelle |
| Comparaison de la sortie (DN-22) | **Option D** (`RenderToDeviceContext` vers `DisplayListDeviceContext`) contre **option A** (conversion du SVG, y compris les `tspan` SMuFL) : temps d'import de P10, pic mémoire de P30, fidélité par captures (SVG comme oracle), lignes de code |
| Processus d'import (DA-16) | Processus privé ; minuterie interne de 5 s ; plafond de mémoire native (`setrlimit` ou allocateur) éprouvé sur l'archive de 1 Go ; étude d'`isolatedProcess` (descripteurs ouverts par le principal, aucun accès à `filesDir`) |
| Données de Verovio | Extraction de `data/` (Bravura, Leipzig, texte) vers le stockage interne, ou chargement par archive (DN-24) |
| Repli | alphaTab Android (MPL-2.0) ; ou moteur dans une vue web (5 fichiers seulement) |
| Corpus | ≥ 20 MusicXML ; P10 et P30 |
| Prototype HTML de QO-27 | Verovio JavaScript, mêmes options |

| Mesure | Critère de sortie |
|---|---|
| Corpus de 20 MusicXML | Aucun défaut bloquant à la revue visuelle |
| Système de longueur illimitée | P30 en une bande ; position de chaque mesure |
| Import P10 | ≤ 5 s (médiane ; maximum ≤ 7,5 s) — ENF-PERF-06, surcoût du processus compris |
| Ouverture P10 | ≤ 1,5 s (médiane ; maximum ≤ 2,25 s) — ENF-PERF-05 |
| Défilement et curseur | 60 images/s, sans image manquée au 95e centile (Macrobenchmark, `FrameTimingMetric`) — ENF-PERF-08 |
| Coloration par identifiant | ≤ 16 ms |
| Mémoire | P30 ouvert sur l'émulateur à 2 Go sans manque de mémoire ; pic du processus d'import |
| Identifiants | 100 % des notes du corpus retrouvées dans la liste d'affichage |
| Taille | `libpianoimport.so` et données par ABI |
| Processus d'import | Surcoût de démarrage ≤ 300 ms [ARCH] ; fichiers piégés refusés en ≤ 5 s sans toucher le processus principal |

**Livrable.** Prototype, choix entre les options A et D, recommandation de moteur et de mode d'affichage, mesures.

## POC-05 — Alignement et classification des erreurs

**Objectif** (EDB) : valider l'alignement et la classification, en temps réel et en différé, à partir d'entrées MIDI. Décisions à confirmer : [ADR-0012](adr/0012-alignement-et-classification-maison.md), [ADR-0015](adr/0015-banc-d-evaluation.md). Livre le **prototype du banc** nécessaire à I0.

| Élément | Spécification |
|---|---|
| Implémentation | `core/align` et `core/analyse` : suiveur en ligne et alignement différé ; instants et tolérances en entiers, coûts en virgule fixe, départage explicite des égalités (DN-18) |
| Corpus | MIDI de jeu correct (≥ 3 000 notes, ≥ 20 pièces) produits à partir des partitions avec variations humaines simulées (tempo ± 20 %, gigue, accords roulés) ; erreurs injectées ; jeux hésitants et retours en arrière scénarisés ; cas particuliers de US-ANA-07 ; batterie de l'annexe E de PED ; flux MIDI avec *running status* et messages intercalés (DN-11) |
| Référence facultative | parangonar (Apache-2.0), Python dans WSL 2 |

| Mesure | Critère de sortie |
|---|---|
| Exactitude d'alignement | ≥ 98 % (EDB 13) |
| Fausses erreurs en MIDI | ≤ 1 % |
| Précision et rappel des erreurs | ≥ 95 % et ≥ 90 % (US-ANA-04 CA4) |
| Resynchronisation après retour en arrière | ≤ 2 mesures |
| Temps de calcul | Suiveur ≤ 2 ms par événement sur le téléphone ; alignement différé de 5 min ≤ 500 ms [ARCH] |
| Reproductibilité | Sorties identiques au bit près sur x86-64, arm64 (CI) et téléphone |

**Limite.** Un corpus MIDI synthétique surestime la qualité ; le rapport le dit.

**Livrable.** Bibliothèque prototype, banc automatisé exécuté en CI (I0), rapport.
