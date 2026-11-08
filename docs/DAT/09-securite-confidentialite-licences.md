# 09 — Sécurité, confidentialité, licences

## 1. Permissions et absence de réseau

| Mesure | Mise en œuvre | Contrôle | Exigences |
|---|---|---|---|
| Pas de permission INTERNET | Aucune déclaration ; `tools:node="remove"` sur `INTERNET` et `ACCESS_NETWORK_STATE` | Script de CI sur l'APK final (`aapt2 dump permissions`) | ENF-CONF-03, US-CNF-01 CA2 |
| Liste blanche et liste noire (DA-24) | Par variante : (a) permissions système demandées : `RECORD_AUDIO` seulement ; (b) permissions de signature propres à l'application (`<applicationId>.*`, ajoutées par AndroidX ou par `:tools`), acceptées par motif. Liste noire explicite : `INTERNET`, `ACCESS_NETWORK_STATE`, `FOREGROUND_SERVICE*`, `POST_NOTIFICATIONS`, `READ_*`, `BLUETOOTH*` | Même script | US-ECO-03 CA1 |
| Pas de WebView, de services Google, ni de télémétrie | Règle de revue ; Licensee ; **recherche de chaînes de télémétrie dans les `.so` et le code compilé** (par exemple `events.data.microsoft.com`, `OneCollector`) (JUR-D01) | CI | US-CNF-01 CA4, ENF-CONF-02 |
| Micro seulement en séance et au réglage | Flux d'entrée lié à l'état de séance | Test instrumenté | ENF-SEC-06 |
| Composants exportés | Activité principale seulement ; service d'import et `FileProvider` non exportés | Revue du manifeste | — |

