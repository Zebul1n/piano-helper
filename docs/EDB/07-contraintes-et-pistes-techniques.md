# 07 — Contraintes et pistes techniques

> Ce document n'est **pas** une architecture. Il recense les contraintes connues et les options techniques disponibles pour évaluer la faisabilité, les coûts et les risques. Les choix définitifs seront pris après les POC (voir [13](13-jalons-et-demarche.md)) et consignés dans des décisions d'architecture.
>
> Les bibliothèques citées et leurs licences sont à **revérifier** au moment du choix : versions, licences et maintenance évoluent.

## 1. Contraintes structurantes

| # | Contrainte | Conséquence |
|---|---|---|
| C1 | Plateformes : **Android d'abord** (téléphone et tablette), iOS à terme. | Ne pas s'enfermer dans des choix exclusivement Android pour le cœur métier (voir §14). |
| C2 | Captation audio et retour en temps réel à faible latence. | Le traitement audio doit tourner en natif (C/C++, Swift, Kotlin/NDK), pas dans une couche interprétée. |
| C3 | Fonctionnement hors ligne pour l'usage courant (lecture, pratique, analyse). | Rendu, synthèse et analyse embarqués sur l'appareil. |
| C4 | Transcription polyphonique de piano sur mobile. | Modèle d'apprentissage automatique embarqué, contrainte de taille, de calcul et de batterie. |
| C5 | Reconnaissance de partition (PDF, photo). | Traitement lourd, probablement côté serveur au départ. |
| C6 | Licences des composants compatibles avec une distribution sur les stores et un usage commercial. | Vigilance sur GPL, AGPL, LGPL (liaison statique sur iOS). Dépend de la licence du projet [QO-19]. |
| C7 | **Budget nul** : outils, bibliothèques, contenus et services gratuits et/ou open source uniquement. | Pas de SDK commercial, pas de serveur payant ; voir §14. |
| C8 | **Porteur unique** assisté d'agents IA. | Privilégier des solutions simples, éprouvées et bien documentées ; limiter le nombre de technologies. |

## 2. Formats de partition

| Format | Nature | Intérêt | Difficulté |
|---|---|---|---|
| **MusicXML** (`.musicxml`, `.xml`, `.mxl`) | Notation structurée, standard d'échange | Exporté par tous les éditeurs ; contient la notation complète (mains, doigtés, nuances) | Format riche et permissif : exports variables selon les logiciels |
| **MIDI** (`.mid`) | Performance (événements temporels) | Très répandu | Pas de notation : quantification, enharmonie (fa♯ ou sol♭ ?), séparation des mains à déduire |
| **MEI** | Notation structurée, monde académique | Riche, bien supporté par Verovio | Peu répandu chez le grand public |
| **ABC** | Texte | Simple, nombreux airs traditionnels | Peu utilisé pour le piano |
| **MuseScore** (`.mscz`) | Format propriétaire ouvert d'un éditeur libre | Énorme base de partitions en ligne | Format interne qui évolue selon les versions ; conversion fiable incertaine [QO-08] |
| **PDF / image** | Graphique | Format le plus courant chez les utilisateurs | Reconnaissance optique (OMR) nécessaire |

**Modèle interne.** Quel que soit le format source, la partition est convertie dans un modèle interne unique (voir [09](09-modele-de-donnees.md)). Piste : utiliser MusicXML ou MEI comme format pivot de stockage, plus une représentation optimisée pour l'analyse (déroulé attendu).

## 3. Rendu de la partition

