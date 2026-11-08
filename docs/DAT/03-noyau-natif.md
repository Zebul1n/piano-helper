# 03 — Noyau natif (C++)

Le noyau est un ensemble de bibliothèques C++20 portables qui contiennent tout le métier : partition, gravure, synthèse, captation, transcription, alignement, analyse et scores. Décisions : [ADR-0001](adr/0001-decoupage-application-noyau-outils.md), [ADR-0002](adr/0002-langages-et-normes.md), [ADR-0020](adr/0020-modele-de-threads-temps-reel.md), [ADR-0022](adr/0022-reproductibilite-ordinateur-telephone.md), [ADR-0023](adr/0023-deux-bibliotheques-natives.md).

## 1. Principes

1. **Portable** : aucune inclusion d'en-tête Android dans `core/` ; la plateforme est vue à travers des interfaces (§2). Contrôle automatique : `core` se construit pour l'hôte Linux en CI.
2. **Testable sans interface** (ENF-MAINT-01) : chaque composant a une API C++ appelable depuis GoogleTest et depuis le banc ; la façade C `core/capi/` est elle aussi testée directement (DN-15).
3. **Reproductible** (C-T3) : à entrée identique, sorties **identiques au bit près** pour le code du projet, sur l'ordinateur et sur le téléphone ; tolérance bornée seulement pour les sorties d'un moteur d'inférence tiers. Les conditions sont fixées par [ADR-0022](adr/0022-reproductibilite-ordinateur-telephone.md) : Clang seul, options flottantes strictes sur tous les modules de calcul (y compris `dsp` et les sources tierces de traitement du signal), tables précalculées, aucune fonction transcendante de la bibliothèque standard dans les décisions, instants et coûts en entiers dans `align` et `analyse`.
4. **Indépendant de la taille des blocs** (DN-07) : `dsp` et `transcribe` redécoupent le flux en **trames de taille fixe ancrées sur la position d'échantillon absolue** ; les fenêtres du transcripteur différé sont ancrées de même. Le banc livre des blocs de taille aléatoire à graine fixe et vérifie que les sorties ne changent pas.
5. **Temps réel** (C-T1) : dans les fonctions de rappel audio, ni allocation (y compris `malloc` du code C), ni verrou, ni appel système bloquant, ni journalisation formatée (§4).
6. **Le temps en échantillons, avec génération de flux** : tout instant mesuré est une position d'échantillon d'un flux, qualifiée par son **numéro de génération** (un flux rouvert après une déconnexion repart de zéro, DN-08), ou un temps monotone de la plateforme en nanosecondes pour le MIDI. La conversion passe par la correspondance alimentée par les horodatages de la plateforme (§3.3). Aucune décision ne dépend du moment où un fil a été ordonnancé.
7. **Codes, pas de textes** : le noyau produit des codes ; les textes FR et EN sont dans l'application (ENF-I18N-01).
8. **Borné par lui-même** (DN-30) : les lecteurs de fichiers comptent leur mémoire et acceptent une annulation coopérative avec délai ; aucun `abort` ni `exit` (Verovio compilé avec `NDEBUG`, journal redirigé). Le processus d'import isolé d'Android ([ADR-0021](adr/0021-import-en-processus-isole.md)) est une défense de plus, pas la seule (il n'existera pas sur iOS).

## 2. Découpage et dépendances

```
core/
  common/      types de base (Pitch, Fraction, temps), files SPSC, journal sans allocation, erreurs
  platform/    interfaces : AudioIO, MidiInput, Clock, Logger, FileAccess, ThreadControl
  capi/        façade C (pianocore.h) : poignées opaques, codes d'erreur, tampons fournis par l'appelant
  score/       modèle de partition, lecteur MEI, identifiants, expansion des reprises, carte temporelle
  dsp/         décimation, transformée de Fourier, fonction d'attaque, filtres ciblés, niveau, diapason
  transcribe/  vérificateur temps réel, transcription différée
  align/       suivi en ligne, alignement différé
  analyse/     écarts, confiance, scores, passages prioritaires, motifs
  synth/       séquenceur, TinySoundFont, métronome
  session/     orchestration d'une séance
  config/      paramètres de l'analyse (fichier versionné)
import/        (bibliothèque libpianoimport) lecteurs MusicXML et MIDI, écriture MEI, façade Verovio,
               contexte de dessin vers la liste d'affichage, rapport d'import
platform/android/   Oboe, AMidi, horloge, ThreadControl (setpriority, APerformanceHint), journal
platform/host/      fichiers audio et MIDI injectés, horloge simulée (banc, tests)
jni/                traduction JNI → core/capi (seul endroit qui connaît jni.h)
```

Deux bibliothèques partagées ([ADR-0023](adr/0023-deux-bibliotheques-natives.md), DN-25) : **`libpianoimport.so`** (Verovio, lecture des fichiers, mise en page), chargée seulement dans le processus d'import ; **`libpianocore.so`** (tout le reste), chargée dans le processus principal. Bibliothèque C++ standard partagée (`c++_shared`, DN-28). Le banc lie tout statiquement.

Dépendances autorisées (sens unique) : `session → analyse → align → transcribe → dsp → common` ; `session → synth → common` ; `score → common` ; `import → score` ; tout module peut dépendre de `platform` (interfaces) et de `config` ; `capi` dépend de `session`, `score` et (dans `libpianoimport`) d'`import`.

## 3. Composants et interfaces

Les signatures sont **indicatives** (à préciser par `dev-natif-cpp`) ; elles fixent les responsabilités, les données échangées et le **fil appelant** de chaque méthode (DN-03).

### 3.1 Captation audio

Module : `platform::AudioIO`, `dsp`.

```cpp
struct StreamPos { uint32_t generation; int64_t frame; };          // DN-08
class AudioInputSink {                                              // implémenté par le noyau
  virtual void onAudioBlock(const float* frames, int n, StreamPos first) noexcept = 0;  // rappel : copie seulement
};
class AudioIO {                                                     // implémenté par la plateforme
  virtual Result openInput(const InputConfig&, AudioInputSink*) = 0;      // fil de séance (non temps réel)
  virtual Result openOutput(const OutputConfig&, AudioRenderSource*) = 0;
  virtual std::optional<FrameTime> inputTimestamp() = 0;            // appelé par le fil d'analyse, ~ toutes les 100 ms (DN-09)
  virtual std::optional<FrameTime> outputTimestamp() = 0;
  virtual int32_t xrunCount() = 0;                                  // pertes du matériel (DN-09)
  virtual void close() = 0;
};
// Événements de plateforme, publiés dans la file sondée : StreamRestarted{génération, cause, format},
// StreamOpenFailed{cause}, InputSilenced (DN-08, DA-06).
```

- Android : Oboe 1.11.0 au-dessus d'AAudio ([ADR-0004](adr/0004-moteur-audio-oboe.md)), 48 kHz mono, basse latence, partage exclusif demandé (repli en partagé), préréglage `Unprocessed` si la propriété système `PROPERTY_SUPPORT_AUDIO_SOURCE_UNPROCESSED` l'annonce, sinon `VoiceRecognition`. On enregistre le préréglage **demandé** et la propriété système, car `AAudioStream_getInputPreset` ne dit pas si le préréglage a été appliqué (DN-10) ; le réglage du micro ajoute une détection empirique du contrôle automatique de gain (rampe de niveau sur un son de référence).
- Déconnexion (casque branché ou retiré, appareil USB ou Bluetooth) : AAudio rend `AAUDIO_ERROR_DISCONNECTED` ; Oboe ferme le flux et appelle `onErrorAfterClose` [VÉRIFIÉ par `dev-natif-cpp` le 2026-11-08]. La plateforme rouvre le flux sur un fil non temps réel, incrémente la génération et publie `StreamRestarted` ; en séance, une déconnexion d'entrée suspend la séance (DA-06).
- POC-02 compare deux flux séparés et `FullDuplexStream` d'Oboe (DN-31) ; le noyau accepte que les puits d'entrée et de sortie soient appelés depuis le même fil (cas d'iOS).
- Choix du micro intégré quand un casque avec micro est branché (US-ECO-07 CA9).
- Hôte (banc) : `FileAudioIO` lit un FLAC ou WAV **à 48 kHz uniquement** (corpus normalisé une fois pour toutes, empreinte dans l'annotation, DN-19) et le livre par blocs de taille aléatoire à graine fixe, avec une horloge simulée : même chemin que le téléphone à partir du décimateur (US-QUA-02 CA5).

### 3.2 MIDI

Module : `platform::MidiInput`.

```cpp
struct MidiEvent { int64_t timeNs; uint8_t status, data1, data2; };
class MidiInput {
  virtual void start(MidiSink*) = 0;      // fil de séance
  virtual void stop() = 0;
  virtual DeviceInfo device() const = 0;  // nom, étendue déclarée si connue (US-ECO-02 CA6)
};
```

- Android ([ADR-0005](adr/0005-midi-midimanager-amidi.md)) : l'application ouvre le `MidiDevice` (API non dépréciées de l'API 33 : `getDevicesForTransport`, `registerDeviceCallback(transport, executor, …)`, DA-21) et le transmet ; le noyau crée l'`AMidiDevice` et lit `AMidiOutputPort_receive` (non bloquant, 0 ou 1 message par appel, codes `DATA` et `FLUSH`). Un **analyseur MIDI à états** découpe le tampon (*running status*, SysEx consommé et ignoré, messages temps réel intercalés) avant de produire les `MidiEvent` (DN-11).
- Fil MIDI : sondage toutes les 1 ms **seulement en séance MIDI**, arrêté sinon ; consommation mesurée en POC-02 à 1, 2 et 5 ms (DN-12). L'horodatage du message sert à l'analyse.
- Fermeture dans l'ordre `AMidiOutputPort_close`, `AMidiDevice_release`, puis `MidiDevice.close()` ; référence forte au `MidiDevice` gardée côté Kotlin pendant la séance (DA-21).
- Normalisation : vélocité nulle → note désactivée ; pédale (CC 64) seuil à 64 ; débranchement → relâchement synthétique des notes enfoncées.
- Appareils en paquets MIDI universels (MIDI 2.0) : exclus au MVP, inscrits au registre de la bêta (DA-21).

### 3.3 Horloges et correspondance des temps

Module : `platform::Clock`.

- `Clock` = « horloge monotone de la plateforme, en nanosecondes » ; sur Android, `CLOCK_MONOTONIC` (même base que `System.nanoTime()`, AMidi et `FrameTimeline`) ; sur iOS, temps hôte converti par la plateforme (DN-31).
- Correspondance position → temps : régression linéaire glissante alimentée par `getTimestamp` en entrée et en sortie, appelé depuis le fil d'analyse à cadence fixe ; ré-ancrée à chaque hausse de `xrunCount` et à chaque nouvelle génération de flux (DN-09).
- **Une seule référence temporelle** ([ADR-0024](adr/0024-mesure-des-latences.md), MIR-06) : l'horodatage d'entrée donne l'instant où une trame est entrée dans la chaîne audio, latence d'entrée connue du système comprise ; on **n'y ajoute pas** une latence de captation mesurée par ailleurs, sauf si le test de cohérence de POC-02 (clic émis par le haut-parleur et capté par le micro) montre un écart. Dans ce cas, on corrige d'un décalage mesuré au réglage du micro (EF-ECO-06 CA2) et l'on cesse d'utiliser l'horodatage d'entrée.
- La même correspondance convertit la position de sortie d'un son de l'application en position d'entrée (§3.5, DN-02).

### 3.4 Déroulé attendu et carte temporelle

Module : `score`.

Le modèle de partition ([07 §4](07-import-et-rendu-de-partition.md#4-modèle-interne)) est **unique** pour la lecture, l'affichage et l'analyse.

```cpp
struct ExpansionOptions { bool ignoreRepeats; MeasureRange range; HandSet hands; };
Expansion expand(const Score&, const ExpansionOptions&);      // ordre de jeu, règles de l'annexe A de PED (US-LEC-07)
ExpectedTimeline expected(const Expansion&, const TempoSettings&);  // événements attendus (EDB 06 §2.1)
TempoMap tempoMap(const Expansion&, const TempoSettings&);   // temps ↔ position musicale (fractions exactes)
```

- Positions musicales en **fractions exactes** ; instants en entiers (échantillons ou nanosecondes, `int64`).
- Une occurrence dans l'ordre de jeu est identifiée par `(identifiant de note, numéro de passage)`.
- Les mêmes fonctions servent au séquenceur, au curseur et à l'analyse.

### 3.5 Transcription micro

Module : `transcribe`. Décision proposée : [ADR-0010](adr/0010-transcription-deux-etages.md), à confirmer par POC-01.

```cpp
struct EventVerdict {                       // MIR-25
  EventId event; enum { Valide, Faux, Incertain, Rien } etat;
  Pitch hauteurJouee; float confiance; HypothesisEvidence preuves[kMaxHyp]; int32_t delaiTrames;
};
class RealtimeVerifier {                    // fil d'analyse temps réel
  void prepare(const ExpectedTimeline&, const Calibration&, const VerifierParams&);  // préallocation
  void setExpectedWindow(EventRange);        // événements attendus autour du curseur
  void process(const float* x16k, int n, StreamPos pos, VerdictSink*);  // émet EventVerdict et attaques inattendues
  void onAppSound(const RenderedSound&);     // sons réellement émis par l'application (DN-02)
};
class DeferredTranscriber {                  // fil différé ; avance pendant le jeu
  void configure(int inputRate);             // 16 000 ou 22 050 Hz selon l'approche retenue (MIR-15)
  void push(const float* x, int n, StreamPos pos);
  NoteList finish();                         // libère et efface tous les tampons audio (ADR-0013)
};
```

**Vérificateur temps réel** (proposition de `expert-audio-mir`, MIR-03, [revue §5](revues/expert-audio-mir.md#5-proposition-détaillée--étage-temps-réel-mode-attente-et-retour-en-direct)) :

1. décimation 48 → 16 kHz par un filtre à réponse impulsionnelle finie en table (MIR-14) ;
2. détection d'attaques générique et causale : transformée courte (Hann 512, pas de 128), flux spectral log-compressé avec filtre maximum en fréquence, énergie haute fréquence, seuil adaptatif ;
3. vérification déclenchée par l'attaque, par **filtres ciblés** (Goertzel) sur les partielles des hypothèses, aux fréquences fₖ = k·f0·√(1 + B·k²) (diapason, inharmonicité B et accord étiré par registre, MIR-18) ;
4. **hypothèses concurrentes** : notes attendues, voisines ±1 et ±2 demi-tons, octaves ±12, notes qui résonnent encore, sons de l'application, rien ;
5. preuve par **augmentation** d'énergie après l'attaque (une note qui résonne ne valide pas) ; règle d'octave sur les partielles **impaires** de l'octave inférieure ;
6. **test séquentiel** à fenêtres croissantes (24 à 64 ms, jusqu'à 128 ms dans le grave) ;
7. **décision asymétrique** : valider vite ; signaler une fausse note seulement avec une confiance élevée (seuil plus haut pour l'octave, et encore plus haut sous sol 2, MIR-19) ; sinon attendre (bénéfice du doute, US-ECO-07 CA4) ;
8. confiance calibrée sur le corpus de POC-01.

**Sons de l'application** (DN-02, MIR-20) : le rappel de sortie publie dans une file SPSC « sons rendus » (note, vélocité, position de sortie) ; le fil d'analyse les convertit en positions d'entrée (§3.3) et les ajoute à l'hypothèse « fond » (partielles masquées, confiance réduite pendant qu'ils sonnent). Une soustraction d'écho adaptative est une option à évaluer en POC-01 (sous-corpus « métronome par le haut-parleur »).

**Transcription différée** : candidats comparés en POC-01 ([ADR-0010](adr/0010-transcription-deux-etages.md)) ; elle ne garde que ses sorties (probabilités, notes).

**Indicateur de signal** (EF-ECO-07) : niveau, écrêtage, rapport signal sur bruit, codes de cause (US-RES-07 CA2).

### 3.6 Suivi temps réel et séance

Module : `align`, `session`.

| Méthode de `Session` | Fil appelant | Mécanisme |
|---|---|---|
| `create(config)` | Arrière-plan Kotlin | Préallocation de tout l'état temps réel |
| `start`, `pause`, `resume`, `stop` | Fil UI (via JNI) | Commande placée dans la file **UI → analyse** ; appliquée par le fil d'analyse entre deux blocs |
| `pollUiEvents(buffer)` | Fil UI, à chaque image | Copie sans allocation depuis la file **analyse → UI** |
| Point de reprise | Produit par le fil d'analyse | Instantané publié en double tampon ; lu par Kotlin sur un fil d'arrière-plan (DN-03) |
| `finish()` | Fil d'arrière-plan **uniquement** (vérifié en débogage) | Après l'arrêt du sondage ; analyse différée complète, qui fait foi en jeu continu |
| `close()` | Fil d'arrière-plan | Idempotent ; appels concurrents interdits (DN-14) |

- **Suiveur en ligne** : position dans le déroulé attendu, recalage après hésitation, arrêt ou retour en arrière (EDB 06 §3.2), règles du mode attente (US-PRA-02), à partir des `EventVerdict` ou des notes MIDI.
- **Machine à états** côté noyau : `Prêt → Décompte → EnJeu ⇄ PauseAuto → Terminée` ; pause après silence et reprise en jouant (EF-PRA-08).
- **Autre main en mode attente** (US-PRA-03 CA2) : le fil d'analyse envoie les notes à jouer au rappel de sortie par **sa propre file SPSC** ; le fil UI a la sienne (lecture, tempo, décompte) ; le rappel fusionne les deux files par position d'échantillon cible (DN-01).
- Rôle de l'analyse différée **en mode attente** (décision QO-52 du 2026-11-08, [fichier 14](14-decisions-a-prendre.md)) : le compte-rendu reprend les `EventVerdict` du temps réel ; l'analyse différée, facultative dans ce mode, peut seulement faire passer un verdict à « incertain » (hors score), jamais créer une erreur (EF-ANA-13 CA2).

### 3.7 Analyse et scores

Module : `analyse`. Entrées : déroulé attendu, notes jouées, alignement. Sorties : écarts typés avec sous-type, confiance et sévérité ; scores ([EDB 06 §5](../EDB/06-analyse-du-jeu-et-erreurs.md#5-calcul-des-scores)) ; passages prioritaires et motifs ; proportion d'écarts incertains. Instants et tolérances en **entiers**, coûts de programmation dynamique en **virgule fixe**, règle explicite de départage des égalités (DN-18) : les décisions MIDI sont identiques au bit près sur toutes les plateformes.

### 3.8 Configuration et paramètres

Toutes les valeurs [HYP] et [PO] sont dans `analysis-params.json`, versionné, embarqué dans l'APK et lu par le banc (US-ANA-07 CA1). Changer un paramètre change la **version de l'analyse** et passe par le banc. La configuration **distante** n'est pas réalisable sans serveur (INC-07) ; depuis l'EDB v0.7, ENF-MAINT-02 prévoit au MVP des paramètres embarqués et versionnés (MDAT-08, [fichier 15](15-modifications-edb-prd-proposees.md)).

### 3.9 Rendu

Module : `import` (bibliothèque `libpianoimport`). Verovio dessine directement, par `RenderToDeviceContext`, dans un **contexte de dessin du projet** qui écrit la liste d'affichage ; le SVG reste l'oracle des tests ([ADR-0007](adr/0007-affichage-liste-d-affichage-compose.md), DN-22). Le processus principal ne charge pas Verovio : il lit la liste d'affichage en cache.

### 3.10 Synthèse

Module : `synth`. Séquenceur dans le rappel de sortie : il lit une liste d'événements **préparée avant le démarrage**, les commandes des files UI et analyse (§3.6) et un facteur de tempo atomique. **Tout appel à TinySoundFont a lieu dans le rappel de sortie** (DN-21) ; voix préallouées par `tsf_set_max_voices` (au moins 256 [ARCH], selon la banque) ; `TSF_MALLOC` et `TSF_REALLOC` redirigés vers l'allocateur instrumenté (DN-05) ; épuisement des voix compté et testé (Prélude op. 28 n° 4 avec pédale). Décision proposée : [ADR-0009](adr/0009-synthese-tinysoundfont.md).

## 4. Règles temps réel

S'appliquent aux fonctions de rappel d'Oboe et à ce qu'elles appellent.

| Règle | Comment | Vérification |
|---|---|---|
| Pas d'allocation | Tampons, voix, files préalloués à la préparation | RealtimeSanitizer (§4.1) ; compteur d'allocations |
| Pas de verrou | Files SPSC sans verrou, une par producteur (DN-01) ; atomiques **sans verrou prouvé** : `static_assert(std::atomic<T>::is_always_lock_free)` (DN-04) ; instantanés de plusieurs mots (position et temps du séquenceur) par verrou de séquence ou double tampon à index atomique | RealtimeSanitizer, revue |
| Pas d'appel bloquant | Ni fichier, ni journal formaté, ni JNI | RealtimeSanitizer |
| Durée bornée | O(taille du bloc) ; aucun calcul d'analyse dans le rappel d'entrée | Traces Perfetto (POC-02) |
| Débordement | Le rappel d'entrée **rejette le bloc entrant** si l'anneau est plein et incrémente un compteur atomique ; le bloc suivant porte sa position, le fil d'analyse voit le trou et le traite comme une discontinuité (DN-06) | Test au banc |

### 4.1 Outils de contrôle

- **RealtimeSanitizer** (`-fsanitize=realtime`, Clang 20 ou plus, Linux) : rappels et fonctions appelées annotés `[[clang::nonblocking]]` ; il signale `malloc`, `free`, `pthread_mutex_lock` et les fonctions marquées bloquantes [VÉRIFIÉ par `dev-natif-cpp` le 2026-11-08, clang.llvm.org/docs/RealtimeSanitizer.html]. Il tourne dans une tâche de CI dédiée avec un Clang ≥ 20 ; cette tâche n'entre pas dans la comparaison au banc, qui utilise clang-18 ([ADR-0022](adr/0022-reproductibilite-ordinateur-telephone.md)).
- ThreadSanitizer et AddressSanitizer sur les tests du noyau.
- Dans le fil d'analyse (non temps réel), l'absence d'allocation en régime établi est souhaitable (prévisibilité de E5) : plans de transformée créés à la préparation (DN-20).

## 5. Modèle de threads

Décision : [ADR-0020](adr/0020-modele-de-threads-temps-reel.md). Fils créés et réglés par l'interface `platform::ThreadControl` (DN-13).

| Fil | Priorité | Rôle | Communique par |
|---|---|---|---|
| Rappel d'entrée audio | Temps réel (système) | Copier les blocs et leur position dans l'anneau d'entrée | Anneau SPSC → analyse |
| Rappel de sortie audio | Temps réel (système) | Séquenceur et synthèse ; publication de la position de lecture et des sons rendus | Files SPSC ← UI et ← analyse ; SPSC « sons rendus » → analyse ; instantané position/temps → UI |
| Fil MIDI (séance MIDI seulement) | Élevée | Sonder AMidi, analyser le flux, horodater | SPSC → analyse |
| Fil d'analyse temps réel (= fil de séance) | Élevée, avec **APerformanceHint** (API 33 : durée cible = période d'un bloc, `reportActualWorkDuration` à chaque bloc) [VÉRIFIÉ par `dev-natif-cpp` le 2026-11-08] | Décimation, vérificateur, suiveur, état de séance, application des commandes, `getTimestamp` périodique | SPSC → UI ; SPSC → différé ; SPSC → sortie (autre main) |
| Fil d'analyse différée | Normale | Transcription par fenêtres, puis alignement et classification complets | Résultat de `finish()` |
| Fil principal Android (UI) | Système | Sonder à chaque image, dessiner | Appels JNI non bloquants |
| Fils d'arrière-plan Kotlin | Normale | Base de données, points de reprise, `finish()` | Coroutines |
| Processus d'import | Normale | Lecture, conversion, mise en page | Fichiers et codes ([ADR-0021](adr/0021-import-en-processus-isole.md)) |

Les puits d'entrée et de sortie peuvent être appelés depuis le même fil (flux duplex, iOS) : aucun code ne suppose deux rappels distincts (DN-31). Les valeurs de priorité permises et l'effet d'APerformanceHint sur E5 et la batterie sont mesurés en POC-02.