**ONNX Runtime officiel** (JUR-D01, **bloquant si utilisé dans une version diffusée**) : l'AAR de Maven Central intègre le kit de télémétrie de Microsoft, activé par défaut, qui crée un identifiant d'appareil persistant et stocke des événements dans une base SQLite locale ; seule `ORT_DISABLE_TELEMETRY=1`, définie avant l'initialisation, l'empêche [VÉRIFIÉ par `juriste-conformite` le 2026-11-07 : `docs/Privacy.md` de la v1.31.1 et inspection de l'AAR]. Sans permission INTERNET, rien ne part, mais la règle « pas de télémétrie » est enfreinte et l'écriture d'un identifiant sur l'appareil relève du consentement (loi Informatique et Libertés, art. 82 [interprétation de JUR]). Conséquence : **l'AAR officiel n'est admis que dans le prototype de POC-01 et la variante interne** ; dans ce cas, `Os.setenv("ORT_DISABLE_TELEMETRY", "1", true)` avant tout chargement, et un test instrumenté échoue si un fichier ou une base inconnus apparaissent dans les données de l'application après une inférence. Décision du porteur du 2026-11-08 (QO-50, [fichier 14](14-decisions-a-prendre.md)) : l'AAR officiel est exclu des versions diffusées.

ENF-SEC-01 à 03 et 05 sont sans objet au MVP.

## 2. Fichiers importés non fiables

Menaces : XML piégé, archive `.mxl` piégée (bombe de décompression, chemins `../`), fichier tronqué ou trompeur, MIDI malformé, manque de mémoire ou plantage natif dans un moteur tiers.

| Défense | Détail |
|---|---|
| Limites | Fichier ≤ 20 Mio (compté pendant la copie de transit), archive décompressée ≤ 64 Mio, ≤ 1 000 entrées [ARCH, figées dans US-IMP-06 CA2 par le PRD v0.4] |
| Archive | Répertoire central lu avant extraction ; tailles réellement produites contrôlées en flux ; chemins absolus ou contenant `..` ignorés |
| XML | pugixml n'exploite pas la DTD (« entities declared in DOCTYPE are not expanded ») [VÉRIFIÉ 2026-11-07] ; la non-résolution des entités externes en est une **déduction** |
| MIDI | Longueurs vérifiées, nombre de pistes et d'événements bornés |
| Bornes internes | Les lecteurs comptent leur mémoire et acceptent une annulation coopérative ; aucun `abort` (DN-30) |
| Processus d'import | `android:process=":import"` : **processus privé, même UID** : il confine les **plantages** et le manque de mémoire, **pas** un code natif compromis, qui pourrait lire les fichiers de l'application ; `android:isolatedProcess="true"` (processus sans permission, entrées et sorties par descripteurs ouverts par le processus principal) est étudié en POC-04 (DA-16, [ADR-0021](adr/0021-import-en-processus-isole.md)) |
| Fuzzing | libFuzzer sur les lecteurs |

## 3. Confidentialité

| Sujet | Décision | Exigences |
|---|---|---|
| Son du micro | En mémoire vive seulement, tampons courts, effacés par une écriture que l'optimiseur ne peut pas supprimer, à la fin de l'analyse différée ; **l'application n'écrit jamais le son dans un fichier** dans les variantes bêta et publique ([ADR-0013](adr/0013-son-en-memoire-uniquement.md)) | ENF-CONF-01, US-ECO-04 CA4, JUR-06 |
| Sauvegarde Android | Désactivée ([06 §8](06-donnees-et-stockage.md#8-sauvegarde-android-désactivée)) | QO-36 |
| Rapport de test | Généré à la demande, contenu limité, lisible avant l'envoi | ENF-MAINT-04 |
| Journaux | Ni son, ni titre, ni nom de fichier, ni identifiant d'appareil dans les variantes bêta et publique | RGPD-02 |
| Enregistreur de séance | Variante interne seulement ; absent des `.so` diffusés (symboles contrôlés, DA-23) | D8 |

**Mémoire placée sur le stockage par le système** (JUR-D10). Android compresse la mémoire en mémoire vive (zram) ; AOSP prévoit aussi une réécriture de zram dans `/data/per_boot/zram_swap`, dossier **chiffré** et **supprimé à chaque démarrage** [VÉRIFIÉ par `juriste-conformite`, `init.rc` d'AOSP] ; la situation des constructeurs (« RAM Plus ») est incertaine. Des pages contenant quelques secondes de son peuvent donc être écrites temporairement par le système, chiffrées, inaccessibles aux applications. Le texte « jamais enregistré dans un fichier » reste **exact et loyal** [interprétation de JUR] ; c'est la formulation de l'ADR-0013 qui a été corrigée. Une phrase facultative peut figurer dans `PRIVACY.md`, rubrique « détails techniques ».

## 4. Intégrité de la chaîne de construction

- Versions figées : catalogue de versions et vérification des dépendances (`verification-metadata.xml`, produit par la CI puis complété sur le poste, DEV-11) ; `distributionSha256Sum` et validation du wrapper Gradle.
- Sources tierces C++ copiées dans `third_party/` avec leur licence ([ADR-0016](adr/0016-construction-et-dependances.md)).
- Clé de diffusion dans un environnement GitHub `release` protégé (étiquettes `v*`, relecteur requis = le porteur), empreinte SHA-256 du certificat publiée (DEV-09, [10 §3](10-build-ci-et-distribution.md#3-variantes-et-signature)).
- Branche principale protégée : PR obligatoire, contrôles requis, ni `force push`, ni suppression d'étiquette `v*` (DEV-18).
- Empreintes SHA-256 des APK publiées ; diffusions immuables.

## 5. Inventaire des composants et compatibilité

Critères ([EDB 11 §3](../EDB/11-juridique-et-conformite.md#3-licences-logicielles-et-contenus)) : **code** compatible GPL-3.0-or-later ; **contenus** sous licence libre sans NC ni ND. Licences lues à la source le 2026-11-07, complétées par la revue de `juriste-conformite` (2026-11-07 et 2026-11-08). La colonne « Mention à reproduire » est reprise mot pour mot par l'écran des licences (JUR-D08).

### 5.1 Composants retenus (embarqués dans l'APK)

| Composant | Version | Licence | Compatibilité | Mention à reproduire |
|---|---|---|---|---|
| Kotlin (bibliothèque standard), `kotlinx.coroutines` | 2.4.21 | Apache-2.0 | Oui | Texte de la licence ; NOTICE s'il existe |
| Jetpack Compose, AndroidX (Room 2.8.5, Lifecycle, Navigation), Material | BOM 2026.09.00 | Apache-2.0 | Oui | Texte de la licence et NOTICE (JUR-01) |
| kotlinx.serialization | 1.12.0 | Apache-2.0 | Oui | Texte de la licence |
| SQLite | Système (Android) | Domaine public | Oui | — ; si Room utilise un jour son pilote SQLite embarqué, SQLite devient un composant embarqué (JUR-D09) |
| Oboe (et son rééchantillonneur) | 1.11.0 | Apache-2.0 | Oui | Texte de la licence (pas de NOTICE, [VÉRIFIÉ par JUR]) |
| Verovio | 6.4.1 | **LGPL-3.0-only** | Oui : la LGPL-3.0 est la GPL-3.0 plus des permissions ; œuvre combinée diffusée sous GPL-3.0 ; LGPL §4 d) 0) satisfaite par la publication des sources complètes (liaison statique et LTO comprises) ; informations d'installation non dues (JUR-D04) | Avis visible « Ce programme utilise Verovio, © les auteurs de Verovio (RISM Digital Center), sous licence LGPL-3.0-only », textes **GPL-3.0 et LGPL-3.0** consultables hors ligne |
| Inclus dans Verovio : pugixml 1.16, miniz-cpp avec **miniz 2.2.0**, humlib, jsonxx, MidiFile, tuning-library, crc | Copie de Verovio 6.4.1 | MIT ; MIT (miniz 2.2.0 : Unlicense / MIT, à préciser à la lecture du fichier) ; BSD-2-Clause ; MIT ; BSD-2-Clause ; MIT ; domaine public | Oui | Copyright et licence de chacun ; clause binaire BSD pour humlib et MidiFile (JUR-D05, JUR-D08) |
| Polices Leipzig et Bravura (fichiers de Verovio) | Versions de Verovio 6.4.1 | OFL-1.1, noms réservés « Leipzig » et « Bravura » | Contenu libre ; fichiers déjà convertis par leurs titulaires, embarqués **sans modification** (JUR-D06, DN-24) | `LICENSE.txt` et `FONTLOG.txt` de chaque police |
| Police de texte (Compose) | TTF d'origine d'une version publiée de Liberation 2.x, ou autre police OFL vérifiée | OFL-1.1, noms réservés Liberation, Arimo, Tinos, Cousine (JUR-D07) | Contenu libre | Licence OFL complète |
| TinySoundFont | Dernier commit 2026-07-19 | MIT | Oui | Deux copyrights : Bernhard Schelling ; « Based on SFZero, Copyright (C) 2012 Steve Folta » (JUR-D08) |
| Banque « Upright Piano KW » (variante small) | 2019-07-03 | CC0-1.0 | Contenu libre | Attribution de courtoisie |
| Modèle de transcription (si POC-01 en retient un) | Selon POC-01 | Basic Pitch : Apache-2.0 (code et poids) | Oui | Basic Pitch : fichier **NOTICE** (« Copyright 2022 Spotify AB », et mentions pour librosa, mir_eval, numpy, pretty-midi, resampy) (JUR-D08) ; données d'entraînement NC : risque résiduel de QO-41 |
| ONNX Runtime | **Exclu des versions diffusées** (décision QO-50) ; si une version construite par le projet est retenue : `LICENSE` et `ThirdPartyNotices.txt` de la version utilisée copiés dans `third_party/onnxruntime/`, une ligne par composant inclus (protobuf, abseil, **Eigen sous MPL-2.0** avec l'adresse de ses sources, XNNPACK, re2, flatbuffers, cpuinfo, ONNX…) (JUR-D02) | MIT et composants inclus | Oui | `ThirdPartyNotices.txt` complet |
| nlohmann/json | 3.12.0 | MIT | Oui | Copyright et licence |
| miniz (noyau) | 3.1.2 | MIT | Oui | Copyright et licence ; le projet aura deux miniz (2.2.0 dans Verovio, 3.1.2 dans le noyau) |
| pocketfft (branche C++) | Dernier commit 2026-10-18 | BSD-3-Clause | Oui | Copyright et clause binaire |
| dr_wav, dr_flac | 0.14.6 / 0.13.4 | Unlicense OR MIT-0 | Oui | — (variante interne seulement) |
| AboutLibraries | 15.3.0 | Apache-2.0 | Oui | Texte de la licence ; `offlineMode = true` **à activer explicitement** (désactivé par défaut) ; aucune dépendance aux services Google dans son catalogue [VÉRIFIÉ par JUR], à confirmer en I0 par `gradle dependencies` (JUR-D09) |

### 5.2 Outils de construction et de test (non embarqués)

| Outil | Version | Licence |
|---|---|---|
| GoogleTest | 1.18.0 | BSD-3-Clause |
| `reuse` (FSFE), action `fsfe/reuse-action@v6` | 6.3.0 | GPL-3.0-or-later |
| Licensee | 1.14.1 | Apache-2.0 |
| CycloneDX Gradle (facultatif, DEV-05) | 3.5.1 | Apache-2.0 |
| ktlint | 1.8.0 | MIT |
| detekt | **Non utilisé au MVP** (1.23.8 construit pour Kotlin 2.0.21, probablement incompatible avec AGP 9 ; réintroduit à sa 2.0 stable, DEV-04) | Apache-2.0 |
| android-emulator-runner | 2.38.0 | Apache-2.0 |
| mir_eval (POC-01) | 0.8.2 | MIT |
| parangonar, partitura (POC-05, facultatif) | 3.4.1 / 1.9.0 | Apache-2.0 |
| OboeTester (POC-02) | Avec Oboe 1.11.0 | Apache-2.0 |
| Modèle de Kong et al. (POC-01, ordinateur seulement) | 0.0.6 | Code « MIT » déclaré dans les métadonnées ; poids CC-BY-4.0 ; usage de ses sorties comme annotations à confirmer par JUR (MIR-09) |

### 5.3 Composants comparés et non retenus

Aucun fichier de ces composants n'entre dans le dépôt ; usage local seulement (JUR-D09).

| Composant | Licence | Raison |
|---|---|---|
| FluidSynth 2.6.2 | LGPL-2.1-or-later | Repli de [ADR-0009](adr/0009-synthese-tinysoundfont.md) |
| OpenSheetMusicDisplay 2.2.1, VexFlow 5.0.0 | BSD-3-Clause, MIT | Vue web ou lecteur incomplet |
| alphaTab 1.9.0 | MPL-2.0 | Repli de POC-04 |
| AndroidSVG 1.4 | Apache-2.0 | Inactif depuis 2019 ; inutile avec l'option D d'ADR-0007 |
| LiteRT 2.3.2 | Apache-2.0 | Voie C Android par les services Google Play |
| ONNX Runtime, AAR officiel | MIT | Télémétrie (JUR-D01), 34 Mo par ABI (DN-26), aucune mention des composants inclus (JUR-D02) : prototype et variante interne seulement |
| Onsets and Frames | Apache-2.0 (code) | Dépôt archivé |
| *Onsets and Velocities* | Code GPL-3.0 (fichier LICENSE, selon `expert-audio-mir`) ; licence des poids non indiquée [NON VÉRIFIÉ] | Candidat D de POC-01, sous réserve de la licence des poids (à vérifier par JUR) |
| Modèles de madmom | CC BY-NC-SA 4.0 | Exclus (NC) |
| KissFFT, PFFFT, libsamplerate | BSD-3-Clause ; licence de type BSD ; BSD-2-Clause | Équivalents ou inutiles |
| Salamander Grand Piano | CC-BY-3.0 | Banque téléchargeable future |
| Catch2 | BSL-1.0 | GoogleTest retenu |

**F-Droid** (JUR-D12) : la politique d'inclusion dit à la fois « All binary dependencies including JAR files must originate either from source compilation or Debian repository downloads », mais aussi « Prebuilt binaries should only come from authorized trusted sources » (Maven Central inclus, s'ils sont « freely licensed ») ; les traceurs propriétaires sont « strictly forbidden » [VÉRIFIÉ par JUR le 2026-11-08].

## 6. REUSE et écran des licences

Décision : [ADR-0018](adr/0018-conformite-des-licences.md).

| Mécanisme | Rôle |
|---|---|
| REUSE 3.3 : en-têtes SPDX, `REUSE.toml`, `LICENSES/` (dont `LGPL-3.0-only.txt`) | Licence de chaque fichier. Annotations : `third_party/verovio/**` en `LGPL-3.0-only`, titulaire « Verovio authors and contributors (RISM Digital Center) » ; annotations particulières pour `include/{crc,hum,json,midi,zip,tuning-library}` et pour `data/` (OFL-1.1) ; `precedence = "closest"` pour que les en-têtes SPDX existants l'emportent (JUR-D05) |
| `reuse lint` en CI | Échec si un fichier n'est pas couvert |
| Licensee | Liste blanche des licences des dépendances Gradle. **Angle mort** : il ne voit pas le contenu natif d'un AAR (JUR-D02) |
| AboutLibraries (`offlineMode = true`) + entrées manuelles tirées de `REUSE.toml` | Écran « À propos » : textes intégraux, NOTICE, attributions, avis de Verovio |
| Nomenclature **obligatoire** : `reuse spdx` (tous les fichiers, `third_party/` compris) ; CycloneDX facultatif et non bloquant (`includeConfigs` sur `betaReleaseRuntimeClasspath`), gardé seulement s'il produit une liste non vide (DEV-05) | Jointe à chaque diffusion |

## 7. Points à faire valider par `juriste-conformite`

Réponses reçues en v0.2 ([revue](revues/juriste-conformite.md)) et points restants :

| Point | Réponse ou état |
|---|---|
| Verovio LGPL-3.0-only | Conforme, avec les mentions de la LGPL §4 a) à c) (§5.1) |
| Sources d'ONNX Runtime | Partie des sources correspondantes ; sans objet si ONNX Runtime n'est pas diffusé (QO-50) ; sinon archive des sources de la version et de ses dépendances jointe à chaque diffusion (JUR-D03) |
| Polices à nom réservé | Fichiers de Verovio à l'identique ; réduction seulement avec autorisation écrite de RISM, demandée si la taille l'exige (décision QO-53) |
| Mémoire sur stockage | Formulation de l'ADR-0013 corrigée (§3) |
| iOS et App Store | Aucune permission supplémentaire GPL §7 pour l'instant (décision QO-51, option B) ; question reposée avant la première contribution extérieure ; avis d'avocat au portage |
| Restent à vérifier | Licence des poids d'*Onsets and Velocities* ; usage des sorties du modèle de Kong et al. comme annotations du corpus (MIR-09) |