| Option | Licence | Points forts | Points faibles |
|---|---|---|---|
| **Verovio** | LGPL-3.0-only ; l'œuvre combinée est diffusée sous GPL-3.0 | Moteur de gravure C++ de qualité, lit MEI, MusicXML, Humdrum, ABC ; produit du SVG et une **carte temporelle** (correspondance notes ↔ temps) utile au curseur ; utilisable en natif (C++) ou en WebAssembly | LGPL : liaison dynamique ou mise à disposition des objets à vérifier sur iOS ; rendu SVG à intégrer |
| **OpenSheetMusicDisplay** (sur VexFlow) | BSD-3 | TypeScript, MusicXML, curseur intégré, mise en page dynamique | Nécessite une vue web ; performances sur grandes partitions à vérifier |
| **VexFlow** | MIT | Bas niveau, flexible | Pas de lecteur MusicXML complet : beaucoup de travail |
| **alphaTab** | MPL-2.0 | Lecture + rendu + synthèse intégrés, lit MusicXML | Orienté guitare à l'origine ; rendu piano à évaluer |
| Moteur maison | — | Contrôle total | Coût très élevé : la gravure musicale est un problème difficile |

**À évaluer [POC-04] :** qualité sur le corpus, performances (temps de rendu d'une partition de 10 pages, fluidité du défilement), facilité de surimpression des erreurs sur les notes (identifiants de notes stables entre le modèle et le rendu).

## 4. Synthèse sonore

| Option | Licence | Remarques |
|---|---|---|
| Échantillonneur natif iOS (`AVAudioUnitSampler`) + banque de sons SoundFont | Système | Simple sur iOS ; pas d'équivalent direct sur Android |
| FluidSynth | LGPL-2.1 | Multiplateforme, éprouvé ; LGPL à gérer |
| TinySoundFont | MIT | Léger, un seul fichier C ; qualité correcte |
| Moteur d'échantillons maison | — | Contrôle de la qualité et de la taille |

Banque de sons de piano : choisir une banque sous licence compatible avec un usage commercial (certaines sont en Creative Commons avec attribution, d'autres libres de droits). Compromis taille de l'application / qualité (de 5 Mo à plusieurs centaines de Mo) : prévoir une banque légère embarquée et une banque haute qualité téléchargeable. Décision QO-10 : banque légère libre embarquée (exemple vérifié : « Upright Piano KW », CC0, variante *small* : 2,9 à 6,0 Mio selon le format ; SF2 complet : 27 Mio), banque de meilleure qualité téléchargeable plus tard ; qualité validée à l'oreille par le porteur.

## 5. Connexion MIDI

| Plateforme | API | USB | Bluetooth (BLE MIDI) |
|---|---|---|---|
| iOS / iPadOS | CoreMIDI | Oui (adaptateur USB, ou USB-C direct) | Oui, avec écran d'appairage système fourni |
| Android | `android.media.midi` (Android 6.0+) | Oui (USB OTG) | Oui, appairage à intégrer dans l'application |

Points d'attention :
- latence et gigue du BLE MIDI (horodatage des événements à exploiter) ;
- certains pianos numériques n'exposent qu'une prise MIDI DIN 5 broches : interface USB-MIDI nécessaire ;
- compatibilité à tester sur un panel de pianos numériques courants (Yamaha, Roland, Kawai, Casio).

## 6. Captation audio

### 6.1 Plateforme

| Plateforme | API | Remarques |
|---|---|---|
| iOS | AVAudioEngine / Audio Units | Latence faible et homogène entre appareils |
| Android | AAudio / Oboe (C++) | Latence très variable selon les appareils et constructeurs ; certains appareils récents offrent un mode basse latence |

### 6.2 Points critiques

- **Désactiver les traitements orientés voix** (contrôle automatique de gain, réduction de bruit, annulation d'écho « voix ») qui déforment le signal musical. Utiliser les modes de captation « mesure » ou « non traité » quand ils existent.
- **Annulation d'écho** : utile uniquement si l'application joue un accompagnement ou un métronome pendant la captation (EF-ECO-08), mais les annulateurs d'écho système sont conçus pour la voix. Piste alternative : soustraire les notes connues jouées par l'application dans l'analyse, ou recommander des écouteurs.
- **Qualité des micros** : très variable ; les téléphones ont souvent plusieurs micros, choisir le plus adapté.
- **Fréquence d'échantillonnage** : 44,1 ou 48 kHz en captation ; la plupart des modèles de transcription travaillent à 16 ou 22,05 kHz (rééchantillonnage).

## 7. Transcription audio → notes (micro)

C'est le **risque technique n° 1** [POC-01].

### 7.1 Pourquoi c'est difficile

Le piano est **polyphonique** : plusieurs notes simultanées, aux harmoniques qui se superposent (une octave partage la plupart de ses harmoniques avec la note de base). La pédale prolonge les notes. Le micro d'un téléphone capte aussi la pièce. Les algorithmes classiques de détection de hauteur (type YIN, pYIN, CREPE) sont **monophoniques** : suffisants pour une mélodie à une main sans pédale, insuffisants dans le cas général.

### 7.2 Options

| Option | Licence | Remarques |
|---|---|---|
| **Basic Pitch** (Spotify) | Apache-2.0 | Modèle léger, polyphonique, multi-instruments ; conçu pour tourner sur des appareils modestes ; précision sur le piano à mesurer : F-mesure par note publiée sur MAESTRO de 70,9 % (Onsets and Frames : 95,2 %) ; anticipation propre au modèle supérieure à 200 ms, incompatible avec le mode attente |
| **Onsets and Frames** (Google Magenta) | Apache-2.0 | Modèle spécialisé piano, référence académique ; plus lourd ; dépôt archivé |
| ***Onsets and Velocities*** (2023) | Code GPL-3.0 ; licence des poids à vérifier | Candidat léger (environ 3,1 millions de paramètres) ; F-mesure sur les attaques publiée de 96,78 % sur MAESTRO |
| Modèles de **madmom** | CC BY-NC-SA 4.0 | Exclus (clause non commerciale, voir [11 §3](11-juridique-et-conformite.md#3-licences-logicielles-et-contenus)) |
| Modèles de transcription piano « haute résolution » (recherche) | À vérifier selon le modèle | Très bonne précision sur des enregistrements studio ; taille et coût de calcul élevés pour du mobile |
| **Approche informée par la partition** | — | Vérifier la présence des notes attendues (et détecter les écarts) plutôt que transcrire à l'aveugle. Ex. : sortie probabiliste d'un modèle (« piano-roll » de probabilités) interprétée avec les notes attendues comme a priori ; ou factorisation spectrale avec des gabarits de notes. Gain de fiabilité attendu important. Un **vérificateur maison non causal** (même principe que l'étage temps réel, avec du contexte de part et d'autre de chaque attaque) est candidat pour l'étage différé. |
| Modèle maison entraîné | Selon données | Coût élevé (données, entraînement) ; à n'envisager qu'après évaluation des modèles existants |

### 7.3 Exécution sur l'appareil

Frameworks d'inférence : TensorFlow Lite (LiteRT), Core ML (iOS), ONNX Runtime Mobile. Décision QO-50 : le moteur ONNX Runtime tout fait de Microsoft (qui contient un module de télémétrie activé par défaut) est exclu des versions diffusées ; il reste permis pour les essais internes. Points à mesurer : temps d'inférence par seconde d'audio, consommation de batterie, échauffement, taille du modèle (impact sur la taille de l'application), latence induite par la fenêtre de contexte du modèle (compatible avec le temps réel ?).

Piste d'architecture à deux étages :
1. **Temps réel** : détection rapide des attaques + vérification des notes attendues à l'instant courant → mode attente, retour en direct, suivi.
2. **Différé** : transcription complète faite **pendant** la séance, par fenêtres, sur le son gardé brièvement en mémoire, sans aucun enregistrement → compte-rendu définitif du jeu continu (en mode attente, son rôle est limité par la décision QO-52, voir EF-ANA-13).

## 8. Alignement jeu ↔ partition

| Contexte | Approches connues |
|---|---|
| Différé, symbolique (MIDI ↔ partition) | Alignement par programmation dynamique (type distance d'édition enrichie du temps), modèles de Markov cachés pour l'alignement de performances |
| Différé, audio ↔ partition | DTW (déformation temporelle dynamique) entre caractéristiques audio (chroma) et partition synthétisée |
| Temps réel (suivi de partition) | DTW en ligne (OLTW), modèles probabilistes de suivi (HMM, filtres particulaires) |

Exigences à satisfaire (voir [06](06-analyse-du-jeu-et-erreurs.md)) : robustesse aux erreurs locales, aux hésitations, aux retours en arrière, aux sauts de reprises.

## 9. Reconnaissance optique de partition (OMR)

**Risque technique n° 2** [POC-03].

| Option | Licence | Remarques |
|---|---|---|
| **Audiveris** | AGPL-3.0 | Java, mature, bonne qualité sur partitions imprimées propres ; l'AGPL impose de publier le code source du service si on l'utilise côté serveur et qu'on le modifie : à étudier juridiquement |
| **oemer** | MIT | Apprentissage profond, Python, plus robuste aux photos ; qualité à évaluer |
| Solutions commerciales sous licence | Payant | À identifier et évaluer (SDK, API) |
| Service OMR maison | — | Très coûteux |

Les partitions de piano sont parmi les plus difficiles pour l'OMR (deux portées, voix multiples, accords denses, croisements). La correction manuelle (EF-IMP-08) est donc indispensable.

Exécution probable **côté serveur** au départ (modèles lourds) → nécessite un backend, des coûts d'infrastructure, et un traitement de données utilisateur (voir [11](11-juridique-et-conformite.md)).

## 10. Technologie de l'application

| Option | Pour | Contre |
|---|---|---|
| Natif (Swift + Kotlin) | Accès direct et optimal à l'audio et au MIDI | Deux bases de code pour l'interface |
| **Noyau C++ partagé + interface native** | Analyse, audio, rendu (Verovio) partagés ; interface optimale par plateforme | Deux interfaces |
| **Noyau C++ partagé + interface multiplateforme** (Flutter, React Native, Kotlin Multiplatform / Compose Multiplatform) | Une seule interface, noyau critique en natif | Pont entre l'interface et le noyau à soigner (performances du curseur, rendu de partition) |
| Interface web embarquée (vue web) | Réutilise les bibliothèques JS de rendu (OSMD, Verovio WASM) | Performances, intégration audio, impression « pas native » |

**Recommandation provisoire :** isoler un **noyau natif partagé** (captation, synthèse, transcription, alignement, analyse) indépendant de l'interface. Le choix de la technologie d'interface se fera après le POC de rendu [POC-04] et le POC de latence [POC-02].

## 11. Backend

Le principe est **« local d'abord »** : l'application fonctionne sans serveur pour l'usage courant. Un backend est nécessaire pour :

| Besoin | Version |
|---|---|
| Reconnaissance de partition (si traitée côté serveur) | V1 |
| Comptes, sauvegarde | V1 |
| Synchronisation multi-appareils | V2 |
| Espace professeur, partage | V2 |
| Gestion des abonnements (validation des reçus) | Selon [QO-01] |
| Télémétrie de qualité (anonyme, avec consentement) | V1 |

## 12. Récapitulatif des risques techniques

| Risque | Gravité | Probabilité | Levée par |
|---|---|---|---|
| Transcription micro insuffisante (fausses erreurs) | Critique | Élevée | POC-01 |
| Latence audio trop élevée sur Android d'entrée de gamme | Élevée | Moyenne | POC-02 |
| OMR insuffisante sur partitions de piano | Élevée | Élevée | POC-03 |
| Rendu de partition lent ou imprécis | Moyenne | Faible | POC-04 |
| Alignement instable sur jeu hésitant | Élevée | Moyenne | POC-05 |
| Licences incompatibles (LGPL/AGPL) | Moyenne | Moyenne | Revue juridique |
| Taille de l'application (modèles + sons) | Moyenne | Moyenne | Téléchargement à la demande |

## 13. Android : fonctionnalités par version

Ce qui compte pour le projet : l'audio basse latence, la qualité de la captation micro, le MIDI (USB et Bluetooth) et l'accès direct depuis le code natif.

| Version (API) | Apport utile au projet | Fonctionnalités concernées |
|---|---|---|
| 6.0 (23) | API MIDI `android.media.midi` (Java/Kotlin) : claviers USB et Bluetooth MIDI. Indicateur « pro audio » déclaré par les appareils qui garantissent une faible latence. | EF-ECO-02, EF-ECO-03 |
| 7.0 (24) | Source audio `UNPROCESSED` : captation sans les traitements prévus pour la voix (si l'appareil la prend en charge). Mode « performance soutenue » contre la baisse de régime due à l'échauffement. | EF-ECO-04, ENF-RES-02 |
| 8.0 (26) | AAudio : API audio native (C) à faible latence. | ENF-PERF-01 à 03 |
| 8.1 (27) | AAudio fiabilisé, chemin direct (MMAP) sur les appareils compatibles. La bibliothèque Oboe utilise AAudio à partir de cette version, OpenSL ES avant. | ENF-PERF-01 à 03 |
| 9 (28) | `MicrophoneInfo` : description des micros de l'appareil (position, directivité). | Choix du meilleur micro (EF-ECO-04) |
| **10 (29)** | **AMidi** : API MIDI native (C/C++). Les **messages** MIDI arrivent directement dans le noyau natif, sans passer par la couche Kotlin ; la découverte et l'ouverture de l'appareil passent par `MidiManager` (Kotlin). Choix de la direction de micro préférée. | EF-ECO-02, ENF-PERF-01, architecture du noyau |
| 12 (31) | Permissions Bluetooth dédiées (`BLUETOOTH_SCAN`, `BLUETOOTH_CONNECT`) : plus besoin de la permission de localisation pour appairer un clavier Bluetooth MIDI. « Classe de performance média » qui identifie les appareils performants. | EF-ECO-03 (parcours moins intrusif), adaptation de l'analyse à la puissance de l'appareil |
| **13 (33)** | MIDI 2.0 en USB. Sélecteur de photos système natif. Permission explicite pour les notifications. Choix de la langue par application dans les réglages du système. | Évolutions futures ; EF-IMP-04 ; EF-PRO-05 ; EF-CPT-05 |
| 15 (35) | Périphériques MIDI 2.0 virtuels. | Sans objet pour le besoin actuel |

Points importants :
- **La qualité audio réelle dépend davantage du matériel et du constructeur que de la version d'Android.** Deux appareils sous la même version peuvent avoir des latences très différentes : seule la mesure sur appareil [POC-02] fait foi.
- Les fonctionnalités récentes peuvent être **activées à l'exécution** selon la version de l'appareil (ex. parcours Bluetooth simplifié à partir d'Android 12), sans relever la version minimale.
- La **version cible** (`targetSdk`) est imposée par Google Play (relevée chaque année) ; seule la **version minimale** (`minSdk`) est un choix du projet.

### Décision : Android 13 (API 33) minimum

Décidé le 2026-11-07 (QO-09). Toutes les fonctionnalités des versions 6 à 13 du tableau ci-dessus sont donc disponibles sans code de compatibilité :

| Apport | Conséquence |
|---|---|
| AAudio, captation `UNPROCESSED`, AMidi | Audio et MIDI basse latence directement dans le noyau natif, sans chemin de repli |
| Permissions Bluetooth dédiées | Appairage Bluetooth MIDI sans permission de localisation, sur tous les appareils |
| Sélecteur de photos, permission de notification, langue par application | Un seul comportement à développer et tester |
| Appareils plus récents | Plus de puissance pour la transcription et l'analyse en temps réel |

Contrepartie : un parc d'appareils couvert plus restreint qu'avec Android 10 (proportion à vérifier sur le tableau de distribution d'Android Studio). Ce choix pourra être revu à la baisse avant la V1 si la cible l'exige.

## 14. Conséquences des décisions de cadrage

### 14.1 Android d'abord, iOS à terme

- **Noyau métier portable** : captation, synthèse, transcription, alignement et analyse en C++ (ou au minimum isolés derrière une interface), réutilisables sur iOS.
- **Interface** : Kotlin + Jetpack Compose pour Android. Compose Multiplatform / Kotlin Multiplatform permettraient de partager l'interface avec iOS plus tard : à évaluer dans le DAT.
- **iOS et budget nul** : compiler une application iOS exige un Mac (Xcode), et la publier sur l'App Store exige l'adhésion payante au programme développeur Apple (abonnement annuel). Le portage iOS n'est donc pas compatible avec un budget strictement nul en l'état.

### 14.2 Budget nul

| Domaine | Conséquence |
|---|---|
| Développement | Android Studio, Kotlin, NDK, CMake, Git : gratuits. Décision QO-48 : sur l'ordinateur du porteur, WSL 2 avec Ubuntu 24.04 (Python, compilateur C++, outils de licence) et Android Studio. |
| Hébergement du code et CI | Décision QO-45 : **GitHub** (code, vérifications automatiques, page de publication), gratuit pour un dépôt public, avec trois précautions : machines de vérification d'une version figée (Ubuntu 24.04), clé de signature dans un espace protégé qui exige l'accord du porteur, copie de secours du code. |
| Distribution | Google Play demande une inscription développeur payante (paiement unique). Alternatives gratuites : APK sur la page de publication du dépôt (GitHub Releases), F-Droid (exige une application entièrement libre, sans dépendance propriétaire). Décision QO-20 : APK sur la page de publication du dépôt pour l'alpha et la bêta, avec le compte gratuit de « distribution limitée » de Google (20 appareils ; vérification des développeurs annoncée pour 2027 ; compte de diffusion complète à 25 USD, question QO-54 ; voir [11 §4](11-juridique-et-conformite.md#4-distribution-et-commerce)) ; canal public choisi avant la V1. |
| Serveur | Aucun serveur payant : renforce le principe « local d'abord ». OMR sur l'appareil, ou via un outil open source exécuté sur l'ordinateur de l'utilisateur ; comptes et synchronisation reportés ou appuyés sur des offres gratuites (avec leurs limites). |
| Composants | Exclusivement open source ou gratuits sous licence compatible : Oboe (Apache-2.0), Verovio (LGPL-3.0-only), modèles de transcription Apache-2.0, banques de sons libres. Aucun SDK commercial. |
| Tests | Parc d'appareils limité au matériel disponible et à des testeurs bénévoles [QO-21]. |

### 14.3 Matériel de test disponible

Un téléphone Android 14, l'émulateur Android, un piano droit acoustique. Pas de piano numérique.

| Usage | Téléphone Android 14 | Émulateur | Commentaire |
|---|:-:|:-:|---|
| Développement et tests de l'interface | ● | ● | L'émulateur suffit, y compris en format tablette |
| Rendu de partition, lecture audio | ● | ● | Performances à mesurer sur le téléphone |
| Mesure de latence (POC-02) | ● | — | L'émulateur passe par l'audio de l'ordinateur : mesures non représentatives |
| Captation micro du piano droit (POC-01) | ● | — | À faire sur le téléphone posé sur le pupitre |
| MIDI réel (USB / Bluetooth) | — | — | Pas de piano numérique : impossible en conditions réelles |
| MIDI simulé | ● | ● | Fichiers MIDI rejoués comme s'ils étaient joués, périphérique MIDI virtuel, clavier visuel à l'écran |
| Tests automatisés du noyau d'analyse | — | — | Sur l'ordinateur et en CI, à partir de fichiers (MIDI, audio enregistrés) |

Conséquences :
- **Le micro devient le chemin principal pour le porteur** : c'est la seule façon pour lui d'utiliser l'application avec son piano. D'où la décision QO-22 de faire entrer la captation micro dans le MVP.
- **Le chemin MIDI reste utile** pour développer et tester le moteur d'analyse de façon déterministe (entrées exactes), indépendamment de la qualité de la transcription audio.
- **Vérité terrain pour le corpus** : sans piano numérique, les enregistrements du piano droit doivent être annotés autrement (voir [06 §9.1](06-analyse-du-jeu-et-erreurs.md#91-corpus-de-test)).
