# 01 — Contexte et contraintes

## 1. Contexte

Piano Helper est une application Android d'aide à l'apprentissage du piano : elle charge une partition, la montre et la joue, écoute le pianiste (micro ou MIDI) et lui montre ses erreurs ([EDB README](../EDB/README.md)). Le cœur du produit est l'analyse du jeu ([EDB 06](../EDB/06-analyse-du-jeu-et-erreurs.md)), avec une règle directrice : **une fausse erreur coûte plus cher qu'une erreur non signalée**.

Le MVP est découpé en incréments I0 à I6 ([PRD 05](../PRD/05-plan-de-release.md)) ; les risques techniques sont levés par cinq preuves de concept, POC-01 à POC-05 ([EDB 13](../EDB/13-jalons-et-demarche.md)). Ce DAT sert de base aux POC et aux incréments : la définition de prêt exige que les décisions d'architecture nécessaires soient consignées en ADR (R9, [PRD 07](../PRD/07-definition-of-ready-done.md)).

## 2. Cadre non négociable

| # | Contrainte | Source | Conséquence d'architecture |
|---|---|---|---|
| K1 | Android uniquement au MVP, **minSdk 33** (Android 13) ; fonctions plus récentes activées à l'exécution | QO-02, QO-09, ENF-COMP-02 | AAudio, AMidi, captation `UNPROCESSED`, `Choreographer.postVsyncCallback` disponibles sans code de compatibilité ([ADR-0004](adr/0004-moteur-audio-oboe.md), [ADR-0005](adr/0005-midi-midimanager-amidi.md)) |
| K2 | iOS à terme : **noyau métier portable** | ENF-COMP-01, [EDB 07 §14](../EDB/07-contraintes-et-pistes-techniques.md#14-conséquences-des-décisions-de-cadrage) | Noyau C++ sans dépendance Android ([ADR-0001](adr/0001-decoupage-application-noyau-outils.md), [11](11-portabilite-ios.md)) |
| K3 | Open source **GPL-3.0-or-later** | QO-19 | Composants : licence compatible GPL-3.0-or-later ; contenus : licence libre sans NC ni ND ([EDB 11 §3](../EDB/11-juridique-et-conformite.md#3-licences-logicielles-et-contenus)) |
| K4 | **Budget nul** : composants et services gratuits ou libres | QO-14, C7 | Aucun SDK commercial ; CI gratuite ; aucun serveur |
| K5 | **Aucun serveur au MVP**, local d'abord, **pas de permission INTERNET** | ENF-OFF-01, ENF-CONF-03 | Tout le calcul sur l'appareil ; contrôle automatique du manifeste ([09](09-securite-confidentialite-licences.md)) |
| K6 | **Porteur unique** assisté d'agents | QO-14, R-14 | Peu de technologies (Kotlin, C++, CMake, Gradle ; Python seulement hors application), bibliothèques éprouvées, contrôles automatiques plutôt que vigilance humaine |
| K7 | Matériel : **un téléphone Android 14**, l'émulateur, un **piano droit acoustique**, pas de piano numérique, pas de tablette | QO-21 | Micro = chemin principal du porteur ; MIDI testé par simulation ([ADR-0005](adr/0005-midi-midimanager-amidi.md)) ; mesures valables pour un seul appareil (R-15) |
| K8 | Poste du porteur : Windows, Node.js 24 et bash installés, **Python non installé** | Consigne du porteur | Outillage principal en C++ (banc) et Node (scripts de documentation, contrôles de l'APK, prototype HTML de QO-27) ; Python seulement pour les prototypes des POC, dans WSL 2 (décision QO-48 du 2026-11-08 ; installation à faire par le porteur) |
| K9 | Sauvegarde automatique d'Android **désactivée** | QO-36, ENF-CONF-01 | `allowBackup="false"` et `dataExtractionRules` qui excluent tout ([06 §8](06-donnees-et-stockage.md#8-sauvegarde-android-désactivée)) |
| K10 | Nom technique définitif et **clé de diffusion pérenne dès I0** ; sources de chaque version diffusée disponibles (étiquette git) | QO-35, US-CNF-04, JUR-03 | [ADR-0017](adr/0017-integration-continue-et-diffusion.md), [10](10-build-ci-et-distribution.md) |

## 3. Contraintes techniques déduites

| # | Contrainte | Origine |
|---|---|---|
| C-T1 | Le traitement audio tourne en natif, sans allocation ni verrou dans les fonctions de rappel audio | ENF-PERF-01 à 03, [EDB 07 §1](../EDB/07-contraintes-et-pistes-techniques.md#1-contraintes-structurantes) (C2) |
| C-T2 | Le même code d'analyse s'exécute sur le téléphone et sur un ordinateur (banc, CI) | ENF-MAINT-01, ENF-QA-02, US-QUA-02 |
| C-T3 | Sorties **identiques au bit près** entre ordinateur et téléphone pour le code du projet ; tolérance bornée pour les sorties d'un moteur d'inférence tiers ([ADR-0022](adr/0022-reproductibilite-ordinateur-telephone.md), [08 §4](08-strategie-de-test-et-banc-d-evaluation.md#4-déterminisme-et-équivalence-ordinateur--téléphone)) | [EDB 06 §9.3](../EDB/06-analyse-du-jeu-et-erreurs.md#93-règle-de-non-régression), US-QUA-02 CA3 et CA5 |
| C-T4 | Aucun son écrit dans le stockage dans les variantes bêta et publique | US-ECO-04 CA4, US-ANA-02 CA4, D8, JUR-06 |
| C-T5 | Fichiers importés traités comme non fiables | ENF-SEC-04, US-IMP-06 |
| C-T6 | Bibliothèques natives alignées sur des pages de 16 Ko (exigence Google Play pour les applications qui ciblent l'API 35 ou plus ; mises à jour bloquées à partir du 2027-02-01) [VÉRIFIÉ 2026-11-07, developer.android.com/guide/practices/page-sizes] | Canal public futur (QO-20) |
| C-T7 | Code source complet de chaque APK diffusé, y compris les bibliothèques natives compilées | GPL-3.0 §6, JUR-03, US-CNF-04 CA3 |
| C-T8 | Variantes de construction **interne / bêta / publique** ; outils de test absents des deux dernières, vérifié sur l'APK | US-QUA-03 CA4, US-QUA-05 CA2, QA-57 |

## 4. Exigences architecturalement significatives

Exigences de l'EDB qui pèsent sur la structure du système, avec l'endroit où le DAT y répond.

### 4.1 Exigences non fonctionnelles

| Exigence | Cible | Effet sur l'architecture | Traitée en |
|---|---|---|---|
| ENF-PERF-01 | Mode attente : MIDI ≤ 100 ms, micro ≤ 150 ms (95e centile), mesurés depuis l'attaque acoustique ou le message MIDI ([ADR-0024](adr/0024-mesure-des-latences.md), INC-11) | Détection temps réel en natif, file sans verrou vers l'interface, affichage cadencé sur la synchronisation verticale | [05 §3](05-flux-temps-reel-et-performance.md#3-budgets-de-latence-par-étape), [ADR-0010](adr/0010-transcription-deux-etages.md), [ADR-0020](adr/0020-modele-de-threads-temps-reel.md) |
| ENF-PERF-02 | Retour en direct ≤ 200 ms | Même chaîne que ENF-PERF-01 | [05](05-flux-temps-reel-et-performance.md) |
| ENF-PERF-03 | Son / curseur ≤ 30 ms | Position du curseur calculée sur l'horloge de la sortie audio | [05 §4](05-flux-temps-reel-et-performance.md#4-synchronisation-son--curseur), [ADR-0004](adr/0004-moteur-audio-oboe.md) |
| ENF-PERF-04 | Compte-rendu ≤ 2 s pour 5 min | Analyse différée **incrémentale** pendant le jeu | [ADR-0010](adr/0010-transcription-deux-etages.md), [ADR-0012](adr/0012-alignement-et-classification-maison.md) |
| ENF-PERF-05 | Ouverture P10 ≤ 1,5 s | Mise en page calculée à l'import et mise en cache | [07 §6](07-import-et-rendu-de-partition.md#6-rendu-et-mise-en-cache) |
| ENF-PERF-06 | Import P10 ≤ 5 s | Import natif ; travail lourd hors du fil principal | [07](07-import-et-rendu-de-partition.md) |
| ENF-PERF-08 | 60 images/s | Dessin vectoriel des seules tuiles visibles ; curseur sur un calque séparé | [ADR-0007](adr/0007-affichage-liste-d-affichage-compose.md) |
| ENF-PERF-09 | Démarrage à froid ≤ 3 s | Chargement différé des bibliothèques natives | [04 §2](04-application-android.md#2-couches-et-modules) |
| ENF-PERF-10 | Lecture ≤ 150 ms | Flux de sortie audio ouvert à l'avance pendant l'affichage d'une partition | [05 §3](05-flux-temps-reel-et-performance.md#3-budgets-de-latence-par-étape) |
| ENF-RES-01, ENF-RES-02 | Batterie ≤ 15 %/h ; pas de baisse de régime en 45 min | Inférence sur un seul cœur, cadence bornée ; mesure dans POC-02 | [05 §6](05-flux-temps-reel-et-performance.md#6-ressources-batterie-échauffement-mémoire) |
| ENF-RES-03 | APK ≤ 150 Mo | Une seule ABI dans les APK diffusés (QO-47) ; Verovio sans les formats inutiles ; AAR officiel d'ONNX Runtime (34 Mo par ABI) exclu des versions diffusées (QO-50) ; seuil de taille en CI | [10 §6](10-build-ci-et-distribution.md#6-taille-de-lapk) |
| ENF-RES-04 | Pas de manque de mémoire avec P30 | Liste d'affichage compacte et mappée ; Verovio seulement dans le processus d'import | [05 §6](05-flux-temps-reel-et-performance.md#6-ressources-batterie-échauffement-mémoire) |
| ENF-COMP-01 | Noyau portable | Noyau C++ et interfaces de plateforme | [ADR-0001](adr/0001-decoupage-application-noyau-outils.md), [11](11-portabilite-ios.md) |
| ENF-COMP-03 | Téléphones et tablettes, portrait et paysage | Mise en page de l'interface adaptative ; bande indépendante de la largeur | [04](04-application-android.md), [07](07-import-et-rendu-de-partition.md) |
| ENF-COMP-04 | Mesures sur l'appareil de référence | Instrumentation intégrée à la variante interne | [05 §5](05-flux-temps-reel-et-performance.md#5-stratégie-de-mesure) |
| ENF-OFF-01 | Tout hors ligne | Aucun service distant ; tout embarqué | [02](02-vue-d-ensemble.md) |
| ENF-FIA-01 | Aucune perte en cas d'arrêt brutal | Transactions SQLite durables ; écritures de fichiers atomiques ; points de reprise de séance | [06 §5](06-donnees-et-stockage.md#5-durabilité-et-arrêts-brutaux) |
| ENF-FIA-02 | Fichier source conservé | Fichier stocké tel quel avec son empreinte | [06](06-donnees-et-stockage.md) |
| ENF-FIA-03 | Schéma versionné et migré | Migrations Room testées depuis chaque version diffusée | [06 §6](06-donnees-et-stockage.md#6-migrations) |
| ENF-FIA-04 | Interruptions sans perte | Machine à états de la séance dans l'application | [04 §6](04-application-android.md#6-cycle-de-vie-dune-séance-et-interruptions) |
| ENF-QA-01, ENF-QA-02 | Fausses erreurs ≤ 1 % (MIDI), ≤ 5 % (micro) ; non-régression | Banc C++ sur corpus versionné ; exécution déterministe | [08](08-strategie-de-test-et-banc-d-evaluation.md), [ADR-0015](adr/0015-banc-d-evaluation.md) |
| ENF-QA-03 | Qualité par catégorie d'appareil | Rapport de mesures par appareil ; avertissement si la qualité minimale n'est pas atteinte | [08](08-strategie-de-test-et-banc-d-evaluation.md) |
| ENF-SEC-04 | Fichiers non fiables | Limites, XML sans DTD, archive contrôlée, bornes internes du noyau, processus d'import séparé (contre les plantages, pas un bac à sable) | [09 §2](09-securite-confidentialite-licences.md#2-fichiers-importés-non-fiables), [ADR-0021](adr/0021-import-en-processus-isole.md) |
| ENF-SEC-06 | Micro seulement en séance et au réglage | Ouverture du flux d'entrée liée à l'état de séance | [04 §6](04-application-android.md#6-cycle-de-vie-dune-séance-et-interruptions) |
| ENF-CONF-01 | Son traité localement ; sauvegarde Android désactivée | Son en mémoire vive seulement ([ADR-0013](adr/0013-son-en-memoire-uniquement.md)) | [06 §8](06-donnees-et-stockage.md#8-sauvegarde-android-désactivée) |
| ENF-CONF-03 | Pas de permission INTERNET | Contrôle du manifeste fusionné à chaque construction | [09 §1](09-securite-confidentialite-licences.md#1-permissions-et-absence-de-réseau) |
| ENF-MAINT-01 | Noyau testable sans interface | Bibliothèque C++ autonome, exécutable de banc | [ADR-0015](adr/0015-banc-d-evaluation.md) |
| ENF-MAINT-02 | Paramètres configurables sans nouvelle version | Paramètres dans un fichier de configuration embarqué et versionné ; la configuration **distante** est impossible tant qu'il n'y a ni serveur ni permission INTERNET (INC-07) | [03 §3.8](03-noyau-natif.md#38-configuration-et-paramètres) |
| ENF-MAINT-04 | Rapport de test exporté par le testeur (MVP) | Rapport généré localement, partage système | [04](04-application-android.md) |
| ENF-ACC-02 | Résultat jamais par la seule couleur | Marques dessinées comme symboles sur la liste d'affichage | [07 §8](07-import-et-rendu-de-partition.md#8-surimpression-des-résultats) |

### 4.2 Exigences fonctionnelles

| Exigence | Effet sur l'architecture | Traitée en |
|---|---|---|
| EF-IMP-01, EF-IMP-12 | Import MusicXML par Verovio ; rapport d'import calculé par un analyseur indépendant | [07 §2](07-import-et-rendu-de-partition.md#2-import-musicxml) |
| EF-IMP-02 | Lecteur MIDI, quantification, orthographe, séparation des mains : code maison | [07 §3](07-import-et-rendu-de-partition.md#3-import-midi) |
| EF-IMP-05 | Filtres d'intention et sélecteur de documents du système, sans autorisation de stockage | [04 §5](04-application-android.md#5-permissions) |
| EF-VIS-01 à EF-VIS-05 | Gravure Verovio, bande horizontale, curseur, lecture en avance dans l'ordre de jeu | [07](07-import-et-rendu-de-partition.md) |
| EF-LEC-01, EF-LEC-03, EF-LEC-07 | Synthèse à partir des événements de la partition ; tempo sans changement de hauteur ; déroulé des reprises partagé avec l'analyse | [ADR-0009](adr/0009-synthese-tinysoundfont.md), [03 §3.4](03-noyau-natif.md#34-déroulé-attendu-et-carte-temporelle) |
| EF-PRA-02, EF-PRA-03, EF-PRA-08, EF-PRA-11 | Machine à états de séance : attente, jeu continu, pause et reprise en jouant, boucle | [03 §3.6](03-noyau-natif.md#36-suivi-temps-réel-et-séance), [04 §6](04-application-android.md#6-cycle-de-vie-dune-séance-et-interruptions) |
| EF-ECO-02 | MIDI USB via `MidiManager` et AMidi | [ADR-0005](adr/0005-midi-midimanager-amidi.md) |
| EF-ECO-04, EF-ECO-06, EF-ECO-10 | Captation non traitée, réglage du micro (niveau, diapason, latence), tolérance de diapason | [ADR-0004](adr/0004-moteur-audio-oboe.md), [ADR-0010](adr/0010-transcription-deux-etages.md) |
| EF-ECO-05 | Écran explicatif et indicateur « Micro actif » liés à l'état du flux | [04 §5](04-application-android.md#5-permissions) |
| EF-ECO-08 | Sons de l'application connus du noyau (métronome, autre main) pour être exclus | [03 §3.5](03-noyau-natif.md#35-transcription-micro) |
| EF-ANA-01, EF-ANA-02, EF-ANA-13 | Transcription, alignement, temps réel puis différé | [ADR-0010](adr/0010-transcription-deux-etages.md), [ADR-0012](adr/0012-alignement-et-classification-maison.md) |
| EF-RES-04 | Surimpression par identifiant de note | [07 §8](07-import-et-rendu-de-partition.md#8-surimpression-des-résultats) |
| EF-PRO-02, EF-BIB-03, EF-CPT-06 | Historique, export, suppression | [06](06-donnees-et-stockage.md) |
| RG-01 à RG-04 | Suppression en cascade ; version de partition et d'analyse figées ; identifiants stables | [06](06-donnees-et-stockage.md), [ADR-0008](adr/0008-modele-de-partition-et-format-pivot.md) |
