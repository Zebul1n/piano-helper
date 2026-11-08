# 03 — User stories du MVP

Ce fichier détaille **toutes les stories du MVP** (version 0.4, après les décisions du porteur du 2026-11-07 et du 2026-11-08, et les modifications MDAT-20 à MDAT-42 du DAT). Il couvre toutes les exigences de priorité **M** de l'EDB v0.7 (voir la [matrice](06-matrice-de-tracabilite.md)), plus les exigences S et C dont une part est nécessaire au MVP. Les décisions du porteur sont rassemblées dans le [fichier 09](09-decisions-a-prendre.md) ; depuis la v0.3, aucune story n'est plus « Proposée ».

## 1. Conventions

### 1.1 Fiche d'une story

> **Priorité** · **V/E/R** · **Exigences** · **Écran** · **Dépendances** · **Incrément** · **Statut**

| Champ | Valeurs |
|---|---|
| Priorité | MoSCoW de l'EDB : **M**, **S**, **C** |
| V / E / R | Valeur, effort, risque : **É** (élevé), **M** (moyen), **F** (faible) ; estimation du PO, à revoir avec `architecte` |
| Exigences | Identifiants de l'EDB ; la [matrice](06-matrice-de-tracabilite.md) cite exactement les mêmes liens |
| Écran | Écrans de l'[EDB 10 §3](../EDB/10-ecrans-et-ergonomie.md) (E01 à E23) et écrans ajoutés par le PRD (E24 à E33, §1.4) |
| Incrément | I0 à I6 ([plan de release](05-plan-de-release.md)). Une story « à cheval » indique l'incrément de chaque critère, par exemple « (I4) » à la fin du critère ; un critère sans mention appartient au premier incrément de la story |
| Statut | **Ferme** ; **Conditionnelle** (dépend d'un POC) ; **Proposée** (dépend d'une décision du porteur ; aucune en v0.3) |

### 1.2 Vérification de chaque critère

Chaque critère d'acceptation (CA) se termine par la façon dont il est vérifié (définition de prêt R2) :

| Étiquette | Vérification |
|---|---|
| [AUTO] | Test automatisé (intégration continue) |
| [BANC] | Banc d'évaluation de l'analyse sur le corpus (US-QUA-02) |
| [MESURE] | Mesure sur le téléphone de référence selon les protocoles de US-QUA-04 |
| [TÉL] | Test manuel sur le téléphone Android 14 |
| [ÉMU] | Test manuel sur l'émulateur (profils téléphone et tablette) |
| [PIANO] | Test du porteur à son piano droit, téléphone sur le pupitre |
| [REVUE] | Relecture par un agent (cohérence d'un texte, d'une règle, d'un fichier) |
| [PORTEUR] | Jugement humain (écoute, lisibilité, compréhension) : jamais délégué à un agent |
| [BÊTA] | Vérification reportée en bêta faute de matériel ; inscrite au registre de [05 §5](05-plan-de-release.md) |

Les valeurs marquées [HYP] viennent de l'EDB ; [PO] signale une valeur proposée par le Product Owner. Toutes deviennent des **paramètres** figés à l'entrée de la story (définition de prêt R14).

### 1.3 Personas et règles transverses

Personas de l'[EDB 02](../EDB/02-utilisateurs-et-personas.md) : **Claire** (adulte autodidacte, piano numérique USB), **Lucas** (11 ans, piano droit) et sa mère **Nadia**, **Marc** (pianiste qui reprend). « Apprenant » désigne l'un d'eux. Le **porteur du projet** joue sur un piano droit. Le persona « **mainteneur** » désigne le porteur et les agents qui développent l'application.

- **Fiabilité d'abord** : en cas de doute, l'application ne signale pas d'erreur ([06 §6](../EDB/06-analyse-du-jeu-et-erreurs.md)).
- **Mains sur le clavier** : pendant le jeu, aucune interaction n'est requise ; démarrer, reprendre et terminer peuvent se faire en jouant.
- **Local d'abord** : aucune donnée ne quitte l'appareil au MVP (US-CNF-01).
- **Ton** (décision [QO-24 g](09-decisions-a-prendre.md), [EDB 10 §6](../EDB/10-ecrans-et-ergonomie.md)) : style unique sobre et encourageant ; tournures impersonnelles (« Passage joué sans erreur ») ; vouvoiement quand il faut s'adresser à l'utilisateur ; jamais de jugement sur la personne ; les termes viennent du lexique bilingue (US-CPT-04).
- **Navigation du MVP** (arbitrage PO, UX-30 ; EDB 10 §2) : pas de barre de navigation ; la bibliothèque est l'écran d'accueil ; les réglages s'ouvrent par une icône ; la barre arrive en V1 avec « Progression ».
- **Variantes de construction** : *interne* (outils de test : simulateur MIDI, clavier visuel, enregistreur de séance), *bêta* et *publique* (sans aucun outil de test, contrôle automatique sur l'APK).

### 1.4 Écrans ajoutés par le PRD

| N° | Écran | Stories |
|---|---|---|
| E24 | Mise en place du téléphone sur le pupitre | US-CPT-06, US-ECO-05 |
| E25 | Séparation des mains d'un MIDI à une piste | US-IMP-02 |
| E26 | Connexion MIDI : état, choix de l'appareil | US-ECO-02 |
| E27 | Calibrage de la latence MIDI | US-ECO-06 |
| E28 | Séance en pause ou interrompue | US-PRA-06, US-PRA-08 |
| E29 | Légende des résultats | US-RES-04 |
| E30 | À propos, licences, confidentialité | US-CNF-02, US-CNF-06 |
| E31 | Supprimer toutes mes données | US-CPT-05 |
| E32 | Morceaux de démonstration masqués | US-BIB-04 |
| E33 | Export du rapport de test | US-CNF-05 |

Les spécifications d'écran (états nominal, vide, chargement, erreur ; téléphone et tablette ; textes FR et EN) sont livrées par `designer-ux` dans `docs/UX/` avant chaque incrément (R11).

## 2. Récapitulatif

| ID | Titre | Prio | Incr. | Statut |
|---|---|:-:|:-:|---|
| US-CPT-01 | Démarrer sans compte | M | I4 | Ferme |
| US-CPT-02 | Déclarer son niveau et son piano | M | I4 | Ferme |
| US-CPT-03 | Modifier son profil et son niveau d'exigence | M | I4 | Ferme |
| US-CPT-04 | Régler les paramètres généraux | M | I4 / I6 | Ferme |
| US-CPT-05 | Supprimer toutes ses données | M | I4 | Ferme |
| US-CPT-06 | Jouer un premier passage guidé | M | I4 | Ferme |
| US-IMP-01 | Importer un fichier MusicXML | M | I1 | Ferme |
| US-IMP-02 | Importer un fichier MIDI | M | I4 | Ferme |
| US-IMP-03 | Ouvrir une partition depuis une autre application | M | I1 | Ferme |
| US-IMP-04 | Vérifier l'aperçu et les métadonnées | M | I1 | Ferme |
| US-IMP-05 | Consulter le rapport d'import | M | I1 / I4 | Ferme |
| US-IMP-06 | Refuser proprement un fichier invalide ou dangereux | M | I1 | Ferme |
| US-IMP-07 | Conserver le fichier d'origine | M | I1 | Ferme |
| US-BIB-01 | Parcourir et trier la bibliothèque | M | I1 / I4 | Ferme |
| US-BIB-02 | Rechercher un morceau | M | I4 | Ferme |
| US-BIB-03 | Modifier les informations d'un morceau | M | I4 | Ferme |
| US-BIB-04 | Supprimer un morceau | M | I4 | Ferme |
| US-BIB-05 | Exporter le fichier source | M | I4 | Ferme |
| US-BIB-06 | Disposer de morceaux de démonstration | M | I1 | Ferme |
| US-BIB-07 | Consulter la fiche d'un morceau | M | I4 | Ferme |
| US-VIS-01 | Afficher une partition fidèle | M | I1 | Ferme |
| US-VIS-02 | Lire dans le mode d'affichage du MVP | M | I1 | Ferme |
| US-VIS-03 | Lire à distance de pupitre | M | I1 | Ferme |
| US-VIS-04 | Suivre un curseur synchronisé | M | I1 | Ferme |
| US-VIS-05 | Voir la suite avant d'y arriver | M | I1 | Ferme |
| US-VIS-06 | Naviguer et sélectionner un passage | M | I1 | Ferme |
| US-VIS-07 | Utiliser l'application en portrait et en paysage | M | I1 | Ferme |
| US-VIS-08 | Ouvrir rapidement une grande partition | M | I1 | Ferme |
| US-LEC-01 | Écouter la partition avec un son de piano | M | I1 | Ferme |
| US-LEC-02 | Commander la lecture | M | I1 | Ferme |
| US-LEC-03 | Régler le tempo | M | I1 | Ferme |
| US-LEC-04 | Écouter main par main | M | I1 | Ferme |
| US-LEC-05 | Utiliser le métronome | M | I1 | Ferme |
| US-LEC-06 | Écouter un passage en boucle | M | I1 | Ferme |
| US-LEC-07 | Respecter les reprises simples | M | I1 | Ferme |
| US-ECO-01 | Choisir la source de captation | M | I2 | Ferme |
| US-ECO-02 | Connecter un piano numérique en USB | M | I2 | Ferme |
| US-ECO-03 | Autoriser le micro en connaissance de cause | M | I3 | Ferme |
| US-ECO-04 | Savoir quand le micro est actif | M | I3 | Ferme |
| US-ECO-05 | Régler le micro | M | I3 | Ferme |
| US-ECO-06 | Mesurer la latence MIDI (option avancée) | M | I2 | Ferme |
| US-ECO-07 | Être écouté au micro en mode attente | M | I3 | Ferme |
| US-ECO-08 | Être écouté au micro en jeu continu | M | I5 | Conditionnelle (POC-01) |
| US-ECO-09 | Jouer sur un piano un peu désaccordé | S | I3 | Ferme (QO-23) |
| US-ECO-10 | Être averti d'un mauvais signal | S | I3 | Ferme (QO-23) |
| US-ECO-11 | Ne pas être jugé sur les sons de l'application | S | I3 | Ferme (QO-23) |
| US-ANA-01 | Construire le déroulé attendu | M | I2 | Ferme |
| US-ANA-02 | Obtenir les événements joués | M | I2 / I3 | Ferme |
| US-ANA-03 | Aligner le jeu sur la partition | M | I2 | Ferme |
| US-ANA-04 | Classer les erreurs de notes | M | I2 | Ferme |
| US-ANA-05 | Évaluer le rythme et la continuité | M | I2 | Ferme |
| US-ANA-06 | Évaluer les accords | M | I2 | Ferme |
| US-ANA-07 | Appliquer les tolérances et les cas particuliers | M | I2 | Ferme |
| US-ANA-08 | Ne signaler que les erreurs sûres | M | I2 / I3 | Ferme |
| US-ANA-09 | Analyser en temps réel puis en différé | M | I2 | Ferme |
| US-PRA-01 | Préparer une séance | M | I2 | Ferme |
| US-PRA-02 | Pratiquer en mode attente | M | I2 / I3 | Ferme |
| US-PRA-03 | Entendre l'autre main en mode attente | M | I3 | Ferme |
| US-PRA-04 | Pratiquer en jeu continu | M | I2 / I5 | Ferme |
| US-PRA-05 | Démarrer sur un décompte | M | I2 | Ferme |
| US-PRA-06 | Reprendre après une interruption | M | I3 | Ferme |
| US-PRA-07 | Terminer une séance | M | I2 | Ferme |
| US-PRA-08 | S'arrêter et reprendre sans toucher l'écran | S | I2 / I3 | Ferme |
| US-PRA-09 | Répéter un passage en boucle en jeu continu | S | I5 | Ferme (QO-26) |
| US-RES-01 | Choisir le retour en direct | M | I2 | Ferme |
| US-RES-02 | Comprendre une erreur en mode attente | M | I2 | Ferme |
| US-RES-03 | Lire le compte-rendu de fin de séance | M | I2 | Ferme |
| US-RES-04 | Voir la partition annotée | M | I2 | Ferme |
| US-RES-05 | Examiner le détail d'une note | M | I2 | Ferme |
| US-RES-06 | Obtenir des scores stables et compréhensibles | M | I2 | Ferme |
| US-RES-07 | Être prévenu d'une captation douteuse | M | I3 | Ferme |
| US-RES-08 | Signaler une fausse erreur | S | I6 | Ferme (QO-28) |
| US-HIS-01 | Enregistrer chaque séance sans perte | M | I4 | Ferme |
| US-HIS-02 | Consulter l'historique d'un morceau | M | I4 | Ferme |
| US-HIS-03 | Conserver ses données lors des mises à jour | M | I4 | Ferme |
| US-QUA-01 | Constituer le corpus de test v0 | M | I0 | Ferme |
| US-QUA-02 | Évaluer automatiquement l'analyse | M | I0 | Ferme |
| US-QUA-03 | Simuler un jeu MIDI | M | I0 | Ferme |
| US-QUA-04 | Mesurer les performances sur l'appareil de référence | M | I0 / I3 / I6 | Ferme |
| US-QUA-05 | Capturer des séances réelles pour les évaluer | M | I3 | Ferme |
| US-CNF-01 | Fonctionner hors ligne sans transmettre de données | M | I1 | Ferme |
| US-CNF-02 | Livrer les mentions légales et les licences dans chaque APK | M | I0 | Ferme |
| US-CNF-03 | Tenir l'inventaire des licences | M | I0 | Ferme |
| US-CNF-04 | Diffuser un APK de test avec ses sources | M | I0 | Ferme |
| US-CNF-05 | Exporter un rapport de test | S | I6 | Ferme (QO-28) |
| US-CNF-06 | Consulter la notice de confidentialité | M | I6 | Ferme |

**Total : 86 stories** — 85 fermes, 1 conditionnelle (US-ECO-08, POC-01), 0 proposée. Nouveautés de la v0.2 : US-QUA-05 (QA-02) et US-CNF-06 (JUR-01, JUR-05) ; US-PRA-08 devient ferme (EF-PRA-08 entrée dans le MVP par l'EDB v0.5). En v0.3, les 6 stories proposées deviennent fermes après les décisions du porteur : US-ECO-09, US-ECO-10, US-ECO-11 (QO-23), US-PRA-09 (QO-26), US-RES-08 et US-CNF-05 (QO-28).

---

## 3. EP-01 — Démarrage, profil et paramètres (CPT)

### US-CPT-01 — Démarrer sans compte

**En tant que** nouvel utilisateur, **je veux** utiliser toute l'application dès l'installation sans créer de compte **afin de** commencer tout de suite sans donner d'informations personnelles.

> **Priorité** M · **V/E/R** É/F/F · **Exigences** EF-CPT-01, ENF-CONF-03 · **Écran** E01, E05 · **Dépendances** — · **Incrément** I4 · **Statut** Ferme

**Critères d'acceptation**
1. Étant donné une installation neuve, quand j'ouvre l'application, alors l'écran d'accueil présente la promesse en trois étapes (importer, écouter, jouer et être corrigé) ; aucun écran ne demande de compte, d'adresse e-mail ni de nom. [TÉL]
2. Toutes les fonctions du MVP sont accessibles sans compte ; les données sont stockées sur l'appareil. [TÉL]
3. L'accueil se passe en un appui et n'est plus affiché ensuite. [AUTO]
4. Un profil local par défaut est créé. [AUTO]
5. Étant donné que l'application est ouverte pour la première fois par « Ouvrir avec » sur un fichier, alors l'aperçu d'import s'affiche d'abord et l'accueil, raccourci, ensuite. [TÉL]
6. Après l'accueil, la bibliothèque est l'écran principal ; les réglages s'ouvrent par une icône de la barre du haut ; il n'y a pas de barre de navigation au MVP. [REVUE]

### US-CPT-02 — Déclarer son niveau et son piano

**En tant que** nouvel utilisateur, **je veux** indiquer mon niveau et mon type de piano **afin que** l'application me propose la bonne source de captation et un niveau d'exigence adapté.

> **Priorité** M · **V/E/R** É/F/F · **Exigences** EF-CPT-02, EF-ECO-01 · **Écran** E02 · **Dépendances** US-CPT-01 · **Incrément** I4 · **Statut** Ferme

**Critères d'acceptation**
1. Deux écrans au plus : niveau, puis type de piano (illustré : piano droit ou à queue, piano numérique). [REVUE]
2. Chaque niveau porte une phrase repère : **Débutant** « Je joue des morceaux de méthode, souvent mains séparées » ; **Intermédiaire** « Je joue des menuets de Bach, des sonatines de Clementi, l'*Album pour la jeunesse* de Schumann » ; **Avancé** « Je joue des inventions de Bach, des sonates de Mozart, du Chopin ou du Debussy ». [REVUE]
3. Source proposée selon le type de piano : numérique USB → MIDI ; numérique Bluetooth → micro, avec le message « La connexion Bluetooth arrivera dans une prochaine version ; si votre piano a une prise USB, vous pouvez l'utiliser » ; numérique sans connexion, acoustique, « Je ne sais pas » → micro. [AUTO]
4. Niveau d'exigence proposé : débutant → indulgent ; intermédiaire → standard ; avancé → standard (validé par PED). « Exigeant » n'est jamais proposé par défaut. [AUTO]
5. Chaque question peut être passée ; valeurs par défaut : débutant, acoustique. [AUTO]

### US-CPT-03 — Modifier son profil et son niveau d'exigence

**En tant qu'**apprenant (Marc), **je veux** changer mon niveau d'exigence selon l'état du morceau **afin d'**être indulgent quand je déchiffre et exigeant quand je peaufine.

> **Priorité** M · **V/E/R** É/F/M · **Exigences** EF-CPT-02, EF-ANA-08, EF-ECO-01 · **Écran** E19, E12 · **Dépendances** US-ANA-07, US-PRA-01 · **Incrément** I4 · **Statut** Ferme

**Critères d'acceptation**
1. Réglages > Profil permet de modifier niveau, type de piano, source de captation et niveau d'exigence par défaut. [TÉL]
2. Les niveaux sont présentés ainsi : **Indulgent** « Pour apprendre un morceau : seules les erreurs nettes sont relevées. » **Standard** « Pour consolider : notes, rythme et accords vérifiés avec une précision normale. » **Exigeant** « Pour peaufiner avant un cours ou une audition : le moindre décalage est relevé. » [REVUE]
3. Le niveau est aussi modifiable dans la préparation de séance et mémorisé par morceau. [TÉL]
4. Étant donné une séance passée analysée en « indulgent », quand je passe en « exigeant », alors les séances suivantes utilisent « exigeant » et la séance passée garde son niveau et ses scores (RG-04). [AUTO]
5. Au micro, « exigeant » est grisé avec une explication si l'incertitude de détection ne permet pas ce niveau (max(2σ ; u) > 50 ms en médium, décision [QO-32](09-decisions-a-prendre.md) précisée le 2026-11-08) : « Le micro ne permet pas de mesurer le rythme aussi finement ; le niveau exigeant est disponible avec un piano numérique relié. » [TÉL]

**Notes.** Au MVP le niveau règle rythme, simultanéité, hésitation et arrêt ; les ornements ne sont pas évalués (EF-ANA-09 en V1).

### US-CPT-04 — Régler les paramètres généraux

**En tant qu'**utilisateur, **je veux** régler la langue, la notation des notes et les sons **afin d'**utiliser l'application selon mes habitudes.

> **Priorité** M · **V/E/R** M/F/F · **Exigences** EF-CPT-05, ENF-I18N-01, ENF-I18N-02, ENF-I18N-03 · **Écran** E19 · **Dépendances** — · **Incrément** I4 (anglais complet et lexique : I6) · **Statut** Ferme

**Critères d'acceptation**
1. Langues : français et anglais (américain) ; par défaut la langue du système si elle est prise en charge, sinon l'anglais ; modifiable dans l'application et par le réglage de langue par application d'Android (décision [QO-24 f](09-decisions-a-prendre.md)). [TÉL]
2. Un affichage en pseudo-langue ne montre aucun texte non traduit. [AUTO]
3. Notation des notes latine ou anglo-saxonne, indépendante de la langue ; latine par défaut en français. Elle s'applique partout où un nom de note apparaît. [AUTO]
4. Les textes destinés à l'élève n'utilisent pas de numéro d'octave (« le do du milieu du clavier », « une octave plus grave ») ; un numéro n'apparaît qu'en mode détaillé et suit la notation : do3 = do central en latin, C4 en anglo-saxon. [REVUE]
5. Sons : volume de la lecture, volume du métronome, son d'erreur du mode attente (désactivé par défaut, indisponible au micro). [TÉL]
6. Retour en direct par défaut (aucun, discret, complet) pour le jeu continu. [TÉL]
7. Réglages audio : source de captation, réglage du micro, test de latence MIDI. [TÉL]
8. Tous les termes affichés (types d'erreurs, niveaux, notions de solfège) viennent d'un lexique français-anglais validé par PED et `designer-ux`. (I6) [REVUE]
9. Les réglages survivent au redémarrage de l'application et de l'appareil, et à un changement de langue en cours d'usage. [TÉL]

**Notes.** Thème clair seul au MVP (décision [QO-24 e](09-decisions-a-prendre.md)) ; notation allemande en V1 (US-CNT-05). La relecture de la terminologie par un musicien anglophone (ENF-I18N-03) reste en V1.

### US-CPT-05 — Supprimer toutes ses données

**En tant qu'**utilisateur (ou parent), **je veux** effacer toutes les données de l'application depuis l'application **afin de** garder la maîtrise de mes données.

> **Priorité** M · **V/E/R** M/F/F · **Exigences** EF-CPT-06, RGPD-03 · **Écran** E31 · **Dépendances** US-HIS-01 · **Incrément** I4 · **Statut** Ferme

**Critères d'acceptation**
1. Réglages > Données et confidentialité > « Supprimer toutes mes données » liste ce qui sera effacé (partitions importées et fichiers d'origine, historique, profil, calibrages, réglages) et précise que **les fichiers déjà exportés ou partagés ne sont pas supprimés** ; confirmation en deux temps. [TÉL]
2. La suppression passe par le mécanisme du système (`clearApplicationUserData`), qui efface les données, retire les autorisations et **ferme l'application** ; le texte de l'écran E31 l'annonce avant la confirmation. À la réouverture, l'application est dans l'état d'une installation neuve ; les démonstrations restent disponibles. [TÉL]
3. Inspection sur l'émulateur : base de données, préférences, caches, dossiers propres à l'application en stockage interne et externe sont vides de données utilisateur. [ÉMU]
4. Après suppression puis désinstallation et réinstallation, aucune donnée n'est restaurée (sauvegarde Android désactivée, décision [QO-36](09-decisions-a-prendre.md)). [ÉMU]
5. Annuler à n'importe quelle étape ne supprime rien. [AUTO]

### US-CPT-06 — Jouer un premier passage guidé

**En tant que** nouvel utilisateur, **je veux** jouer quelques mesures d'un morceau de démonstration juste après la configuration **afin de** comprendre en quelques minutes ce que l'application m'apporte.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-BIB-05, EF-ECO-06 · **Écran** E01 à E04, E24, E13, E14 · **Dépendances** US-CPT-02, US-ECO-03, US-ECO-05, US-BIB-06, US-PRA-02, US-RES-03 · **Incrément** I4 · **Statut** Ferme

**Critères d'acceptation**
1. Enchaînement micro : accueil, niveau, type de piano, explication et autorisation du micro, **mise en place** (« Posez le téléphone sur le pupitre, en paysage », illustré), réglage du micro réduit au niveau d'entrée et au diapason (« Jouez quelques notes au milieu du clavier, comme vous voulez »), proposition de démonstration, jeu, compte-rendu, bibliothèque avec une carte « Importer ma partition ». [REVUE]
2. Enchaînement MIDI : « Branchez votre piano » avec détection en direct et mention de l'adaptateur USB ; repli « Utiliser le micro pour l'instant » si rien n'est détecté. Aucun test de latence au premier lancement. [TÉL]
3. Micro refusé : le parcours se termine par « Découvrir en écoutant » (lecture d'une démonstration) ; le critère de durée est alors sans objet. [TÉL]
4. Passage proposé selon le niveau : débutant → *Au clair de la lune*, mesures 1 à 4, main droite, mode attente ; intermédiaire → Schumann, *Mélodie* op. 68 n°1, mesures 1 à 4, deux mains ; avancé → Chopin, Prélude op. 28 n°7, mesures 1 à 4, deux mains. Au plus 16 notes par main. La proposition peut être refusée (« Plus tard »). [REVUE]
5. À partir de la mise en place, tout se lit à 60 cm et rien n'exige de toucher l'écran, sauf « Recommencer » et « Passer ». [PORTEUR]
6. Sur **3 personnes qui ne connaissent pas l'application** (dont au moins un vrai débutant, une personne de plus de 45 ans et une personne qui utilise un téléphone qui n'est pas le sien), à leur piano ou à celui du porteur, **chacune** atteint le premier compte-rendu en **≤ 5 minutes** depuis la première ouverture (installation non comprise), sans intervention, chronométrée par un observateur muet, et sait dire ce que lui apprend le compte-rendu. Le passage du porteur sert de contrôle préalable. [PORTEUR] Décision [QO-44](09-decisions-a-prendre.md) (option A) : en I4, le porteur fait seul le parcours (contrôle préalable) ; le critère à 3 personnes est vérifié avec les premiers testeurs de la bêta et inscrit au registre de [05 §5](05-plan-de-release.md). [BÊTA]

**Notes.** Le parcours MIDI réel est vérifié en bêta [BÊTA]. Le cas « notes répétées lentes au micro » (*Au clair de la lune*) est ajouté aux critères de POC-01 (PED-37).

---

## 4. EP-02 — Import de fichiers numériques (IMP)

### US-IMP-01 — Importer un fichier MusicXML

**En tant qu'**apprenant, **je veux** importer la partition MusicXML que je travaille **afin de** l'avoir sur mon téléphone avec toutes ses indications.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-IMP-01, EF-IMP-11, ENF-PERF-06 · **Écran** E06, E07 · **Dépendances** POC-04, US-VIS-01, US-IMP-04, US-IMP-05, US-QUA-01, US-QUA-04 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. Fichiers `.musicxml`, `.xml`, `.mxl`, en MusicXML 3.1 et 4.0, organisation par parties (« partwise ») ; un fichier organisé par mesures (« timewise ») est importé ou refusé avec un message clair. [AUTO]
2. Sous-corpus « éditeurs » (au moins 5 fichiers valides par logiciel disponible parmi MuseScore, Finale, Sibelius, Dorico, provenance et licence documentées ; un logiciel sans fichier est signalé) : **100 %** des fichiers s'importent sans erreur bloquante. [AUTO]
3. Sous-corpus « tout-venant » (au moins 40 fichiers de sources publiques, retenus sans tri) : taux de réussite **≥ 95 %**. [AUTO]
4. Sont conservés : portées, clés, armures, chiffrages, notes, silences, durées, altérations, liaisons de prolongation et d'expression, nuances, indications de tempo, reprises, doigtés, numéros de mesure. Le nombre d'éléments lus par catégorie est comparé à des valeurs produites par un analyseur indépendant de celui de l'application ; les écarts sont examinés et consignés. [AUTO]
5. Éléments non pris en charge : ignorés sans bloquer, listés au rapport (US-IMP-05). [AUTO]
6. Le fichier de référence **P10** du corpus (250 mesures à deux portées et 4 000 notes, ± 10 % ; le fichier **P30** compte 750 mesures et 12 000 notes, ± 10 % ; valeurs de l'`architecte`) s'importe en **≤ 5 s** (médiane, maximum ≤ 7,5 s) ; progression affichée au-delà d'1 s. [MESURE]
7. Encodage UTF-8 ou UTF-16, avec ou sans marque d'ordre des octets ; titres en caractères non latins affichés et recherchables. [AUTO]
8. Partition à plusieurs instruments : la première partie à deux portées (à défaut, la première partie) est retenue comme partie jouée ; les autres sont ignorées ; le rapport l'indique. [AUTO]

**Notes.** Budget nul : les fichiers de logiciels payants viennent de sources publiques. Choix de la partie jouée en V1 (US-CNT-01).

### US-IMP-02 — Importer un fichier MIDI

**En tant qu'**apprenant, **je veux** importer un fichier MIDI **afin de** travailler un morceau dont je n'ai pas la partition MusicXML.

> **Priorité** M · **V/E/R** M/É/M · **Exigences** EF-IMP-02 · **Écran** E07, E25 · **Dépendances** US-IMP-01, US-VIS-01 · **Incrément** I4 · **Statut** Ferme

**Critères d'acceptation**
1. Fichiers `.mid` et `.midi`, types 0 et 1 ; type 2 et division en temps absolu (SMPTE) refusés avec un message. Note désactivée codée par une vélocité nulle, notes de même hauteur qui se chevauchent, plus de deux pistes, changements de tempo : chaque cas est au corpus avec son résultat attendu. [AUTO]
2. Durées quantifiées ; mesures déduites du chiffrage, sinon 4/4 signalé au rapport ; tempo absent → ♩ = 120 affiché « estimé ». [AUTO]
3. Type 1 avec deux pistes piano : une piste par portée (la plus aiguë en haut, main droite). [AUTO]
4. Type 0 ou piste unique : l'écran E25 propose un point de séparation (do central par défaut), modifiable avec aperçu ; quand c'est possible, une note est attribuée à la main la plus proche de la note précédente de chaque main (cas des deux pouces sur le do central, au corpus). [AUTO]
5. L'aperçu permet d'indiquer une anacrouse (durée) ou de décaler les barres de mesure ; si le premier temps fort semble mal placé, le rapport le signale « peut fausser l'analyse ». [TÉL]
6. Orthographe des notes selon les règles de l'[annexe C de la revue PED](revues/pedagogue-piano.md) : tonalité du fichier ou estimée (indiquée au rapport), degrés de la gamme, 6e et 7e degrés haussés en mineur, chromatismes selon le sens de la ligne, pas de double altération, orthographe constante dans la mesure. Contrôle : *Für Elise* importé d'un MIDI affiche ré♯ (et non mi♭) et sol♯ (et non la♭). [AUTO]
7. Avertissement visible à l'aperçu et sur la fiche : affichage approximatif (quantification). [TÉL]
8. Pistes de percussion ignorées et signalées. [AUTO]

### US-IMP-03 — Ouvrir une partition depuis une autre application

**En tant qu'**apprenant, **je veux** ouvrir directement dans Piano Helper une partition reçue par mail, téléchargée ou stockée en ligne **afin de** l'importer en un minimum de gestes.

> **Priorité** M · **V/E/R** É/F/F · **Exigences** EF-IMP-05 · **Écran** E06 · **Dépendances** US-IMP-01 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. « Importer » ouvre le sélecteur de fichiers du système, avec les stockages en ligne qui s'y intègrent ; il montre les fichiers des formats pris en charge **même quand leur type déclaré est générique** ; le format est contrôlé à l'import. [TÉL]
2. L'application apparaît dans « Ouvrir avec » et « Partager » pour les fichiers MusicXML (`.musicxml`, `.xml`, `.mxl`) et MIDI (`.mid`, `.midi`), reconnus par leur **type MIME** (`application/vnd.recordare.musicxml+xml`, `application/vnd.recordare.musicxml`, `application/xml`, `text/xml`, `audio/midi`, `audio/x-midi`), car un contenu partagé n'a pas d'extension fiable. Le choix de déclarer aussi `application/octet-stream`, pour les `.mxl` reçus sans type précis, au prix d'une application proposée pour tout fichier binaire, est fait à l'entrée de la story avec `designer-ux` et consigné en [08 §3](08-questions-ouvertes.md). [TÉL]
3. Étant donné un `.mxl` en pièce jointe d'un mail, quand je l'ouvre avec Piano Helper, alors l'aperçu d'import s'affiche. [TÉL]
4. Depuis la notification « téléchargement terminé » du navigateur, l'aperçu est atteint en **2 appuis au plus** (EF-IMP-05 CA2 : moins de trois interactions). [TÉL]
5. Un `.xml` qui n'est pas du MusicXML est refusé avec le message de US-IMP-06 ; rien n'entre dans la bibliothèque. [AUTO]
6. L'import ne demande aucune autorisation d'accès au stockage : seul le fichier choisi est lu. [REVUE]

### US-IMP-04 — Vérifier l'aperçu et les métadonnées

**En tant qu'**apprenant, **je veux** voir un aperçu et corriger le titre et le compositeur avant de valider **afin de** retrouver facilement le morceau.

> **Priorité** M · **V/E/R** M/F/F · **Exigences** EF-IMP-06 · **Écran** E07 · **Dépendances** US-IMP-01, US-VIS-01 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. Aperçu : premier système rendu, titre, compositeur, nombre de mesures et de portées, tempo indiqué (ou « non indiqué »). [TÉL]
2. Métadonnées du fichier préremplies ; à défaut de titre, nom du fichier sans extension. [AUTO]
3. Titre et compositeur modifiables ; titre non vide. [AUTO]
4. « Annuler » n'ajoute rien et ne laisse aucun fichier. [AUTO]
5. Après validation, le morceau peut être ouvert directement depuis l'écran de confirmation. [TÉL]

**Notes.** Étiquette à l'import en V1 (décision [QO-24 d](09-decisions-a-prendre.md) ; EDB EF-IMP-06 CA2).

### US-IMP-05 — Consulter le rapport d'import

**En tant qu'**apprenant, **je veux** savoir ce que l'application n'a pas pu interpréter **afin de** savoir si je peux faire confiance à l'affichage et à l'analyse.

> **Priorité** M · **V/E/R** É/M/F · **Exigences** EF-IMP-12 · **Écran** E07, E10 · **Dépendances** US-IMP-01 · **Incrément** I1 / I4 · **Statut** Ferme

**Critères d'acceptation**
1. Le rapport liste : éléments ignorés (type, nombre, mesures), mesures incomplètes ou trop longues, incohérences de durée, sauts non pris en charge (D.C., D.S., Coda, Fine, reprises imbriquées), choix automatiques (partie retenue, chiffrage par défaut, séparation des mains, armure estimée). [AUTO]
2. Sans anomalie : « Import complet ». [AUTO]
3. Chaque anomalie est « information » ou « peut fausser l'analyse » ; dans le second cas, l'utilisateur est averti avant de valider et l'avertissement est rappelé à la préparation de toute séance sur ce morceau. [TÉL]
4. Le rapport reste consultable depuis la fiche du morceau. (I4) [TÉL]
5. Rédigé pour un non-informaticien, sans nom d'élément XML. [REVUE]

### US-IMP-06 — Refuser proprement un fichier invalide ou dangereux

**En tant qu'**apprenant, **je veux** un message clair quand un fichier ne peut pas être importé **afin de** comprendre quoi faire, sans que l'application plante.

> **Priorité** M · **V/E/R** M/M/M · **Exigences** ENF-SEC-04 · **Écran** E07 · **Dépendances** US-IMP-01, US-QUA-01 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. Fichier corrompu, tronqué, de format non pris en charge ou à l'extension trompeuse : message en langage simple et lien vers les formats acceptés ; rien n'est ajouté. [AUTO]
2. Taille maximale de fichier : **20 Mio** ; archive `.mxl` : **64 Mio** décompressés et **1 000 entrées** au plus (valeurs de l'`architecte`, [DAT 07](../DAT/07-import-et-rendu-de-partition.md)) ; au-delà, refus avec message. [AUTO]
3. Entités externes XML jamais résolues. Sur les fichiers piégés du corpus (expansion d'entités, archive dont le contenu décompressé dépasse 1 Go), l'import est refusé en ≤ 5 s, la mémoire reste sous la limite du CA2 et l'application reste utilisable. [AUTO]
4. Archive `.mxl` : chemins qui sortent du dossier de travail ignorés ; fichier principal lu d'après le manifeste ; sans manifeste exploitable, refus clair. [AUTO]
5. Stockage plein pendant l'import : refus avec message ; aucune entrée à moitié créée, aucun fichier orphelin. [AUTO]

### US-IMP-07 — Conserver le fichier d'origine

**En tant qu'**apprenant, **je veux** que l'application garde mon fichier d'origine **afin de** le récupérer et de profiter des améliorations futures de l'import.

> **Priorité** M · **V/E/R** M/F/F · **Exigences** ENF-FIA-02, RG-02 · **Écran** E07, E10 · **Dépendances** US-IMP-01 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. Le fichier source est conservé à l'identique (même empreinte). [AUTO]
2. Le morceau mémorise la version du moteur d'import ; la partition peut être réimportée depuis le fichier d'origine (à la demande ou lors d'une mise à jour). [AUTO]
3. Après réimportation, l'historique est conservé ; les séances antérieures sont marquées « analysées sur une version précédente de la partition » (RG-02). [AUTO]
4. Fichier de même empreinte déjà présent (y compris une démonstration) : l'application le signale et propose d'ouvrir le morceau existant ou d'importer une copie. [AUTO]

---

## 5. EP-03 — Bibliothèque (BIB)

### US-BIB-01 — Parcourir et trier la bibliothèque

**En tant qu'**apprenant, **je veux** voir mes morceaux et reprendre d'un geste celui que je travaille **afin de** commencer à jouer vite.

> **Priorité** M · **V/E/R** É/F/F · **Exigences** EF-BIB-01 · **Écran** E05 · **Dépendances** US-IMP-01, US-HIS-01 · **Incrément** I1 / I4 · **Statut** Ferme

**Critères d'acceptation**
1. Chaque entrée affiche titre, compositeur, date d'ajout ; dernière pratique (ou « jamais pratiqué ») et meilleur score (séances terminées et fiables) (I4). [TÉL]
2. Tri par titre, compositeur, date d'ajout, dernière pratique ; défaut « dernière pratique » ; tri mémorisé. [AUTO]
3. Un appui sur un morceau ouvre la partition (E11) ; la fiche s'ouvre par une icône « i ». [TÉL]
4. En haut de la liste, une carte « Reprendre » ouvre la préparation de séance du dernier morceau pratiqué, avec ses derniers réglages. (I4) [TÉL]
5. Démonstrations identifiées comme telles. Bibliothèque sans morceau importé : invitation à importer et démonstrations visibles ; si toutes les démonstrations sont masquées : bouton « Réafficher les démonstrations ». [TÉL]
6. Défilement automatisé d'une liste de 200 morceaux, version publique, téléphone de référence : ≤ 5 % d'images en retard. [MESURE]
7. Après un changement d'heure ou de fuseau, le tri « dernière pratique » reste cohérent. (I4) [AUTO]

### US-BIB-02 — Rechercher un morceau

**En tant qu'**apprenant qui a beaucoup de partitions, **je veux** rechercher un morceau par titre ou compositeur **afin de** le retrouver vite.

> **Priorité** M · **V/E/R** M/F/F · **Exigences** EF-BIB-01 · **Écran** E05 · **Dépendances** US-BIB-01 · **Incrément** I4 · **Statut** Ferme

**Critères d'acceptation**
1. Recherche sur titre et compositeur, sans tenir compte des majuscules ni des accents (« fur elise » trouve « Für Elise ») ; fonctionne sur des titres en caractères non latins. [AUTO]
2. Résultats mis à jour pendant la saisie. [TÉL]
3. Sans résultat : message et proposition d'effacer la recherche. [TÉL]

### US-BIB-03 — Modifier les informations d'un morceau

**En tant qu'**apprenant, **je veux** renommer un morceau et corriger son compositeur **afin de** garder une bibliothèque claire.

> **Priorité** M · **V/E/R** M/F/F · **Exigences** EF-BIB-03 · **Écran** E10 · **Dépendances** US-BIB-07 · **Incrément** I4 · **Statut** Ferme

**Critères d'acceptation**
1. Titre, compositeur et arrangeur modifiables ; titre non vide. [AUTO]
2. Modification visible immédiatement dans la bibliothèque et l'historique. [TÉL]
3. Le fichier d'origine n'est pas modifié. [AUTO]

### US-BIB-04 — Supprimer un morceau

**En tant qu'**apprenant, **je veux** supprimer un morceau que je ne travaille plus **afin de** désencombrer ma bibliothèque sans perdre mon historique par erreur.

> **Priorité** M · **V/E/R** M/F/F · **Exigences** EF-BIB-03, RG-01 · **Écran** E10, E32 · **Dépendances** US-BIB-07, US-HIS-01 · **Incrément** I4 · **Statut** Ferme

**Critères d'acceptation**
1. Confirmation obligatoire ; si le morceau a des séances, l'avertissement indique leur nombre. [TÉL]
2. Sont supprimés : fichier d'origine, partition convertie, séances, écarts, réglages du morceau. [AUTO]
3. Une démonstration est masquée et non supprimée ; elle se rétablit depuis l'écran E32. [TÉL]

### US-BIB-05 — Exporter le fichier source

**En tant qu'**apprenant, **je veux** exporter le fichier d'origine **afin de** le transmettre à mon professeur ou de l'ouvrir ailleurs.

> **Priorité** M · **V/E/R** F/F/F · **Exigences** EF-BIB-03 · **Écran** E10 · **Dépendances** US-IMP-07 · **Incrément** I4 · **Statut** Ferme

**Critères d'acceptation**
1. « Exporter » propose le partage système ou l'enregistrement dans un dossier choisi. [TÉL]
2. Le fichier exporté est identique au fichier importé et garde son extension. [AUTO]
3. L'export d'une démonstration conserve sa mention de licence et son attribution (inscrites dans le fichier). [AUTO]

### US-BIB-06 — Disposer de morceaux de démonstration

**En tant que** nouvel utilisateur, **je veux** trouver des morceaux prêts à jouer dès l'installation **afin de** découvrir l'application sans chercher de partition.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-BIB-05 · **Écran** E05, E10 · **Dépendances** [QO-31](09-decisions-a-prendre.md), US-CNF-03 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. Critères juridiques (JUR-11) : air traditionnel ou compositeur **mort avant 1900** ; texte musical original, sans arrangement ni simplification moderne d'un tiers ; saisie d'après une édition ancienne du domaine public, sans reprendre d'apports d'éditeur moderne ; licence et attribution inscrites dans le fichier ; fiche d'inventaire (œuvre, compositeur et dates, édition source avec URL et date de consultation, auteur de la saisie, licence, date de vérification). [REVUE]
2. Critères pédagogiques (PED-22) : pièces courtes ; indication métronomique écrite ; doigtés pour les pièces débutantes ; aucun ornement ajouté par un éditeur ; aucun D.C. (réécrit en toutes notes) ; points d'orgue admis, le rythme n'y étant pas évalué (décision [QO-40](09-decisions-a-prendre.md)) ; répartition claire des mains. [REVUE]
3. La sélection comprend au moins : deux morceaux à une main, dont un à la main gauche en clé de fa ; un morceau avec accords ; un morceau avec reprise simple ; un morceau à deux mains de niveau intermédiaire. [REVUE]
4. Chaque morceau s'importe sans anomalie « peut fausser l'analyse ». [AUTO]
5. Les morceaux sont disponibles hors ligne dès le premier lancement ; la licence est visible sur la fiche. [TÉL]
6. Source et saisie (décision [QO-31](09-decisions-a-prendre.md)) : gravure maison en priorité, saisie dans MuseScore par un agent d'après une édition ancienne numérisée, publiée sous **CC0 1.0** ; fichiers Mutopia (CC BY ou CC BY-SA, attribution) seulement pour compléter. [REVUE]
7. Chaque fichier est relu note par note par PED [REVUE], puis validé **à l'écoute** par le porteur (lecture par l'application, comparée à l'édition source). [PORTEUR]

**Liste proposée** (PED, après arbitrage du PO, voir [08 §3](08-questions-ouvertes.md)) : les six premières suffisent pour I1.

| # | Pièce | Niveau | Apport |
|:-:|---|---|---|
| 1 | *Au clair de la lune* (traditionnel), do majeur, ♩ = 80 | Tout premier débutant | Main droite seule, notes répétées lentes |
| 2 | *Frère Jacques* (traditionnel) | Débutant | Main gauche seule, clé de fa |
| 3 | Beethoven, *Ode à la joie*, transcription maison du thème | Débutant | Main droite, puis main gauche en notes longues |
| 4 | Mozart, thème des Variations K. 265 | Débutant | Deux mains, deux sections reprises |
| 5 | Schumann, *Marche du soldat* op. 68 n°2 | Débutant avancé | Accords, rythme pointé |
| 6 | Bach (attribué à Petzold), *Menuet en sol* BWV Anh. 114 | Élémentaire | Fa♯ à l'armure, reprises |
| 7 | Schumann, *Mélodie* op. 68 n°1 | Élémentaire | Mélodie et accompagnement |
| 8 | Beethoven, *Für Elise*, première partie | Intermédiaire | Anacrouse, ré♯ / ré♮, 1re et 2e fins, pédale |
| 9 | Chopin, Prélude op. 28 n°4 en mi mineur, extrait | Intermédiaire | Tempo lent, pédale continue, accords répétés de la main gauche ; très demandé par les adultes (remplace la *Gymnopédie n°1* de Satie, mort en 1925) |
| 10 | Chopin, Prélude op. 28 n°7 en la majeur | Intermédiaire | Anacrouse, grand accord arpégé (mes. 12), pédale |

**Notes.** La « transcription maison » du n°3 se limite au thème, sans harmonisation moderne reprise d'un tiers. Le remplaçant du n°9 est à confirmer par PED (alternative : Schumann, *Rêverie* op. 15 n°7).

### US-BIB-07 — Consulter la fiche d'un morceau

**En tant qu'**apprenant, **je veux** une fiche qui rassemble les informations, les dernières séances et les actions d'un morceau **afin de** tout retrouver au même endroit.

> **Priorité** M · **V/E/R** M/F/F · **Exigences** EF-IMP-12, EF-PRO-02, EF-BIB-03 · **Écran** E10 · **Dépendances** US-BIB-01, US-HIS-02 · **Incrément** I4 · **Statut** Ferme

**Critères d'acceptation**
1. La fiche affiche : titre, compositeur, arrangeur, origine (MusicXML, MIDI approximatif, démonstration), date d'ajout, meilleur score, tempo de référence, cinq dernières séances (chacune ouvre son compte-rendu) ; « Pas encore de séance » et bouton « Pratiquer » sinon. [TÉL]
2. Accès au rapport d'import et, pour une démonstration, à la licence et à l'attribution. [TÉL]
3. Actions : ouvrir, pratiquer, modifier, exporter, réimporter, supprimer (ou masquer). [TÉL]

---

## 6. EP-04 — Visualisation de la partition (VIS)

### US-VIS-01 — Afficher une partition fidèle

**En tant qu'**apprenant, **je veux** voir ma partition gravée selon les conventions habituelles **afin de** la lire comme sur papier.

> **Priorité** M · **V/E/R** É/É/M · **Exigences** EF-VIS-01, RG-03 · **Écran** E11 · **Dépendances** POC-04, US-QUA-01 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. Sont rendus : grand portée, changements de clé et de chiffrage (y compris au milieu d'une mesure), armures, altérations, liaisons, ligatures, points, triolets, silences, reprises, nuances, indications de tempo, doigtés, pédale, ornements usuels, indications d'octave (8va, 8vb, 15ma). [AUTO]
2. Doigtés et numéros de mesure agrandis (environ 1,3 fois la taille de gravure [PO, à valider par PED]) : hauteur des chiffres ≥ 2,8 mm à la taille par défaut sur le téléphone. [MESURE]
3. Sur le corpus de rendu (au moins 20 MusicXML), aucun défaut bloquant : revue visuelle documentée avec captures de référence. [PORTEUR]
4. Toute évolution du rendu passe un test automatique de comparaison des captures (seuil de différence) ; une différence est examinée et la capture de référence mise à jour sur décision. [AUTO]
5. Chaque note affichée est reliée de façon stable à la note de la partition (RG-03). [AUTO]

**Notes.** Rendre un élément ne signifie pas qu'il est analysé (US-ANA-07). Critère ajouté à POC-04 : savoir rendre un système de longueur illimitée en donnant la position de chaque mesure (UX-01).

### US-VIS-02 — Lire dans le mode d'affichage du MVP

**En tant qu'**apprenant, **je veux** un affichage adapté au pupitre **afin de** suivre la partition sans manipulation pendant que je joue.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-VIS-02 · **Écran** E11, E13 · **Dépendances** [QO-27](09-decisions-a-prendre.md), US-VIS-01 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. Un seul mode au MVP : **bande horizontale qui avance par paliers, en paysage**, position retenue par la décision [QO-27](09-decisions-a-prendre.md) en attendant le test au pupitre que le porteur fait seul avant I1 (protocole du fichier 09) ; le résultat du test fixe le mode. [PORTEUR] Cette décision est provisoire : le mode retenu est reconfirmé en bêta avec au moins 3 testeurs, dont un débutant et une personne de plus de 45 ans. [BÊTA]
2. Bande : le curseur se déplace entre 20 % et 60 % de la largeur ; au-delà, la bande se décale sur une barre de mesure en 250 à 300 ms (instantané si les animations sont désactivées dans Android) ; clé et armure courantes rappelées à gauche ; chaque mesure porte son numéro. [TÉL]
3. En portrait, un message invite à tourner le téléphone ; le portrait reste utilisable. [TÉL]
4. La position est conservée lors d'une rotation ou d'un changement de taille. [AUTO]
5. Plein écran en lecture et en pratique ; barres système rappelées par un balayage ; encoche de la caméra hors de la partition ; commandes masquées 3 s après le début de la lecture, réaffichées par un appui. [TÉL]
6. L'écran reste allumé tant qu'une partition est affichée au premier plan (lecture, jeu, partition annotée) et pendant le réglage du micro. [TÉL]

**Notes.** Le second mode (page) arrive en V1 (US-CNT-03).

### US-VIS-03 — Lire à distance de pupitre

**En tant qu'**apprenant qui pose son téléphone sur le pupitre, **je veux** une partition assez grande **afin de** la lire confortablement à 60 cm.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-VIS-03, ENF-ACC-07 · **Écran** E11 · **Dépendances** US-VIS-02 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. Sur le téléphone de référence, en paysage, au réglage par défaut, la hauteur de portée affichée (calculée à partir de la densité physique réelle de l'écran) est **≥ 7,6 mm** et les doigtés ≥ 2,8 mm [PO] ; un test instrumenté le vérifie sur le téléphone et sur les profils d'émulateur réglés à leur taille physique. [AUTO]
2. Sur tablette 10 pouces en paysage, portée ≥ **8,9 mm** (lisible à 70 cm, EF-VIS-03 CA2) : vérifié par le calcul sur l'émulateur [ÉMU], puis sur une vraie tablette. [BÊTA]
3. Protocole humain de [la revue UX §3.2](revues/designer-ux.md) réussi à 60 cm : au moins 4 lecteurs (le porteur et 3 autres, dont une personne de plus de 45 ans), extraits inconnus, aucune erreur de lecture due à la taille chez au moins 3 sur 4, aucun participant penché ; taille par défaut ≥ 1,2 fois le seuil médian. Décision [QO-44](09-decisions-a-prendre.md) (option A) : en I1, le protocole est passé par le porteur seul (aucune erreur due à la taille, pas penché, taille par défaut ≥ 1,2 fois son seuil) [PORTEUR] ; le critère à 4 lecteurs est vérifié en bêta. [BÊTA]
4. Zoom par pincement et commande rapide « Taille de la partition » (Normale, Grande, Très grande) ; la taille par défaut ne descend jamais sous la cible ; zoom mémorisé par morceau et par orientation. [TÉL]
5. La mise en page se recalcule selon l'écran et le zoom. [AUTO]

### US-VIS-04 — Suivre un curseur synchronisé

**En tant qu'**apprenant, **je veux** voir où en est la musique **afin de** suivre la lecture et mon jeu.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-VIS-04, ENF-PERF-03 · **Écran** E11, E13 · **Dépendances** US-VIS-01, US-LEC-01, US-QUA-04 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. En lecture et en pratique, un curseur indique la position courante ; les notes en cours sont mises en évidence. Le curseur est une bande translucide d'une teinte hors de la palette des résultats. [TÉL]
2. En lecture, décalage son / curseur **≤ 30 ms au 95e centile**. [MESURE]
3. En mode attente, le curseur reste sur l'événement attendu tant qu'il n'est pas joué. [AUTO]
4. Le curseur reste visible quel que soit le zoom. [TÉL]

### US-VIS-05 — Voir la suite avant d'y arriver

**En tant qu'**apprenant, **je veux** toujours voir ce qui vient après la note en cours **afin de** lire en avance, comme un musicien.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-VIS-05 · **Écran** E11, E13 · **Dépendances** US-VIS-02, US-VIS-04 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. À tout instant, **au moins une mesure entière et au moins 4 temps** sont visibles au-delà du curseur, au réglage par défaut, en lecture, en mode attente et en jeu continu, jusqu'à 150 % du tempo ; le nombre de mesures visibles (cible de PED : deux) est relevé au test de [QO-27](09-decisions-a-prendre.md). [TÉL]
2. À l'approche d'une barre de reprise ou d'une première fin, la musique jouée ensuite est visible avant la fin de la mesure : la partition est déroulée dans l'ordre de jeu, numéros d'origine conservés, mention « 2e fois ». [TÉL]
3. Si le mode page est retenu : tourne en deux temps (EF-VIS-05 CA2). [TÉL]
4. Aucun décalage ni tourne ne masque la mesure en cours. [TÉL]
5. Une liaison de prolongation à travers une barre ou un décalage de la bande reste lisible. [TÉL]

### US-VIS-06 — Naviguer et sélectionner un passage

**En tant qu'**apprenant, **je veux** aller à une mesure et sélectionner un passage **afin de** travailler précisément ce qui me pose problème.

> **Priorité** M · **V/E/R** É/M/F · **Exigences** EF-VIS-11 · **Écran** E11, E12 · **Dépendances** US-VIS-01 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. « Aller à la mesure » accepte un numéro de mesure tel qu'affiché (texte, « 8a » possible) ; un numéro absent est refusé avec un message. [AUTO]
2. Numérotation selon l'[annexe A.2 de la revue PED](revues/pedagogue-piano.md) : numéros du fichier sans renumérotation ; anacrouse sans numéro (« anacrouse » dans les textes) ; « Revenir au début » inclut l'anacrouse, « Aller à la mesure 1 » va à la première mesure complète ; mesures coupées par une reprise : un seul numéro. [AUTO]
3. En lecture, un appui sur une mesure place le point de départ ; un **appui long** suivi d'un glissement sélectionne une plage, réglable par des poignées au temps près ; la plage se saisit aussi dans la préparation de séance (« De la mesure … à … »). [TÉL]
4. La plage est reprise par la boucle (US-LEC-06) et la préparation de séance (US-PRA-01). [TÉL]
5. Plage qui commence dans une deuxième fin ou qui coupe une reprise : règle de l'annexe A.1 (reprise jouée une fois, de façon linéaire) ; le numéro affiché indique le passage (1re ou 2e fois). [AUTO]

### US-VIS-07 — Utiliser l'application en portrait et en paysage

**En tant qu'**apprenant, **je veux** utiliser l'application en portrait ou en paysage, sur téléphone ou tablette **afin de** l'adapter à mon pupitre.

> **Priorité** M · **V/E/R** M/M/F · **Exigences** EF-VIS-12, ENF-COMP-03 · **Écran** tous · **Dépendances** US-VIS-03 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. Sur chaque profil (téléphone de référence ; émulateur 7, 10 et 13 pouces) et chaque orientation : aucun texte tronqué, toutes les commandes accessibles sans défilement horizontal, cibles ≥ 48 dp ; captures archivées. [ÉMU]
2. Une rotation hors séance conserve la position et la sélection. [AUTO]
3. Pendant une séance, l'orientation est verrouillée **sur téléphone** (arbitrage PO, CL-39) [TÉL] ; sur tablette, où Android ignore ce verrouillage pour les applications qui visent l'API 36, la séance continue sans interruption ni perte d'événement après une rotation ou un redimensionnement. [ÉMU]
4. Taille de police et taille d'affichage du système au maximum : aucun écran inutilisable ; écrans principaux vérifiés à 130 %. [ÉMU]
5. Vraies tablettes 7 et 13 pouces. [BÊTA]

### US-VIS-08 — Ouvrir rapidement une grande partition

**En tant qu'**apprenant, **je veux** qu'une partition s'ouvre vite, même longue **afin de** ne pas perdre de temps de travail.

> **Priorité** M · **V/E/R** M/M/M · **Exigences** ENF-PERF-05, ENF-RES-04 · **Écran** E11 · **Dépendances** US-VIS-01, US-QUA-04 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. Fichier de référence **P10** déjà importé : premier affichage en ≤ 1,5 s (médiane ; maximum ≤ 2,25 s). [MESURE]
2. Fichier **P30** (environ 750 mesures) : ouverture, lecture et pratique sans arrêt pour manque de mémoire, sur le téléphone et sur un émulateur configuré avec la mémoire minimale visée, **2 Go** (valeur de l'`architecte`). [MESURE]
3. Appareil d'entrée de gamme (processeur lent, non simulable). [BÊTA]
4. Partition qui ne peut plus être affichée (après une mise à jour du moteur) : message, « Réimporter », accès au rapport d'import. [AUTO]

---

## 7. EP-05 — Lecture audio de la partition (LEC)

### US-LEC-01 — Écouter la partition avec un son de piano

**En tant qu'**apprenant, **je veux** entendre la partition jouée avec un son de piano réaliste, même sans connexion **afin de** savoir comment le morceau doit sonner.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-LEC-01, ENF-PERF-10, ENF-OFF-01 · **Écran** E11 · **Dépendances** POC-04, US-CNF-03, [QO-10](09-decisions-a-prendre.md) · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. La lecture fonctionne en mode avion. [TÉL]
2. Le son commence **≤ 150 ms** après l'appui sur Lecture (95e centile). [MESURE]
3. Banque de sons légère embarquée, sous licence libre sans NC ni ND (décision [QO-10](09-decisions-a-prendre.md) ; exemple vérifié : « Upright Piano KW », CC0, variante *small* de 2,9 à 6,0 Mio selon le format), inscrite à l'inventaire avec son attribution ; une banque de meilleure qualité, téléchargeable, viendra plus tard. [REVUE]
4. Les 88 notes de la banque produisent un son au-dessus d'un seuil, sans écart de niveau > 6 dB [PO] entre deux notes voisines. [AUTO]
5. Le porteur juge le son acceptable pour le travail sur trois démonstrations, une gamme chromatique des 88 touches et un passage avec pédale (Chopin, Prélude op. 28 n°4). [PORTEUR]
6. Volume du téléphone à zéro ou mode silencieux : message « son coupé » au lancement de la lecture. [TÉL]

### US-LEC-02 — Commander la lecture

**En tant qu'**apprenant, **je veux** lancer, mettre en pause, arrêter et démarrer la lecture où je veux **afin d'**écouter le passage qui m'intéresse.

> **Priorité** M · **V/E/R** É/F/F · **Exigences** EF-LEC-02 · **Écran** E11 · **Dépendances** US-LEC-01, US-VIS-04 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. Lecture, pause (reprise au même endroit), arrêt (retour au début de la sélection ou du morceau). [TÉL]
2. En lecture (hors séance), un appui sur une mesure ou une note place le point de départ. [TÉL]
3. Commandes ≥ 48 × 48 dp. [ÉMU]
4. Perte du focus audio (appel, alarme, autre application qui joue) : lecture mise en pause ; sortie audio perdue : pause et message. [TÉL]

### US-LEC-03 — Régler le tempo

**En tant qu'**apprenant, **je veux** ralentir ou accélérer la lecture sans changer la hauteur des notes **afin d'**écouter un passage difficile lentement.

> **Priorité** M · **V/E/R** É/M/F · **Exigences** EF-LEC-03 · **Écran** E11 · **Dépendances** US-LEC-01 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. Tempo de 25 % à 150 % du tempo de référence, ou en battements par minute avec l'unité de pulsation affichée (« ♩. = 54 »). [TÉL]
2. Hauteur inchangée. [AUTO]
3. Un changement pendant la lecture s'applique au plus tard au temps suivant. [AUTO]
4. Changements de tempo écrits respectés proportionnellement. [AUTO]
5. Tempo par défaut selon l'[annexe B de la revue PED](revues/pedagogue-piano.md) : indication métronomique, sinon indication textuelle (Allegro → 132…), sinon chiffrage (2/4, 3/4, 4/4 → ♩ = 80 ; 6/8 → ♩. = 54…). Un tempo estimé est affiché comme tel (« tempo non indiqué, estimé à ♩ = 80 »). [AUTO]
6. L'utilisateur peut fixer le **tempo de référence** du morceau (celui que demande le professeur), mémorisé sur la fiche. [TÉL]

### US-LEC-04 — Écouter main par main

**En tant qu'**apprenant (Claire), **je veux** écouter chaque main séparément **afin de** comprendre chaque main avant de les assembler.

> **Priorité** M · **V/E/R** É/F/F · **Exigences** EF-LEC-04 · **Écran** E11 · **Dépendances** US-LEC-01 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. Main droite, main gauche, les deux. [TÉL]
2. Option : main non sélectionnée à volume réduit plutôt que coupée. [TÉL]
3. La main est déduite de la portée de la note (portée du haut = main droite) ; les textes « m.g. » et « m.d. » sont ignorés au MVP [à valider par PED]. Au corpus : main droite écrite sur la portée du bas. [AUTO]

### US-LEC-05 — Utiliser le métronome

**En tant qu'**apprenant, **je veux** un métronome réglable **afin de** travailler la régularité.

> **Priorité** M · **V/E/R** M/F/F · **Exigences** EF-LEC-05 · **Écran** E11, E13 · **Dépendances** US-LEC-01 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. Métronome activable, accentuation du premier temps, subdivision optionnelle, activée automatiquement sous 40 battements par minute. [AUTO]
2. Unité de battement selon le chiffrage (annexe B de PED) ; sans chiffrage, pas d'accentuation. [AUTO]
3. Son et volume réglables ; suit le tempo et les changements de chiffrage. [TÉL]
4. Métronome **visuel** : légère variation d'opacité ou de taille du curseur et d'un compteur de temps dans la barre du haut, jamais par-dessus la partition, jamais d'éclair ; au plus 3 changements par seconde (les subdivisions ne sont pas affichées au-delà) ; réglage Android « Supprimer les animations » respecté. [REVUE]

**Notes.** Au micro, le métronome sonore suit US-ECO-11.

### US-LEC-06 — Écouter un passage en boucle

**En tant qu'**apprenant (Marc), **je veux** faire jouer un passage en boucle **afin de** l'entendre plusieurs fois sans manipuler l'écran.

> **Priorité** M · **V/E/R** É/F/F · **Exigences** EF-LEC-06 · **Écran** E11 · **Dépendances** US-VIS-06, US-LEC-02 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. La plage sélectionnée (mesures, ou affinée au temps près par les poignées de US-VIS-06) est rejouée jusqu'à l'arrêt. [TÉL]
2. Pause optionnelle de 0 à 5 s entre deux répétitions. [AUTO]
3. Tempo, mains et métronome s'appliquent à la boucle. [TÉL]

### US-LEC-07 — Respecter les reprises simples

**En tant qu'**apprenant, **je veux** que la lecture respecte les barres de reprise **afin d'**entendre le morceau dans l'ordre où il se joue.

> **Priorité** M · **V/E/R** M/M/M · **Exigences** EF-LEC-07 · **Écran** E11 · **Dépendances** US-LEC-01, US-VIS-04 · **Incrément** I1 · **Statut** Ferme

**Critères d'acceptation**
1. Reprises simples (annexe A.1 de PED) : barre de fin avec sa barre de début ; barre de fin **sans** barre de début (retour à la dernière barre de début, sinon après la dernière reprise jouée, sinon au début) ; retour à l'**anacrouse** quand la section commence par une anacrouse ; fins « 1. / 2. » et « 1.-3. / 4. » ; barre de reprise au milieu d'une mesure si le fichier la représente par deux mesures partielles. [AUTO]
2. Lecture et curseur suivent l'ordre de jeu. [AUTO]
3. Option « ignorer les reprises » : chaque section jouée une fois, **les fins autres que la dernière sont sautées** ; s'applique à la lecture, au curseur et à l'analyse. [AUTO]
4. Passage sélectionné : reprises suivies seulement si leurs deux barres sont dans la sélection. [AUTO]
5. Exclus du MVP, signalés au rapport, lecture linéaire : D.C., D.S., Fine, Coda, reprises imbriquées, répétitions écrites en texte (« 3 fois »). Au corpus : reprise jouée trois fois, fins 1, 2 et 3, reprises imbriquées, avec l'ordre attendu ou le signalement. [AUTO]

---

## 8. EP-06 — Captation : MIDI USB, micro, calibrage (ECO)

### US-ECO-01 — Choisir la source de captation

**En tant qu'**apprenant, **je veux** choisir si l'application m'écoute par le câble MIDI ou par le micro **afin d'**utiliser le meilleur moyen selon mon piano.

> **Priorité** M · **V/E/R** É/F/F · **Exigences** EF-ECO-01 · **Écran** E03, E12, E19 · **Dépendances** — · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Source MIDI ou micro, mémorisée, modifiable dans les réglages et la préparation de séance ; avant le questionnaire de profil (I4), la source par défaut est le micro. [AUTO]
2. Étant donné un appareil MIDI branché, quand j'ouvre la préparation avec la source micro, alors l'application propose de passer en MIDI, sans obligation. [TÉL]
3. Source indisponible au lancement d'une séance (MIDI débranché, micro refusé ou muet) : message et proposition de l'autre source ou de la marche à suivre. [TÉL]

### US-ECO-02 — Connecter un piano numérique en USB

**En tant que** pianiste équipé d'un piano numérique (Claire), **je veux** brancher mon piano en USB **afin que** l'application reçoive exactement ce que je joue.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-ECO-02, ENF-FIA-04 · **Écran** E26 · **Dépendances** US-QUA-03 · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Réception de note activée, note désactivée, vélocité et pédale de sustain, sur tous les canaux ; pédale à mi-course traitée comme enfoncée au-delà de la moitié de sa course [PO]. [AUTO]
2. Branchement et débranchement détectés et signalés en < 2 s. [AUTO]
3. Débranchement pendant une séance : séance suspendue (curseur arrêté) ; les notes restées enfoncées sont relâchées et ne valident pas l'événement suivant ; reprise après rebranchement du même appareil, ou sur confirmation si c'est un autre appareil. [AUTO]
4. Plusieurs appareils branchés : l'utilisateur choisit (E26). [TÉL]
5. Transposition activée sur le piano (toutes les notes décalées d'un même intervalle) : décalage systématique détecté et signalé au lieu d'une série de fausses notes. [AUTO]
6. Clavier de 61 ou 76 touches : avertissement en préparation de séance si des notes du passage sont hors de son étendue (si l'appareil la déclare, sinon à la première note reçue hors étendue). [AUTO]
7. Ces critères sont vérifiés par simulation (US-QUA-03). La simulation ne traverse pas la couche USB (adaptateur, messages de maintien, vrais claviers) : vérification sur au moins 3 modèles de marques différentes, 0 note perdue sur 500, détection < 2 s. [BÊTA]
8. Les appareils qui communiquent en paquets MIDI universels (MIDI 2.0) ne sont pas pris en charge au MVP ; inscrit au registre des vérifications reportées ([05 §5](05-plan-de-release.md)). [BÊTA]

### US-ECO-03 — Autoriser le micro en connaissance de cause

**En tant que** parent d'un jeune pianiste (Nadia), **je veux** comprendre pourquoi l'application demande le micro et ce qu'elle en fait **afin d'**autoriser l'accès en confiance.

> **Priorité** M · **V/E/R** É/F/F · **Exigences** EF-ECO-05, ENF-CONF-01 · **Écran** E03 · **Dépendances** US-ECO-01 · **Incrément** I3 · **Statut** Ferme

**Critères d'acceptation**
1. L'accès au micro n'est demandé que lorsque l'utilisateur choisit la source micro, jamais à l'installation ; aucune autre autorisation (notifications, stockage) n'est demandée au MVP. [TÉL]
2. Avant la demande d'Android, l'écran d'explication affiche le texte du §8.1 ci-dessous, validé par `juriste-conformite`. [REVUE]
3. Après un refus ou « Pas maintenant » : « L'écoute au micro est désactivée. Vous pouvez toujours lire et écouter vos partitions, et jouer en MIDI. » [Continuer sans micro] ; l'application n'insiste pas ; le micro peut être choisi à nouveau dans Réglages > Captation. [TÉL]
4. Après deux refus, ou si Android n'affiche plus la demande, quand l'utilisateur choisit à nouveau le micro : guide vers les réglages d'Android (« Réglages > Applications > Piano Helper > Autorisations > Micro ») avec [Ouvrir les réglages] [Annuler]. [TÉL]
5. Autorisation « uniquement cette fois-ci » : à la séance suivante, bandeau court « Android redemande l'accès au micro, car vous l'aviez autorisé une seule fois. » [TÉL]
6. Autorisation retirée pendant une séance, ou réinitialisée par Android : le système ferme l'application ; au retour, la séance est enregistrée « interrompue » avec ce qui a été joué, et l'écran explique que le micro n'est plus autorisé (texte du CA3). [TÉL]

**§8.1 — Texte de l'écran d'explication** (fusion des propositions JUR §4.1 et UX §3.4, arbitrage du PO)

> **L'application a besoin d'entendre votre piano**
> - Pour savoir quelles notes vous jouez, elle écoute votre piano avec le micro de ce téléphone, pendant une séance ou le réglage du micro.
> - Le son est analysé **sur ce téléphone**. Il n'est **envoyé nulle part**.
> - Il est gardé en mémoire le temps de la séance, puis effacé. Il n'est **jamais enregistré** dans un fichier.
> - Le micro peut aussi capter les voix et les bruits de la pièce : ils ne sont ni enregistrés ni transmis.
> - Le micro est **coupé en dehors des séances** ; le voyant « Micro actif » l'indique.
> - Android va vous demander d'autoriser l'application à [« enregistrer des contenus audio »] : c'est le nom qu'Android donne à l'accès au micro. Choisissez [« Pendant l'utilisation de l'appli »].
>
> [Continuer] [Pas maintenant]

Les libellés entre crochets sont à relever par le porteur sur le téléphone de référence (action 2 du [fichier 09 §2.1](09-decisions-a-prendre.md)). La troisième puce est à aligner sur la décision d'architecture qui fixe où vit le son pendant la séance (ADR demandé à `architecte`, JUR-06). Version anglaise : [revue UX §3.4](revues/designer-ux.md).

### US-ECO-04 — Savoir quand le micro est actif

**En tant qu'**apprenant, **je veux** savoir à tout moment si le micro m'écoute **afin d'**être sûr qu'il ne m'écoute pas en dehors de mes séances.

> **Priorité** M · **V/E/R** É/F/F · **Exigences** EF-ECO-05, ENF-SEC-06, ENF-CONF-01 · **Écran** E13, E04, E28 · **Dépendances** US-ECO-03 · **Incrément** I3 · **Statut** Ferme

**Critères d'acceptation**
1. Voyant « Micro actif » (icône et texte, jamais une couleur seule) pendant toute ouverture du micro ; « Micro coupé » en pause longue. [TÉL]
2. Le micro n'est ouvert que pendant une séance (y compris une pause automatique de 30 s au plus, US-PRA-08) ou un réglage du micro ; il est fermé à la fin de la séance, en pause manuelle, en arrière-plan. [TÉL]
3. L'indicateur de confidentialité d'Android n'apparaît que pendant ces phases. [TÉL]
4. Dans les variantes bêta et publique, aucun son n'est écrit dans le stockage ; à la fin de l'analyse différée les tampons audio sont libérés ; après un arrêt forcé, aucun son ne subsiste (inspection). [ÉMU]
5. Micro autorisé mais muet (bascule « Accès au micro » d'Android coupée, micro occupé par un appel ou un assistant vocal) : silence anormal détecté en ≤ 3 s [PO] au début de la séance et expliqué. [TÉL]

### US-ECO-05 — Régler le micro

**En tant qu'**apprenant au piano acoustique (Lucas), **je veux** un réglage rapide du micro **afin que** l'application m'entende bien et tienne compte de mon piano.

> **Priorité** M · **V/E/R** É/M/É · **Exigences** EF-ECO-06 · **Écran** E24, E04 · **Dépendances** POC-01, POC-02, US-ECO-03 · **Incrément** I3 · **Statut** Ferme

**Critères d'acceptation**
1. Étapes : mise en place (pupitre ou ≤ 1,5 m), niveau d'entrée (trop faible, saturation), diapason, à partir de notes libres jouées au milieu du clavier ; l'écran avance seul dès que les notes sont détectées et se lit à 60 cm (textes ≥ 28 sp). [TÉL]
2. Un son émis par le téléphone et capté par son micro sert de **test de cohérence** des horodatages de captation (et non le jeu de l'utilisateur au clic) ; une correction de latence n'est appliquée que si ce test révèle un écart, sinon la même latence serait comptée deux fois ; le test est fait avant la première séance en jeu continu au micro, pas au premier lancement. [MESURE]
3. Durée < 60 s sur 5 réglages successifs au piano. [PIANO]
4. Exactitude : diapason à ± 3 cents [HYP] sur des sons de référence à 435, 440 et 446 Hz diffusés par un haut-parleur ; écart d'horodatage détecté à ± 10 ms. [MESURE]
5. Résultat enregistré pour l'appareil ; réglage relançable depuis les réglages et la préparation, qui indique « Micro · réglé » et signale un réglage ancien ou fait sur un autre téléphone. [TÉL]
6. Aucune note détectée après 15 s, ou bruit trop fort : conseil concret, [Recommencer] [Passer]. [TÉL]

### US-ECO-06 — Mesurer la latence MIDI (option avancée)

**En tant que** pianiste en MIDI, **je veux** pouvoir mesurer le décalage de mon installation **afin que** mon rythme ne soit pas jugé faux à cause du matériel.

> **Priorité** M · **V/E/R** M/F/M · **Exigences** EF-ECO-06 · **Écran** E27 · **Dépendances** US-ECO-02, US-QUA-03 · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Par défaut en USB, **aucune compensation** : le décalage constant du joueur est déjà neutralisé par l'analyse (US-ANA-05 CA2). [AUTO]
2. Option avancée (Réglages > Audio), absente du premier lancement : jouer une note sur chacun de 8 clics ; résultat refusé si l'écart-type dépasse 25 ms [PO] ou si moins de 6 notes sont reçues ; compensation bornée à 30 ms [PO]. [AUTO]
3. Durée < 1 minute. [TÉL]
4. Avec des entrées simulées affectées d'une latence connue, la latence est retrouvée à ± 10 ms. [AUTO]
5. Débranchement pendant le test : test annulé, ancienne valeur conservée. [AUTO]
6. Sur de vrais pianos : latence mesurée ≤ 30 ms [PO]. [BÊTA]

### US-ECO-07 — Être écouté au micro en mode attente

**En tant qu'**apprenant au piano acoustique (Lucas, le porteur), **je veux** que l'application reconnaisse au micro les notes que je joue en mode attente **afin de** pratiquer avec mon piano droit.

> **Priorité** M · **V/E/R** É/É/É · **Exigences** EF-ECO-04, EF-ANA-01, ENF-PERF-01, ENF-QA-01 · **Écran** E13 · **Dépendances** **POC-01 concluant en mode attente**, POC-02, J1, US-ECO-05, US-ANA-02, US-PRA-02, US-QUA-05 · **Incrément** I3 · **Statut** Ferme

**Critères d'acceptation**
1. Bonne note ou bon accord : curseur avancé en **≤ 150 ms** [HYP] depuis l'attaque acoustique, au 95e centile ; si seul le grave dépasse alors que le médium tient la cible, cible propre au grave fixée au vu des mesures de POC-01 (décision [QO-55](../DAT/14-decisions-a-prendre.md)). [MESURE]
2. **Validations indues** (le curseur avance sans que l'événement attendu ait été joué) ≤ 1 % [PO] ; **non-validations** (événement joué juste non validé en ≤ 150 ms) ≤ 5 % [HYP] ; **fausses erreurs** ≤ 5 % [HYP] : mesurés par le banc événement par événement (chaque tentative enregistrée est soumise au détecteur avec au moins 3 s du son qui la précède) sur le corpus micro, les validations indues sur le lot de tentatives fausses (US-QUA-01) ; un taux est tenu si sa valeur est ≤ cible et si la borne haute de l'intervalle de Wilson à 95 % est ≤ 1,5 × cible [BANC], et sur au moins 20 séances réelles du porteur (≥ 1 000 notes) capturées par US-QUA-05 et annotées à la réécoute ; résultat valable pour cet appareil et ce piano (R-15). [PIANO]
3. Résonance des notes précédentes (pédale, notes tenues) : ni fausse erreur, ni avance indue. [BANC]
4. Détection incertaine : l'application attend au lieu de signaler une erreur. **Bénéfice du doute** : si une attaque incertaine compatible avec la note attendue est détectée 2 fois de suite, ou 2 fois dans les 5 s, le curseur avance ; la note est marquée « incertaine » (hors score). [BANC]
5. Une **erreur d'octave** n'est signalée qu'avec un seuil de confiance renforcé, dès le sol 2 et en dessous (valeur fixée avec `expert-audio-mir`) ; en dessous de ce seuil, l'application attend. [BANC]
6. Notes des registres extrêmes (bornes définitives fixées par octave après POC-01) : confiance réduite, aucune fausse erreur. [BANC]
7. CA1 à CA3 mesurés avec le téléphone sur le pupitre et à 1,5 m. [PIANO]
8. Bruits mécaniques (pédale, couvercle, page tournée), voix : aucune note détectée (sous-corpus dédié). [BANC]
9. Écouteurs avec micro branchés : la captation reste sur le micro du téléphone, ou l'application l'indique. [TÉL]
10. Source audio utilisée documentée (traitements « voix » du système désactivés) ; résultats au corpus avec et sans ces traitements. [BANC]
11. Autres appareils et autres pianos. [BÊTA]

### US-ECO-08 — Être écouté au micro en jeu continu

**En tant qu'**apprenant au piano acoustique, **je veux** jouer un passage en continu et être analysé au micro **afin de** travailler aussi le rythme sur mon piano.

> **Priorité** M · **V/E/R** É/É/É · **Exigences** EF-ECO-04, EF-PRA-03, ENF-PERF-02, ENF-PERF-04, ENF-QA-01 · **Écran** E12, E13, E14 · **Dépendances** **POC-01 concluant en jeu continu**, J1, US-ECO-07, US-PRA-04, [QO-29](09-decisions-a-prendre.md), [QO-43](09-decisions-a-prendre.md) · **Incrément** I5 · **Statut** Conditionnelle

**Critères d'acceptation**
1. Fausses erreurs de **notes** ≤ 5 % [HYP] sur le corpus micro ; fausses erreurs de **rythme** ≤ 5 % [HYP] sur le sous-corpus d'attaques annotées à la main (US-QUA-01). [BANC]
2. L'analyse différée avance pendant le jeu, au moins aussi vite que le jeu ; le compte-rendu d'un passage de 5 minutes s'affiche en ≤ 2 s (médiane) après la fin, avec « Analyse en cours… » et progression au-delà de 2 s. Le délai est mesuré en I5 ; au vu de la mesure, le porteur garde 2 s ou accepte jusqu'à 10 s avec progression (décision [QO-43](09-decisions-a-prendre.md)). [MESURE]
3. Retour en direct « aucun » par défaut au micro ; s'il est activé, il respecte ENF-PERF-02. [MESURE]
4. Tolérance rythmique et fenêtre d'accord au micro calculées par registre selon la formule de [EDB 06 §4.1](../EDB/06-analyse-du-jeu-et-erreurs.md) (décision [QO-32](09-decisions-a-prendre.md), précisée le 2026-11-08) : T_eff = max(T ; 2σ ; u), W_eff = max(W ; 2√2·σ) ; marque individuelle de rythme seulement à 1,5 fois la tolérance. [BANC]
5. La fonction est marquée « expérimental » (décision [QO-29](09-decisions-a-prendre.md)), décidée par le porteur au vu du rapport de POC-01 relu par `qa-testeur` et `architecte`. [TÉL] Revérifiée en bêta sur d'autres appareils. [BÊTA]

**Notes.** Si POC-01 n'est pas concluant en jeu continu, la story passe en V1 (US-PRV-01).

### US-ECO-09 — Jouer sur un piano un peu désaccordé

**En tant qu'**apprenant dont le piano n'a pas été accordé récemment, **je veux** que l'application tolère un piano un peu trop haut ou trop bas **afin de** ne pas recevoir de fausses erreurs.

> **Priorité** S · **V/E/R** É/M/M · **Exigences** EF-ECO-10 · **Écran** E04 · **Dépendances** US-ECO-05, POC-01, [QO-23](09-decisions-a-prendre.md) · **Incrément** I3 · **Statut** Ferme

**Critères d'acceptation**
1. Sur le sous-corpus micro transposé de −20 et +24 cents (La = 435 et 446 Hz), le taux de fausses erreurs dépasse d'au plus 0,5 point [PO] celui du corpus d'origine. [BANC]
2. Tolérance supplémentaire de ± 30 cents [HYP] par note, plus large dans les deux octaves extrêmes (accord étiré), valeur fixée avec `expert-audio-mir` ; plage étendue vers 430 Hz si POC-01 le permet. [BANC]
3. Désaccord global au-delà du seuil fixé : signalé au réglage du micro avec un conseil d'accordage, sans bloquer. [TÉL]
4. Unisson désaccordé ou touche muette : signalé comme problème de captation, pas comme note manquée répétée. [BANC]

### US-ECO-10 — Être averti d'un mauvais signal

**En tant qu'**apprenant au micro, **je veux** savoir pendant le jeu si l'application m'entend mal **afin de** corriger la situation.

> **Priorité** S · **V/E/R** M/F/F · **Exigences** EF-ECO-07 · **Écran** E13 · **Dépendances** US-ECO-05, [QO-23](09-decisions-a-prendre.md) · **Incrément** I3 · **Statut** Ferme

**Critères d'acceptation**
1. Indicateur discret : niveau trop faible, saturation, bruit ambiant excessif. Sur les enregistrements étiquetés « bruit », « saturé », « trop faible », il s'allume dans ≥ 90 % des cas ; sur les enregistrements normaux, dans ≤ 5 % [PO]. [BANC]
2. Il ne masque jamais la partition à venir et ne demande aucune interaction. [TÉL]

### US-ECO-11 — Ne pas être jugé sur les sons de l'application

**En tant qu'**apprenant au micro, **je veux** que le métronome, le décompte ou l'autre main jouée par l'application ne soient pas pris pour mon jeu **afin de** ne pas recevoir de fausses erreurs.

> **Priorité** S · **V/E/R** É/M/É · **Exigences** EF-ECO-08, EF-PRA-02, EF-PRA-03, EF-PRA-05 · **Écran** E12, E13 · **Dépendances** POC-01, US-LEC-05, US-PRA-03, [QO-23](09-decisions-a-prendre.md) · **Incrément** I3 · **Statut** Ferme

**Critères d'acceptation** (forme minimale)
1. Au micro, métronome et décompte **visuels** par défaut (US-LEC-05 CA4). [TÉL]
2. Le métronome sonore et l'autre main jouée par l'application ne sont disponibles au micro qu'après un avertissement recommandant des **écouteurs filaires** ; choix mémorisé. Le son d'erreur est indisponible au micro (arbitrage PO). [TÉL]
3. Sortie audio Bluetooth détectée : avertissement de latence ; en jeu continu, le métronome sonore est désactivé tant que la latence de sortie n'est pas compensée (question transmise à `architecte`). [TÉL]
4. Sur des enregistrements où le métronome sort par le haut-parleur, aucun clic n'est compté comme une note jouée. [BANC]
5. Notification, sonnerie ou vibration pendant une séance : aucune note « en trop » ; au début de la séance, l'application propose seulement d'ouvrir le réglage « Ne pas déranger » du système (le modifier elle-même exigerait une permission supplémentaire). [BANC]

---

## 9. EP-07 — Moteur d'analyse du jeu (ANA)

> Règles de l'[EDB 06](../EDB/06-analyse-du-jeu-et-erreurs.md) (v0.6, qui intègre les précisions de la revue PED : [10](10-modifications-edb-proposees.md), MOD-01 à MOD-15). Les valeurs sont des paramètres. Chaque story est relue par PED (règles) et `expert-audio-mir` (faisabilité) et vérifiée par le banc (US-QUA-02).

### US-ANA-01 — Construire le déroulé attendu

**En tant qu'**apprenant, **je veux** que l'application sache exactement ce que je dois jouer dans le passage choisi **afin que** mon jeu soit comparé à ce qui est écrit.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-ANA-02, EF-ANA-10 · **Écran** — · **Dépendances** POC-05, US-IMP-01, US-LEC-07 · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Suite ordonnée d'événements attendus (notes qui commencent au même instant), avec position, instant théorique, hauteurs, main (déduite de la portée), durée écrite, nature, voix. [AUTO]
2. Plage, mains choisies et reprises simples appliquées dans le même ordre que la lecture (US-LEC-07), y compris l'option « ignorer les reprises ». [AUTO]
3. Note liée non attendue comme nouvelle attaque, y compris à travers une barre, un changement de ligne ou vers une fin de reprise ; liaison « laissez vibrer » ignorée. [AUTO]
4. Unisson entre voix ou entre mains : une seule attaque attendue. [AUTO]
5. Indications d'octave appliquées aux hauteurs attendues ; notes cachées et notes « cue » non attendues ; trémolo écrit non évalué au MVP. [AUTO]
6. Voix multiples et retours en arrière dans la mesure : nombre d'événements conforme à des références établies à la main pour au moins 10 pièces. [AUTO]
7. Passage sans aucune note (mesures de silence) : aucune séance n'est proposée. [AUTO]

### US-ANA-02 — Obtenir les événements joués

**En tant qu'**apprenant, **je veux** que mon jeu soit converti fidèlement en notes **afin que** l'analyse parte de ce que j'ai réellement joué.

> **Priorité** M · **V/E/R** É/É/É · **Exigences** EF-ANA-01, ENF-CONF-01 · **Écran** — · **Dépendances** US-ECO-02 ; POC-01, US-ECO-05 (micro) · **Incrément** I2 / I3 · **Statut** Ferme

**Critères d'acceptation**
1. MIDI : un événement par note (hauteur exacte, début et fin compensés, vélocité, pédale), confiance 1 ; notes reçues = notes émises par le simulateur (aucune perte). [AUTO]
2. Micro : un événement par note détectée (hauteur, début, intensité relative, confiance de 0 à 1) ; la fin des notes n'est pas utilisée pour juger. (I3) [BANC]
3. Micro : détection informée par la partition, selon l'approche recommandée par POC-01. (I3) [REVUE]
4. Aucun signal audio conservé après traitement dans les variantes bêta et publique. (I3) [ÉMU]

### US-ANA-03 — Aligner le jeu sur la partition

**En tant qu'**apprenant qui hésite et recommence parfois, **je veux** que chaque note jouée soit rattachée à la bonne note de la partition **afin que** mes erreurs soient bien localisées.

> **Priorité** M · **V/E/R** É/É/É · **Exigences** EF-ANA-02 · **Écran** — · **Dépendances** **POC-05 concluant**, US-ANA-01, US-ANA-02 · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Une seule note fausse au milieu d'un passage : toutes les autres notes sont alignées sur leur note attendue. [BANC]
2. Note oubliée : « manquée », sans décalage de la suite ; mesure entière sautée : notes « manquées », sans décalage. [BANC]
3. Retour en arrière (2 mesures, début de la phrase de 4 mesures, début du passage, début du morceau) : toutes les notes jouées à partir de la 3e mesure qui suit le point de reprise sont alignées sur la bonne note ; les notes rejouées ne sont pas « en trop ». [BANC]
4. Sous-corpus « hésitant » (pauses jusqu'au seuil d'arrêt, tempo à ± 20 %) : exactitude d'alignement ≥ 98 % en MIDI. [BANC]
5. Exactitude d'alignement ≥ 98 % sur le corpus MIDI, ≥ 90 % [HYP] sur le corpus micro. [BANC]
6. Notes jouées après la fin du passage : ignorées, sans « en trop ». [BANC]

### US-ANA-04 — Classer les erreurs de notes

**En tant qu'**apprenant, **je veux** savoir quelles notes sont justes, fausses, oubliées ou en trop **afin de** corriger mes erreurs de lecture.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-ANA-03, ENF-QA-01 · **Écran** — · **Dépendances** US-ANA-03 · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Note attendue : juste, fausse ou manquée ; note jouée non associée : en trop ; note liée rejouée : rejouée (sévérité faible). [BANC]
2. Sous-types d'une note fausse : altération omise (armure ou accidentelle), **bécarre omis**, **altération reportée** au-delà de la barre, altération ajoutée, erreur d'octave, demi-ton voisin, autre. Exemple : *Für Elise* mes. 1, ré♮ attendu après ré♯, ré♯ joué → « bécarre omis ». [AUTO]
3. Main droite seule choisie mais les deux mains jouées : les notes jouées qui correspondent à la main non travaillée ne sont pas comptées « en trop » (règle du PO, à valider par PED). [BANC]
4. Corpus MIDI avec erreurs injectées : fausses erreurs ≤ 1 % ; **précision des erreurs** (même type, même note attendue) ≥ 95 % [PO] ; **rappel des erreurs** ≥ 90 % [PO]. Au micro : précision des erreurs ≥ 85 % [PO], rappel mesuré sans seuil. [BANC]

### US-ANA-05 — Évaluer le rythme et la continuité

**En tant qu'**apprenant (Marc), **je veux** savoir où je joue en avance, en retard ou où j'hésite **afin de** corriger mon rythme.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-ANA-04 · **Écran** — · **Dépendances** US-ANA-03, US-ANA-07 · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. En jeu continu, une note juste dont l'attaque s'écarte de l'instant attendu de plus de T (plafonnée en fraction de la durée de la note) est « en avance » ou « en retard ». [BANC]
2. Compensation du décalage global : étant donné un jeu MIDI simulé juste, décalé de +80 ms constants, en exigeant (T = 50 ms), alors aucune note n'est « en retard » ; étant donné le même jeu avec un décalage qui croît de 0 à +200 ms, alors des notes « en retard » apparaissent. [AUTO]
3. Hésitation et arrêt sont mesurés comme le **retard de l'attaque sur son instant attendu**, après la durée écrite de l'événement précédent (notes tenues et silences compris) ; arrêt = retard > max(seuil du niveau ; 2 temps au tempo de la séance). Une ronde lente tenue n'est pas un arrêt, à 25 % comme à 150 % du tempo. [BANC]
4. Après chaque hésitation, arrêt ou retour en arrière, la référence temporelle et le curseur se recalent sur le joueur : un arrêt de 3 s suivi d'un jeu parfait ne produit aucun « en retard » après la reprise. [BANC]
5. La note qui suit une hésitation ou un arrêt n'est pas comptée en plus « en retard ». [BANC]
6. Retour en arrière : pas une erreur de notes. Mode attente : rythme non évalué. [BANC]

### US-ANA-06 — Évaluer les accords

**En tant qu'**apprenant, **je veux** que mes accords soient reconnus même si mes doigts ne tombent pas exactement ensemble **afin de** ne pas être pénalisé pour une simultanéité normale.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-ANA-06, EF-PRA-02 · **Écran** — · **Dépendances** US-ANA-03 · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Notes jouées dans la fenêtre de simultanéité du niveau (80 ms en standard [HYP]) : simultanées. [BANC]
2. Mode attente : accord validé quand toutes ses notes sont enfoncées dans la fenêtre, ou déjà tenues ; une note liée d'un accord n'a pas à être rejouée. [AUTO]
3. Jeu continu : écart supérieur à la fenêtre → « accord non synchronisé », en distinguant le décalage entre mains du décalage dans une main (main déduite de la note attendue associée). [BANC]
4. Accord marqué arpégé (y compris sur les deux portées) : fenêtre = max(fenêtre du niveau ; 60 ms × (nombre de notes − 1)), plafonnée à la moitié de la durée écrite ; référence rythmique = première note entendue ; ordre non sanctionné ; accord arpégé joué plaqué : juste. [BANC]
5. Accord de plus d'une octave dans une main : fenêtre d'arpège appliquée automatiquement. Accord roulé naturellement par un débutant : en indulgent, pas d'« accord non synchronisé » dans la fenêtre du niveau. [BANC]

### US-ANA-07 — Appliquer les tolérances et les cas particuliers

**En tant qu'**apprenant, **je veux** que l'analyse applique des tolérances adaptées à mon niveau et aux situations musicales particulières **afin de** ne pas être sanctionné à tort.

> **Priorité** M · **V/E/R** É/M/É · **Exigences** EF-ANA-08, EF-ANA-09, RG-04 · **Écran** — · **Dépendances** US-ANA-03 · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Paramètres des trois niveaux ([06 §4.1](../EDB/06-analyse-du-jeu-et-erreurs.md)) dans une configuration unique, avec les valeurs de l'EDB par défaut ; le niveau est figé et enregistré avec la séance (RG-04). [AUTO]
2. **Points d'orgue, césures** : le curseur attend l'attaque suivante ; la référence temporelle repart de cette attaque ; l'événement suivant n'est pas évalué en rythme. **Rit., rall., allarg., accel.** : rythme non évalué jusqu'au prochain « a tempo » ou changement de tempo, hauteurs évaluées. **Dernier événement** : seule l'attaque est évaluée. Décision [QO-40](09-decisions-a-prendre.md). [BANC]
3. **Ornements** (trille, mordant, gruppetto) : non évalués ; l'événement orné est validé (attente) ou juste (continu) si l'on entend, dans sa durée écrite, la note principale ou sa voisine diatonique supérieure ou inférieure ; les autres notes de l'ornement, y compris les terminaisons écrites, ne sont ni fausses ni en trop ; un ornement omis n'est pas une erreur. [BANC]
4. **Petites notes** : après une petite note, la note principale est jugée avec une tolérance élargie jusqu'à la moitié de sa durée ; une petite note absente est signalée « manquée (mineure) » sur la partition annotée, hors du score ; en mode attente, une petite note jouée avec la note principale est acceptée ; petites notes avant le premier temps : pas d'erreur de rythme. [BANC]
5. **Notes répétées rapides** : chaque répétition associée à la bonne occurrence ; au micro, confiance réduite. **Pédale au micro** : la résonance ne crée pas de notes « en trop ». **Notes extrêmes au micro** : seuil de signalement relevé. [BANC]
6. Pièces à indication « Swing » : rythme non évalué au MVP. [BANC]
7. Chaque cas particulier a au moins un cas de test au corpus. [BANC]

**Notes.** L'évaluation des ornements est en V1 (US-ANX-04).

### US-ANA-08 — Ne signaler que les erreurs sûres

**En tant qu'**apprenant, **je veux** que l'application ne me signale une erreur que si elle en est sûre **afin de** garder confiance dans ses retours.

> **Priorité** M · **V/E/R** É/M/É · **Exigences** ENF-QA-01 · **Écran** — · **Dépendances** US-ANA-03, US-QUA-02 · **Incrément** I2 / I3 · **Statut** Ferme

**Critères d'acceptation**
1. Chaque écart porte une confiance de 0 à 1 (captation et qualité de l'alignement local ; en MIDI, seule la seconde). [AUTO]
2. Sous le seuil (0,7 par défaut [HYP]) : pas d'erreur affichée, pas d'effet sur le score ; visible comme « incertain » seulement avec l'option « Afficher les détections incertaines » (US-RES-04). [AUTO]
3. Fausses erreurs ≤ 1 % en MIDI (I2) et ≤ 5 % [HYP] au micro (I3), avec intervalle de confiance à 95 % publié. [BANC]
4. La proportion d'écarts incertains de chaque séance est calculée et enregistrée. [AUTO]

### US-ANA-09 — Analyser en temps réel puis en différé

**En tant qu'**apprenant, **je veux** un retour immédiat pendant que je joue et un compte-rendu plus précis à la fin **afin d'**avoir à la fois réactivité et justesse.

> **Priorité** M · **V/E/R** É/É/M · **Exigences** EF-ANA-13, ENF-PERF-01, ENF-PERF-02, ENF-PERF-04 · **Écran** — · **Dépendances** US-ANA-03, US-QUA-04, [QO-52](../DAT/14-decisions-a-prendre.md) · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Mode attente et retour en direct : analyse temps réel dans les latences d'ENF-PERF-01 et 02 (95e centile). [MESURE]
2. En jeu continu, le compte-rendu utilise l'analyse complète a posteriori, qui fait foi. En mode attente (décision [QO-52](../DAT/14-decisions-a-prendre.md)), il reprend les décisions prises pendant le jeu ; l'analyse différée y est facultative et peut seulement rendre une décision « incertaine » (hors score), jamais créer une erreur. [AUTO]
3. Erreur affichée en direct puis annulée par l'analyse différée : absente du compte-rendu, sans mise en avant. En mode attente, une note validée pendant le jeu n'est jamais déclarée fausse dans le compte-rendu. [AUTO]
4. Passage de 5 minutes en MIDI : compte-rendu en ≤ 2 s (médiane ; maximum ≤ 3 s). [MESURE]
5. Échec de l'analyse : séance enregistrée « non analysée », analyse relançable. [AUTO]

---

## 10. EP-08 — Pratique : modes attente et jeu continu (PRA)

### US-PRA-01 — Préparer une séance

**En tant qu'**apprenant, **je veux** choisir en un écran le passage, les mains, le mode et le tempo **afin de** commencer à jouer vite avec les bons réglages.

> **Priorité** M · **V/E/R** É/M/F · **Exigences** EF-PRA-01 · **Écran** E12 · **Dépendances** US-VIS-06, US-ECO-01 · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Toujours visibles : passage (tout, ou « De la mesure … à … »), mains, mode (attente, jeu continu), tempo (jeu continu seulement). Sous « Plus d'options » : retour en direct (jeu continu), métronome, décompte, autre main jouée par l'application, « Répéter » (jeu continu, US-PRA-09). [TÉL]
2. Affichés : source de captation et son état (« Micro · réglé »), niveau d'exigence (modifiable), avertissement d'import « peut fausser l'analyse », calibrage ancien ou fait sur un autre téléphone. [TÉL]
3. « Commencer » reste fixé en bas, visible en paysage sur le téléphone. [ÉMU]
4. Les derniers réglages du morceau sont proposés par défaut. [AUTO]
5. Option indisponible : grisée avec sa raison (exemple : jeu continu au micro sans US-ECO-08) ; le jeu continu au micro porte la mention « expérimental » (décision [QO-29](09-decisions-a-prendre.md)). [TÉL]
6. Passage sans aucune note : « Commencer » indisponible avec explication. [AUTO]

### US-PRA-02 — Pratiquer en mode attente

**En tant que** débutante (Claire), **je veux** que la partition attende que je joue la bonne note avant d'avancer **afin d'**apprendre les notes à mon rythme.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-PRA-02, ENF-PERF-01 · **Écran** E13 · **Dépendances** US-ANA-03, US-ANA-06, US-RES-02 · **Incrément** I2 / I3 · **Statut** Ferme

**Critères d'acceptation**

```gherkin
Étant donné le mode attente, main droite seule, et le curseur sur la première note
Quand je joue la note attendue
Alors le curseur passe à l'événement suivant en 100 ms au plus en MIDI (150 ms au micro, I3), au 95e centile

Étant donné le curseur sur un mi attendu
Quand je joue un fa
Alors, en MIDI, la fausse note est signalée en 100 ms au plus
Et, au micro, elle est signalée en 150 ms au plus si sa confiance atteint le seuil, sinon rien n'est signalé
Et le curseur ne bouge pas

Étant donné le curseur sur un accord de trois notes
Quand j'enfonce les trois notes dans la fenêtre de simultanéité du niveau
Alors l'accord est validé et le curseur avance
```
[AUTO] [MESURE]

1. Notes tenues qui se prolongent : ne bloquent pas la progression. [AUTO]
2. Rythme non évalué ; en main seule, l'autre main n'est pas attendue. [AUTO]
3. Une note en trop jouée dans la fenêtre de simultanéité avec la ou les notes attendues n'empêche pas d'avancer **si elle est à 1 ou 2 demi-tons d'une note attendue et seule de son espèce dans cette main** ; elle est comptée « en trop ». Au-delà (plusieurs notes en trop, ou note éloignée), l'événement n'est pas validé et « Jouez seulement la ou les notes indiquées » s'affiche. Exemple : mi attendu ; mi + fa → avance ; do-ré-mi-fa-sol → n'avance pas. [AUTO]
4. Une note attendue est **juste si elle est trouvée du premier coup**, sinon fausse ; une seule erreur par événement quel que soit le nombre d'essais ; les essais faux ne sont pas comptés en plus « en trop » ; le nombre d'essais est conservé. [AUTO]
5. Le son d'erreur est désactivé par défaut et indisponible au micro. [TÉL]
6. Reprendre après une pause : jouer la note attendue reprend la séance. [TÉL]

### US-PRA-03 — Entendre l'autre main en mode attente

**En tant qu'**apprenant qui travaille une seule main, **je veux** que l'application joue l'autre main au fur et à mesure **afin d'**entendre le résultat complet.

> **Priorité** M · **V/E/R** M/M/M · **Exigences** EF-PRA-02, EF-LEC-04 · **Écran** E12, E13 · **Dépendances** US-PRA-02, US-LEC-04, US-ECO-11 · **Incrément** I3 · **Statut** Ferme

**Critères d'acceptation**
1. Option « autre main jouée par l'application » en mode attente quand une seule main est travaillée. [TÉL]
2. Après la validation d'un événement, l'application joue les notes de l'autre main **au tempo de la séance** jusqu'à l'instant du prochain événement de la main travaillée, puis attend. Au corpus : Mozart K. 545, mes. 1 à 4 (basse d'Alberti). [AUTO]
3. En MIDI, l'option est disponible sans restriction. [AUTO]
4. Au micro, elle suit US-ECO-11 : disponible seulement avec des écouteurs filaires (décisions [QO-23](09-decisions-a-prendre.md) et [QO-24 b](09-decisions-a-prendre.md)). [TÉL]
5. Avec un vrai piano numérique. [BÊTA]

### US-PRA-04 — Pratiquer en jeu continu

**En tant qu'**apprenant, **je veux** jouer un passage au tempo choisi sans être arrêté **afin de** travailler la continuité et le rythme.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-PRA-03 · **Écran** E13 · **Dépendances** US-ANA-05, US-RES-01, US-PRA-05 · **Incrément** I2 (MIDI) / I5 (micro, si US-ECO-08) · **Statut** Ferme

**Critères d'acceptation**
1. Le curseur avance au tempo choisi ; hauteurs et rythme analysés. [AUTO]
2. Le jeu n'est jamais interrompu à cause d'une erreur. [AUTO]
3. Métronome activable pendant le jeu (sonore en MIDI ; au micro selon US-ECO-11). [TÉL]
4. Pendant la séance, les appuis sur la partition sont ignorés ; seules les commandes réagissent (US-PRA-07). [TÉL]
5. Au micro (I5) : comportement de US-ECO-08. [PIANO]

### US-PRA-05 — Démarrer sur un décompte

**En tant qu'**apprenant, **je veux** démarrer au bon tempo sans toucher l'écran **afin de** garder les mains sur le clavier.

> **Priorité** M · **V/E/R** M/F/F · **Exigences** EF-PRA-05 · **Écran** E13 · **Dépendances** US-LEC-05 · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Par défaut en jeu continu : « Jouez une note pour lancer le décompte » ; n'importe quelle note convient (MIDI et micro), elle n'est pas analysée. Option : décompte lancé par « Commencer », précédé d'un délai de 3 s. Faisabilité au micro confirmée par `expert-audio-mir`. [TÉL]
2. Le décompte dure au moins 3 pulsations : 1 mesure par défaut, 2 mesures en 2/4, 6/8 et 3/8 ; réglable de 1 à 2 mesures. [AUTO]
3. Toujours visuel (chiffres ≥ 32 sp dans la barre du haut, jamais par-dessus la partition) ; sonore en MIDI, et au micro selon US-ECO-11. [TÉL]
4. Anacrouse : le décompte compte les mesures demandées puis continue sur les temps qui précèdent l'anacrouse ; la première note tombe à sa place, jamais sur un « 1 » ajouté. Exemples de test : *Berceuse* de Brahms (3/4) → « 1-2-3 | 1-2 », jeu sur « 3 » ; *Für Elise* (3/8) → « 1-2-3 | 1-2 », jeu sur « 3 ». Anacrouse sur une subdivision : subdivision indiquée au dernier temps. [AUTO]
5. Petites notes avant le premier temps : le décompte se termine avant elles. [AUTO]
6. Décompte aussi en boucle (US-PRA-09). [AUTO]

### US-PRA-06 — Reprendre après une interruption

**En tant qu'**apprenant, **je veux** qu'un appel ou une notification ne fasse pas planter ma séance **afin de** reprendre où j'en étais.

> **Priorité** M · **V/E/R** M/M/M · **Exigences** ENF-FIA-04 · **Écran** E28 · **Dépendances** US-PRA-02, US-PRA-04, US-ECO-04 · **Incrément** I3 · **Statut** Ferme

**Critères d'acceptation**
1. Appel, application plus visible (passage en arrière-plan, écran éteint), perte du focus audio, perte de la position d'activité principale (écran partagé, fenêtre flottante) : séance suspendue (curseur arrêté, micro fermé), sans plantage ni perte ; un volet de notifications ou une fenêtre transparente ne suspendent pas la séance. Vérifié sur l'émulateur (appel simulé) [ÉMU] et sur le téléphone (mise en arrière-plan) [TÉL].
2. Au retour (E28) : « reprendre une mesure avant » (par défaut en jeu continu, avec décompte), « reprendre à cette mesure », « recommencer », « voir le compte-rendu » ; cibles ≥ 64 dp, texte ≥ 28 sp. [TÉL]
3. Séance non reprise (l'utilisateur quitte l'écran de séance, ou le processus est arrêté par le système) : enregistrée « interrompue » avec ce qui a été joué. [AUTO]
4. L'écran ne se met pas en veille pendant une séance. [TÉL]

### US-PRA-07 — Terminer une séance

**En tant qu'**apprenant, **je veux** que la séance se termine d'elle-même à la fin du passage ou quand je le décide **afin d'**obtenir mon compte-rendu.

> **Priorité** M · **V/E/R** É/F/F · **Exigences** EF-RES-03, EF-PRA-08 · **Écran** E13 · **Dépendances** US-PRA-02, US-PRA-04 · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Attente : la séance se termine quand le dernier événement est joué. Jeu continu : après la durée écrite du dernier événement plus 1 s, ou dès le relâchement des touches en MIDI. [AUTO]
2. « Pause » et « Terminer » accessibles à tout moment : cibles ≥ 64 dp, dans les coins, hors de la partition, espacées d'au moins 16 dp. [ÉMU]
3. Terminer avant la fin : compte-rendu limité à la partie jouée (« Séance interrompue à la mesure 12 »). [AUTO]
4. Notes jouées après la fin du passage : ignorées. [AUTO]
5. Aucune note jouée : pas de score, message « Aucune note entendue… » ; la séance n'est pas enregistrée dans l'historique (arbitrage PO). [AUTO]

### US-PRA-08 — S'arrêter et reprendre sans toucher l'écran

**En tant qu'**apprenant aux mains sur le clavier (Lucas), **je veux** que l'application détecte que j'ai arrêté de jouer et reprenne quand je rejoue **afin de** ne pas avoir à toucher l'écran.

> **Priorité** S · **V/E/R** É/F/M · **Exigences** EF-PRA-08, ENF-ACC-01 · **Écran** E13, E28 · **Dépendances** US-PRA-07, [QO-25](09-decisions-a-prendre.md) · **Incrément** I2 (MIDI) / I3 (micro) · **Statut** Ferme

**Critères d'acceptation** (valeurs : décision [QO-25](09-decisions-a-prendre.md), [EDB 06 §4.1](../EDB/06-analyse-du-jeu-et-erreurs.md))
1. Jeu continu : sans nouvelle attaque pendant le délai de pause (seuil d'arrêt + 3 s : 7 / 6 / 5 s en indulgent / standard / exigeant), la séance passe en **pause** : curseur figé sur la dernière position jouée, message « En pause — reprenez quand vous voulez ». Au micro, « silence » = aucune nouvelle attaque, pas absence de son. [AUTO]
2. Pendant la pause (30 s au plus), le micro reste ouvert, voyant allumé ; **jouer une note** relance le décompte et la reprise au début de la mesure précédente ; l'interruption compte pour un arrêt, quelle que soit sa durée. [AUTO]
3. Après 30 s de pause (réglable de 15 à 60 s), la séance se termine et le compte-rendu s'affiche. [AUTO]
4. Mode attente : aucune pause avant 2 minutes sans note ; au-delà, le micro se ferme et la reprise demande un appui. [AUTO]
5. Les choix « recommencer » et « voir le compte-rendu » restent disponibles à l'écran (cibles ≥ 64 dp). [TÉL]

### US-PRA-09 — Répéter un passage en boucle en jeu continu

**En tant qu'**apprenant (Marc), **je veux** rejouer un passage plusieurs fois d'affilée **afin de** le travailler sans manipuler l'écran.

> **Priorité** S · **V/E/R** É/M/M · **Exigences** EF-PRA-11, EF-ANA-04, EF-PRA-05 · **Écran** E12, E14 · **Dépendances** US-PRA-04, [QO-26](09-decisions-a-prendre.md) · **Incrément** I5 · **Statut** Ferme

**Critères d'acceptation**
1. Option « Répéter » en jeu continu : 3 répétitions par défaut, réglable, ou jusqu'à l'arrêt. [AUTO]
2. Entre deux répétitions : une mesure de pause puis le décompte. [AUTO]
3. Chaque répétition est analysée séparément ; le compte-rendu montre le score de chacune. [AUTO]
4. Le tempo ne change pas (boucle progressive en V1). [AUTO]

---

## 11. EP-09 — Restitution des erreurs (RES)

### US-RES-01 — Choisir le retour en direct

**En tant qu'**apprenant, **je veux** choisir si et comment mes erreurs s'affichent pendant le jeu continu **afin de** ne pas être gêné dans ma lecture.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-RES-01, ENF-PERF-02 · **Écran** E12, E13 · **Dépendances** US-ANA-09, US-RES-04 · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Trois niveaux, en **jeu continu seulement** : aucun ; discret (couleur et symbole après le passage du curseur) ; complet (signalement immédiat avec la note jouée). En mode attente, l'erreur est toujours affichée (US-RES-02). [AUTO]
2. Coloration en ≤ 200 ms au 95e centile. [MESURE]
3. Marques uniquement sur les notes déjà passées, jamais sur les mesures à venir ; pas de « ? » pendant le jeu. [TÉL]
4. Seuls les écarts au-dessus du seuil de confiance sont montrés. [AUTO]
5. Valeur par défaut « discret » ; issue des réglages à partir de I4 (US-CPT-04). [AUTO]

### US-RES-02 — Comprendre une erreur en mode attente

**En tant que** débutante (Claire), **je veux** voir la note que j'ai jouée et une indication simple **afin de** comprendre mon erreur.

> **Priorité** M · **V/E/R** É/M/F · **Exigences** EF-RES-02, ENF-ACC-02 · **Écran** E13 · **Dépendances** US-ANA-04, US-PRA-02 · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. La note fausse apparaît à sa hauteur réelle (tête pleine, couleur de la famille « hauteur » et pastille « ✕ » hors de la tête, code de US-RES-04) ; la note attendue est entourée d'un halo. [TÉL]
2. Indication textuelle optionnelle (activée par défaut), selon l'[annexe D.1 de PED](revues/pedagogue-piano.md), décrivant ce qui a été joué et ce qui était attendu sans supposer la cause (sauf altération et octave) : « Fa♯ : le dièse est à l'armure. » ; « Ré naturel : le bécarre annule le dièse. » ; « Bonne note, une octave plus grave (vers la gauche). » ; « Mi : un demi-ton plus grave, la touche juste à gauche. » ; « Attendu : la — joué : ré. » ; « Il manque le mi de l'accord. » ; « Jouez seulement la note indiquée. » [AUTO]
3. Aucun « Faux ! », « Raté », « Erreur ! ». [REVUE]
4. L'indication disparaît dès que la bonne note est jouée. [AUTO]

### US-RES-03 — Lire le compte-rendu de fin de séance

**En tant qu'**apprenant, **je veux** un résumé clair à la fin de chaque séance **afin de** savoir comment j'ai joué et quoi retravailler.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-RES-03, EF-RES-06, ENF-PERF-04 · **Écran** E14 · **Dépendances** US-ANA-09, US-RES-06, US-VIS-06 · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Affiché en ≤ 2 s après la fin d'un passage de 5 minutes (médiane) ; « Analyse en cours… » avec progression au-delà de 2 s. [MESURE]
2. Ordre : score (≥ 45 sp, lisible à 60 cm) et sous-scores selon le mode ; mention « meilleur score sur ce morceau » si c'est le cas ; **un point positif réel** (« Sans erreur : mesures 1 à 8 »), s'il existe ; **passages à retravailler** ; puis détail chiffré dans une section repliée (erreurs par type, durée, mode, mains, tempo, niveau, source). [TÉL]
3. Passages à retravailler (décision [QO-24 a](09-decisions-a-prendre.md), [EDB 06 §7](../EDB/06-analyse-du-jeu-et-erreurs.md)) : jusqu'à 3 groupes de 1 à 4 mesures, classés par gravité pondérée (manquée = fausse = arrêt = 3 ; rythme = hésitation = 2 ; en trop = 1 ; rejouée = 0,5 [PO]) ; à égalité, le plus tôt dans la partition ; chacun avec son type dominant (libellés de l'annexe D.2 de PED : « Mes. 5-6 · main droite · fa♯ joué fa (3 fois) », « Mes. 9 · arrêt — à travailler lentement »). [AUTO]
4. Chaque passage est un lien « Voir sur la partition » qui ouvre la partition annotée à cet endroit, la plage présélectionnée ; « Pratiquer » ouvre alors la préparation préremplie avec cette plage. [TÉL]
5. Erreur dans une section reprise : localisée par sa mesure et, si elle n'apparaît qu'à un passage, « (1re fois) » ou « (2e fois) ». [AUTO]
6. Variante mode attente : score de notes ; « 38 notes sur 42 trouvées du premier coup » ; notes le plus souvent corrigées. [TÉL]
7. Actions : voir la partition annotée, recommencer (mêmes réglages ; « Modifier les réglages » ouvre la préparation), terminer (retour à la partition, à l'endroit du passage joué). [TÉL]
8. Tous les textes respectent les règles de ton (§1.3) ; la liste des textes est relue selon une grille : aucun qualificatif de la personne, aucune formule négative sans conseil. [REVUE]

### US-RES-04 — Voir la partition annotée

**En tant qu'**apprenant, **je veux** voir mes erreurs sur la partition **afin de** savoir exactement où elles sont.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-RES-04, ENF-ACC-02 · **Écran** E14, E29 · **Dépendances** US-VIS-01, US-ANA-04, US-ANA-05 · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Code des résultats ([10, MOD-35](10-modifications-edb-proposees.md)) : la couleur indique la famille (hauteur `#C23B00`, rythme `#005BB5`, absence et continuité `#5F6368`, incertain `#80868B`), un symbole indique le type ; **aucune tête de note ne change de forme** ; notes justes sans marque. [REVUE]
2. Marques : fausse (tête pleine à la hauteur jouée + « ✕ ») ; en trop (petite tête + « + ») ; rejouée (« ↻ ») ; manquée (anneau en pointillé autour de la tête attendue) ; en avance / en retard (flèche ← / →) ; accord non synchronisé (« ≠ ») ; hésitation et arrêt (« ‖ » entre les événements) ; incertain (« ? » en pointillé). Pastilles ≥ 1,5 interligne, au-dessus de la portée pour la main droite et au-dessous pour la main gauche, sans masquer altérations ni doigtés. [TÉL]
3. Contrastes ≥ 3:1 ; vérification avec la simulation des daltonismes d'Android (deutéranomalie, protanomalie, tritanomalie, monochromie), l'inversion des couleurs et le mode sombre forcé du système ; contrôle par un testeur daltonien si possible. Pas de couleurs dynamiques dans la zone de partition ; couleurs définies en jetons (`result.pitch`, `result.rhythm`, `result.absence`, `result.uncertain`, `cursor`). [TÉL]
4. Légende (E29) accessible depuis l'écran. [TÉL]
5. Interrupteur « Afficher les détections incertaines », désactivé par défaut, état mémorisé. [AUTO]
6. Une erreur d'une mesure jouée deux fois (reprise) est rattachée au bon passage. [AUTO]

**Notes.** Symboles à valider par PED.

### US-RES-05 — Examiner le détail d'une note

**En tant qu'**apprenant (Marc), **je veux** toucher une note annotée pour voir le détail **afin de** comprendre précisément ce qui ne va pas.

> **Priorité** M · **V/E/R** M/F/F · **Exigences** EF-RES-04 · **Écran** E15 · **Dépendances** US-RES-04 · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Appui sur une note annotée : note attendue (nom selon la notation, mesure, temps, main), note jouée, type d'erreur. [TÉL]
2. Écart de rythme exprimé d'abord en valeur musicale (« un peu en retard : environ une double croche »), la valeur en ms en second, en petit. [AUTO]
3. Écart incertain (option activée) : « détection incertaine ». [TÉL]
4. La fenêtre se ferme d'un geste et ne masque pas la note. [TÉL]

### US-RES-06 — Obtenir des scores stables et compréhensibles

**En tant qu'**apprenant, **je veux** des scores qui progressent quand je progresse **afin de** mesurer mon amélioration.

> **Priorité** M · **V/E/R** É/F/M · **Exigences** EF-RES-05 · **Écran** E14 · **Dépendances** US-ANA-04, US-ANA-05, US-ANA-08, [QO-39](09-decisions-a-prendre.md) · **Incrément** I2 · **Statut** Ferme

**Critères d'acceptation**
1. Scores sur 100 (notes, rythme, continuité, global) selon [06 §5](../EDB/06-analyse-du-jeu-et-erreurs.md) ; score global **multiplicatif** S_global = S_notes × (0,55 + 0,30 × S_rythme/100 + 0,15 × S_continuité/100) et pénalités de continuité rapportées à la longueur jouée (par tranche de 16 mesures), selon la décision [QO-39](09-decisions-a-prendre.md) ; coefficients en paramètres. Mode attente : score global = score de notes, avec la règle « du premier coup » (US-PRA-02 CA4). [AUTO]
2. Arrondi à l'entier le plus proche (,5 vers le haut), calculé une seule fois sur le score final. [AUTO]
3. Écarts incertains, notes rejouées après un retour en arrière et petites notes absentes hors du score. [AUTO]
4. Batterie de l'[annexe E de PED](revues/pedagogue-piano.md) (7 séances types sur *Für Elise*) : classement attendu A > B ≈ C > D ≈ E > F ≈ G, respecté avec la formule multiplicative (F ≈ 85 ; la somme pondérée de l'EDB v0.5 donnait F ≈ 92). [AUTO]
5. Scores figés à la fin de l'analyse (RG-04). [AUTO]

**Notes.** Scores par main en V1 (EF-ANA-07).

### US-RES-07 — Être prévenu d'une captation douteuse

**En tant qu'**apprenant au micro, **je veux** être prévenu quand l'application m'a mal entendu **afin de** ne pas croire à un score faux.

> **Priorité** M · **V/E/R** É/F/F · **Exigences** ENF-QA-01 · **Écran** E14 · **Dépendances** US-ANA-08, US-ECO-10 · **Incrément** I3 · **Statut** Ferme

**Critères d'acceptation**
1. Plus de 20 % [HYP] d'écarts incertains : bandeau en tête « L'application vous a mal entendu : score indicatif. Rapprochez le téléphone du piano ou réduisez le bruit, puis refaites le réglage du micro. » ; score en gris « indicatif » ; bouton « Refaire le réglage du micro ». [TÉL]
2. Cause probable affichée (détectée par US-ECO-10) ; sur les enregistrements étiquetés par condition, elle correspond à l'étiquette dans ≥ 80 % des cas [PO] ; sans cause identifiée : message générique. [BANC]
3. Séance non fiable exclue du meilleur score. [AUTO]

### US-RES-08 — Signaler une fausse erreur

**En tant que** testeur, **je veux** indiquer qu'une erreur signalée n'en était pas une, ou que j'ai joué juste sans que l'application avance **afin d'**aider à améliorer l'analyse.

> **Priorité** S · **V/E/R** É/F/F · **Exigences** — (EDB 13 §5, bêta) · **Écran** E15, E13 · **Dépendances** US-RES-05, [QO-28](09-decisions-a-prendre.md) · **Incrément** I6 · **Statut** Ferme

**Critères d'acceptation**
1. Le détail d'une erreur propose « Ce n'était pas une erreur ». [TÉL]
2. En mode attente, un appui long sur la note en cours propose « J'ai joué juste mais l'application n'a pas avancé ». [TÉL]
3. Signalement enregistré localement avec : mesure, type, confiance, source, niveau, date au jour près ; **ni titre de morceau importé, ni heure**. [AUTO]
4. Le score n'est pas modifié (RG-04) ; l'erreur est marquée « contestée ». [AUTO]
5. Signalements inclus dans le rapport de test (US-CNF-05). [AUTO]

---

## 12. EP-10 — Historique des séances (HIS)

### US-HIS-01 — Enregistrer chaque séance sans perte

**En tant qu'**apprenant, **je veux** que chaque séance soit enregistrée automatiquement **afin de** garder une trace fiable de mon travail.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** EF-PRO-02, ENF-FIA-01, RG-04 · **Écran** — · **Dépendances** US-RES-03 · **Incrément** I4 · **Statut** Ferme

**Critères d'acceptation**
1. Chaque séance terminée ou interrompue (avec au moins une note jouée) est enregistrée : date, durée, morceau, passage, mode, mains, tempo réglé, source et appareil, niveau, version de l'analyse, scores, écarts, nombre d'essais (attente), état. [AUTO]
2. Test automatisé : 50 arrêts forcés du processus à des instants aléatoires pendant une séance et dans les 2 s qui suivent sa fin, et 10 arrêts brutaux de l'émulateur : aucune base corrompue ; toute séance terminée avant l'arrêt est présente ; la séance en cours est enregistrée « interrompue » jusqu'à la dernière mesure complète. [AUTO]
3. Stockage plein à l'enregistrement : message clair, séances existantes intactes. [AUTO]
4. Dernière pratique et meilleur score de la bibliothèque mis à jour ; dates cohérentes après un changement d'heure ou de fuseau. [AUTO]

### US-HIS-02 — Consulter l'historique d'un morceau

**En tant qu'**apprenant, **je veux** retrouver mes séances passées et leurs comptes-rendus **afin de** voir le chemin parcouru.

> **Priorité** M · **V/E/R** M/F/F · **Exigences** EF-PRO-02 · **Écran** E10, E14 · **Dépendances** US-HIS-01, US-BIB-07 · **Incrément** I4 · **Statut** Ferme

**Critères d'acceptation**
1. Depuis la fiche du morceau, liste des séances (date, passage, mode, score, état). [TÉL]
2. Un compte-rendu passé s'ouvre identique à l'original (scores figés). [AUTO]
3. Base de 1 000 séances générée par un outil de test : les 20 premières s'affichent en ≤ 500 ms [PO]. [MESURE]

**Notes.** Au MVP, tout le détail est conservé (décision [QO-11](09-decisions-a-prendre.md)) ; la place occupée est mesurée en bêta, le compactage décidé avant la V1.

### US-HIS-03 — Conserver ses données lors des mises à jour

**En tant qu'**apprenant, **je veux** retrouver toutes mes données après une mise à jour **afin de** ne jamais perdre mon travail.

> **Priorité** M · **V/E/R** É/M/M · **Exigences** ENF-FIA-03 · **Écran** — · **Dépendances** US-HIS-01, US-CNF-04 · **Incrément** I4 · **Statut** Ferme

**Critères d'acceptation**
1. Données versionnées et migrées automatiquement. [AUTO]
2. Migration testée depuis **chacune** des versions diffusées (N−k → N), avec un jeu de données de référence archivé par version. [AUTO]
3. Échec de migration provoqué par injection de défaut : données d'avant intactes, message, accès en lecture seule, export du rapport de test possible. [AUTO]
4. Stockage insuffisant : migration non lancée, message. [AUTO]
5. Mises à jour sur les téléphones des testeurs. [BÊTA]

---

## 13. EP-11 — Outillage qualité (QUA)

### US-QUA-01 — Constituer le corpus de test v0

**En tant que** mainteneur, **je veux** un corpus de partitions et de jeux annotés **afin de** mesurer objectivement l'import, le rendu et l'analyse.

> **Priorité** M · **V/E/R** É/É/M · **Exigences** ENF-QA-01 · **Écran** — · **Dépendances** PED (pièces, fiches), JUR (licences), [QO-37](09-decisions-a-prendre.md), [QO-49](../DAT/14-decisions-a-prendre.md) · **Incrément** I0 · **Statut** Ferme

**Critères d'acceptation**
1. Sous-corpus, chacun relié au CA qu'il sert :
   - **import** : « éditeurs » et « tout-venant » (US-IMP-01), MIDI des cas de US-IMP-02, fichiers malformés et piégés (US-IMP-06), fichiers de référence **P10** et **P30** ;
   - **rendu** : au moins 20 MusicXML (US-VIS-01) ;
   - **analyse MIDI** : jeux corrects (≥ 3 000 notes sur ≥ 20 pièces), erreurs injectées, jeux hésitants avec retours en arrière, cas particuliers de US-ANA-01 et US-ANA-07, pièces de l'[annexe F de PED](revues/pedagogue-piano.md) ;
   - **analyse micro** : enregistrements du porteur sur le téléphone de référence (≥ 1 000 notes correctes en mode attente, et autant en jeu continu si I5), avec et sans pédale, sur le pupitre et à 1,5 m ; registres extrêmes ; notes répétées ; métronome par le haut-parleur ; autre main jouée par l'application ; conditions étiquetées « bruit », « trop faible », « saturé », « trop loin » ; bruits mécaniques ; versions transposées de −20 et +24 cents ; **sous-corpus d'attaques annotées à la main** (≥ 300 notes, annotées deux fois, écart ≤ 10 ms au 95e centile) ; lot **L0** : gamme chromatique de la 0 à do 8, en deux nuances ; lot **L2** : au moins 400 tentatives fausses par mode, dont au moins 80 erreurs d'octave ; audio normalisé à 48 kHz ; vérité terrain semi-automatique (modèle de référence aligné sur la partition, désaccords vérifiés à l'écoute, sous-corpus annoté à la main pour mesurer l'écart ; usage des sorties du modèle soumis à JUR). [REVUE]
2. Au moins 200 notes par sous-ensemble ciblé par un CA. [AUTO]
3. **Protocole d'annotation** (annexe F de PED) : erreurs scénarisées avant l'enregistrement (fiche par prise), écarts réels notés après, un type d'erreur par prise ; jeu correct au métronome pour le jeu continu ; chaque annotation vérifiée à la réécoute par le porteur [PORTEUR] et sa cohérence avec la partition par PED [REVUE] ; annotation douteuse retirée.
4. **Licences et données** : chaque élément a une source, une licence et un usage documentés. Corpus **public** (dépôt séparé `piano-helper-corpus` : partitions, annotations et MIDI versionnés, fichiers audio joints à chaque version de ce dépôt avec leurs empreintes ; rejoué par l'intégration continue) : partitions et contenus libres, compositeurs morts avant 1900, enregistrements maison publiés sous **CC0** (décision [QO-49](../DAT/14-decisions-a-prendre.md)), bruit ambiant de sources CC0 ou maison sans voix ni programme protégé. Corpus **privé** (hors du dépôt) : enregistrements non publiables, pièces de compositeurs morts après 1900 (Satie, Joplin), jeux de données sous licence NC (téléchargés par un script versionné : URL, somme de contrôle, licence ; leurs adaptations restent sous la même licence ; jamais utilisés pour entraîner un modèle). [REVUE]
5. Pas de pièce « swing » ; les jeux de données expressifs (rubato) servent aux métriques de transcription et d'alignement, pas au taux de fausses erreurs de rythme. [REVUE]
6. Corpus versionné ; toute mesure cite sa version. [AUTO]

### US-QUA-02 — Évaluer automatiquement l'analyse

**En tant que** mainteneur, **je veux** un banc qui mesure la qualité de l'analyse à chaque évolution **afin de** ne jamais dégrader le taux de fausses erreurs.

> **Priorité** M · **V/E/R** É/M/F · **Exigences** ENF-QA-01, ENF-QA-02, ENF-MAINT-01 · **Écran** — · **Dépendances** POC-05, US-QUA-01 · **Incrément** I0 · **Statut** Ferme

**Critères d'acceptation**
1. Le noyau d'analyse s'exécute sur le corpus sans interface, sur un ordinateur et dans l'intégration continue gratuite (corpus public). [AUTO]
2. Métriques de [06 §9.2](../EDB/06-analyse-du-jeu-et-erreurs.md) plus : validations indues et non-validations du mode attente (évaluation événement par événement), précision et rappel des erreurs ; par source, niveau, catégorie de pièce et type d'erreur (notes, rythme, accords, continuité) ; chaque taux avec son effectif et son intervalle de confiance à 95 %. [AUTO]
3. **Règle de non-régression** : comparaison à la référence (dernière version acceptée) **sur la même version du corpus** (si le corpus change, la référence est d'abord recalculée) ; blocage si (a) en MIDI, le **nombre** de fausses erreurs augmente au total ou dans un fichier (tolérance nulle) ; (b) au micro, l'exécution n'est pas reproductible (deux exécutions identiques) ou, en **effectifs**, la hausse nette du nombre de fausses erreurs dépasse ⌊0,001 × N⌋ par mode (N notes), ou max(2 ; ⌊0,005 × N_s⌋) dans un sous-corpus de N_s notes (grave, aigu, pédale, notes répétées, bruit, 1,5 m, transposé) ; même règle pour les validations indues ([EDB 06 §9.3](../EDB/06-analyse-du-jeu-et-erreurs.md)) ; aucune comparaison entre des résultats obtenus avec des compilateurs ou des images d'intégration continue différents : la référence est d'abord recalculée ; (c) un taux dépasse la cible d'ENF-QA-01 ; toute nouvelle fausse erreur, même tolérée, est listée (fichier, mesure, type) ; une hausse acceptée en échange d'un autre gain exige une décision consignée du porteur. [AUTO]
4. Le corpus privé est rejoué par le porteur avant chaque acceptation d'incrément ; rapport archivé. Les données NC donnent des mesures complémentaires, jamais bloquantes. [PORTEUR]
5. À chaque incrément, le corpus micro est aussi rejoué **sur le téléphone de référence** (fichiers audio injectés à la place du micro) et comparé au résultat sur ordinateur à deux niveaux ([ADR-0022](../DAT/adr/0022-reproductibilite-ordinateur-telephone.md)) : pour le code du projet, identité au bit près, tout écart est une anomalie ; pour les sorties d'un moteur d'inférence tiers, écart ≤ ε, les décisions proches du seuil étant listées sans être bloquantes. [MESURE]
6. Rapports archivés par version de l'analyse. [AUTO]

### US-QUA-03 — Simuler un jeu MIDI

**En tant que** mainteneur sans piano numérique, **je veux** simuler un jeu MIDI **afin de** développer et tester l'analyse avec des entrées exactes.

> **Priorité** M · **V/E/R** É/M/F · **Exigences** EF-ECO-02 · **Écran** — · **Dépendances** — · **Incrément** I0 · **Statut** Ferme

**Critères d'acceptation**
1. Rejeu d'un fichier MIDI comme s'il était joué, avec injection d'erreurs, de latence et de gigue. [AUTO]
2. Périphérique MIDI virtuel sur le téléphone et l'émulateur ; production des cas : plusieurs canaux, messages de maintien en continu, pédale à mi-course, transposition, débranchement avec notes enfoncées. [AUTO]
3. Clavier visuel à l'écran pour jouer au toucher. [TÉL]
4. Outils présents seulement dans la variante interne ; un contrôle automatique vérifie leur absence des APK bêta et publics. [AUTO]

### US-QUA-04 — Mesurer les performances sur l'appareil de référence

**En tant que** mainteneur, **je veux** mesurer les cibles de performance sur un vrai téléphone, selon des protocoles écrits à l'avance **afin de** savoir si l'application tient ses promesses.

> **Priorité** M · **V/E/R** M/M/F · **Exigences** ENF-COMP-04, ENF-PERF-01, ENF-PERF-02, ENF-PERF-03, ENF-PERF-04, ENF-PERF-05, ENF-PERF-06, ENF-PERF-10, ENF-RES-04 · **Écran** — · **Dépendances** POC-02 · **Incrément** I0 (protocoles) / I3 (latences) / I6 (campagne complète) · **Statut** Ferme

**Critères d'acceptation**
1. **(I0)** Pour chaque cible ENF-PERF et ENF-RES du MVP, un protocole précise : début et fin de la mesure (au micro, le départ est l'**attaque acoustique**, attaque de référence définie avec son incertitude ; une seule référence temporelle, sans compter deux fois la même latence) ; instrumentation (horodatages internes : événement tactile, tampons audio d'entrée et de sortie, affichage de l'image ; vérification externe par une caméra ≥ 120 images/s d'un second appareil quand un testeur peut en prêter un) ; ce que l'instrumentation interne ne mesure pas ; variante publique ; conditions (batterie > 50 %, économie d'énergie désactivée, mode avion, appareil au repos thermique, une minute de préchauffage) ; nombre de mesures (≥ 50 pour une latence, ≥ 10 pour une durée) ; règle de décision : **95e centile du total mesuré ≤ cible** pour les latences (au micro, résultats publiés par octave ; cible propre au grave si la mesure l'exige, décision [QO-55](../DAT/14-decisions-a-prendre.md)), **médiane ≤ cible et maximum ≤ 1,5 × cible** pour les durées [PO]. [REVUE]
2. Mesures sur le téléphone Android 14 du porteur, jamais sur l'émulateur pour les latences. [MESURE]
3. Latences mesurées aussi en mode économie d'énergie ; avertissement si la cible n'est plus tenue. [MESURE]
4. Chaque rapport précise qu'il ne vaut que pour cet appareil (R-15) et est archivé par version. [REVUE]
5. Un écart à une cible devient une anomalie ou une question ouverte. [REVUE]
6. Autres appareils, dont un d'entrée de gamme (ENF-COMP-04 complet avant la V1, US-PUB-03). [BÊTA]

### US-QUA-05 — Capturer des séances réelles pour les évaluer

**En tant que** mainteneur, **je veux** enregistrer mes propres séances avec une version de test **afin de** mesurer les fausses erreurs en conditions réelles et d'enrichir le corpus.

> **Priorité** M · **V/E/R** É/M/F · **Exigences** ENF-QA-01, ENF-QA-02 · **Écran** — · **Dépendances** US-QUA-03 · **Incrément** I3 · **Statut** Ferme

**Critères d'acceptation**
1. Dans la variante interne seulement, un enregistreur de séance sauvegarde : audio, événements détectés, résultat de l'analyse, version, réglages. [TÉL]
2. Un contrôle automatique vérifie son absence des APK bêta et publics. [AUTO]
3. Les séances capturées sont annotées par le porteur à la réécoute avec la partition et versées au corpus privé (ou public, avec son accord, sans voix identifiable). [PORTEUR]
4. Les enregistrements ne contiennent pas de voix de tiers : le porteur enregistre seul dans la pièce. [PORTEUR]

---

## 14. EP-12 — Confidentialité, licences et diffusion de test (CNF)

### US-CNF-01 — Fonctionner hors ligne sans transmettre de données

**En tant qu'**apprenant (ou parent), **je veux** que l'application fonctionne sans connexion et n'envoie rien **afin de** pratiquer partout et de protéger ma vie privée.

> **Priorité** M · **V/E/R** É/F/F · **Exigences** ENF-OFF-01, ENF-CONF-01, ENF-CONF-02, ENF-CONF-03 · **Écran** — · **Dépendances** US-CNF-03, [QO-36](09-decisions-a-prendre.md) · **Incrément** I1 (vérifié à chaque incrément) · **Statut** Ferme

**Critères d'acceptation**
1. En mode avion, bibliothèque, visualisation, lecture, pratique, analyse et historique fonctionnent. [TÉL]
2. Le manifeste de l'APK ne déclare pas la permission INTERNET (contrôle automatique à chaque construction). [AUTO]
3. Sauvegarde automatique d'Android et transfert entre appareils désactivés (décision [QO-36](09-decisions-a-prendre.md)) : une sauvegarde forcée ne contient aucune donnée de l'application. [ÉMU]
4. Aucune télémétrie, publicité ni bibliothèque d'analyse d'audience (vérifié dans l'inventaire) ; en particulier, le moteur ONNX Runtime tout fait de Microsoft, qui contient un module de télémétrie, n'est jamais dans les variantes bêta et publique (décision [QO-50](../DAT/14-decisions-a-prendre.md)). [REVUE]

**Notes.** ENF-SEC-01 est sans objet au MVP (aucun serveur).

### US-CNF-02 — Livrer les mentions légales et les licences dans chaque APK

**En tant qu'**utilisateur, **je veux** trouver dans chaque version la licence, les droits d'auteur, les composants tiers et le lien vers les sources **afin de** savoir ce que j'installe et de pouvoir le redistribuer.

> **Priorité** M · **V/E/R** M/F/F · **Exigences** — ([EDB 11 §3](../EDB/11-juridique-et-conformite.md), écran des licences) · **Écran** E30 · **Dépendances** US-CNF-03, [QO-34](09-decisions-a-prendre.md) · **Incrément** I0 · **Statut** Ferme

**Critères d'acceptation**
1. Dès la première version diffusée, un écran « À propos », accessible depuis le menu principal, affiche : « Piano Helper — version X.Y.Z — © 2026 Ronan Launay et les contributeurs. Piano Helper est un logiciel libre : vous pouvez le redistribuer et le modifier selon les termes de la licence publique générale GNU, version 3 ou (à votre choix) toute version ultérieure. Il est fourni sans aucune garantie. », suivi de l'avis « Ce programme utilise Verovio, © les auteurs de Verovio (RISM Digital Center), sous licence LGPL-3.0-only ». Ronan Launay est le nom civil du porteur (décision [QO-34](09-decisions-a-prendre.md), option C). [REVUE]
2. Il donne accès, hors ligne, au texte intégral de la GPL-3.0 **et de la LGPL-3.0**, à la liste des composants et contenus tiers avec le **texte intégral** de chaque licence, le contenu des fichiers NOTICE (dont celui de Basic Pitch s'il est retenu), les deux mentions de droits d'auteur de TinySoundFont, les fichiers de licence et FONTLOG des polices, et pour chaque contenu CC : auteur, titre, source, licence, modifications. [AUTO]
3. Liste générée à partir de l'inventaire par un outil libre ; les contenus hors dépendances de construction (sons, partitions, polices, modèles) y figurent. [AUTO]
4. Lien vers les sources de la version installée (étiquette du dépôt). [TÉL]
5. Le numéro de version est aussi visible sur l'écran principal dès I0. [TÉL]

### US-CNF-03 — Tenir l'inventaire des licences

**En tant que** mainteneur, **je veux** un inventaire à jour de tous les composants et contenus tiers **afin de** garantir qu'ils sont libres avant toute diffusion.

> **Priorité** M · **V/E/R** É/F/M · **Exigences** — ([EDB 11 §3](../EDB/11-juridique-et-conformite.md), R-06) · **Écran** — · **Dépendances** JUR · **Incrément** I0 · **Statut** Ferme

**Critères d'acceptation**
1. Chaque composant, poids de modèle, jeu de données, banque de sons, police et partition est inscrit : version, source, licence, obligations, conformité. [REVUE]
2. Critères : **code** sous licence compatible GPL-3.0-or-later (MIT, BSD, Apache-2.0, LGPL, GPL-3.0, MPL-2.0 sans la mention « Incompatible With Secondary Licenses ») ; **contenus** (sons, partitions, polices, images, données embarquées) sous licence libre autorisant usage commercial, modification et redistribution (CC0, CC BY, CC BY-SA, OFL-1.1, MIT, Apache-2.0) ; NC, ND, « usage personnel », licences à restriction d'usage (RAIL, « recherche seulement ») et SDK propriétaires exclus. [REVUE]
3. Modèle d'IA : licences du code, des poids et des données d'entraînement inscrites séparément ; poids publiés sous licence libre par leur titulaire : acceptables, données NC signalées au porteur comme risque résiduel ([QO-41](09-decisions-a-prendre.md)) ; un modèle entraîné par le projet n'utilise que des données libres. [REVUE]
4. Polices (Bravura et polices du moteur de rendu) inscrites avec le texte OFL ; elles sont embarquées sans modification, leurs noms étant réservés ; une réduction n'est faite qu'avec l'autorisation écrite de RISM, demandée seulement si la taille de l'application l'exige (décision [QO-53](../DAT/14-decisions-a-prendre.md)). [REVUE]
5. Spécification REUSE : en-têtes SPDX ou `REUSE.toml`, dossier `LICENSES/` ; `reuse lint` et le contrôle des dépendances échouent si un fichier ou une dépendance n'est pas inscrit, y compris les fichiers embarqués ; nomenclature SPDX produite par `reuse spdx` à chaque diffusion (obligatoire ; CycloneDX facultatif) ; le contenu natif des AAR, que le contrôle des dépendances ne voit pas, est inscrit à la main dans l'inventaire. [AUTO]
6. Avant chaque diffusion, JUR relit l'inventaire et le **porteur consigne sa validation** ; les points marqués [avocat] sont tranchés par un avis humain qualifié avant la V1. [PORTEUR]

### US-CNF-04 — Diffuser un APK de test avec ses sources

**En tant que** testeur, **je veux** installer facilement une version de test et trouver ses sources **afin d'**essayer l'application, la mettre à jour sans perdre mes données et exercer mes droits de logiciel libre.

> **Priorité** M · **V/E/R** É/M/F · **Exigences** ENF-COMP-02 · **Écran** — · **Dépendances** `devops-release`, [QO-35](09-decisions-a-prendre.md), [QO-20](09-decisions-a-prendre.md), [QO-45](../DAT/14-decisions-a-prendre.md), [QO-46](../DAT/14-decisions-a-prendre.md), [QO-47](../DAT/14-decisions-a-prendre.md) · **Incrément** I0 · **Statut** Ferme

**Critères d'acceptation**
1. L'APK s'installe sur Android 13 (API 33) et plus ; refusé en dessous. [ÉMU]
2. Chaque APK diffusé correspond à une étiquette du dépôt public, hébergé sur GitHub, et est construit par l'intégration continue de GitHub sur des machines d'une version figée (décision [QO-45](../DAT/14-decisions-a-prendre.md)) ; versions des dépendances figées ; les APK diffusés (bêta, publique) sont construits pour les seuls processeurs 64 bits `arm64-v8a`, la variante interne ajoute `x86_64` pour l'émulateur (décision [QO-47](../DAT/14-decisions-a-prendre.md)). [AUTO]
3. La page de diffusion contient, à côté de l'APK, « Code source de cette version : [lien] — licence GPL-3.0-or-later » ; si le dépôt utilise des sous-modules, Git LFS ou des bibliothèques natives compilées, une archive complète des sources (`…-src.tar.gz`) est jointe ; à chaque étiquette, l'APK est reconstruit **hors ligne** depuis cette archive, et la diffusion échoue si la reconstruction échoue ; étiquettes et diffusions ne sont jamais supprimées. [REVUE] [AUTO]
4. Signature avec la clé de diffusion pérenne ([QO-35](09-decisions-a-prendre.md)), distincte de la clé de débogage, conservée dans un environnement protégé de l'intégration continue qui exige la validation du porteur, jamais dans le dépôt, sauvegardée hors ligne par le porteur dès sa création ; **empreinte SHA-256 du certificat** publiée dans le README et dans chaque note de version ; nom technique définitif dès I0 : `io.github.zebul1n.pianohelper` (décision [QO-46](../DAT/14-decisions-a-prendre.md)) ; les mises à jour s'installent par-dessus sans perte. [AUTO]
5. Testé sur le téléphone Android 14 et l'émulateur en API 33, 34, 35, 36 et 37, plus une image à pages mémoire de 16 Ko. [ÉMU]
6. Un guide d'installation écrit (sources inconnues, alertes de sécurité, partage par QR code ou lien du compte de distribution limitée, avec consentement sur chaque appareil) accompagne la diffusion. [REVUE]
7. Diffusion gratuite, sur la page de publication du dépôt, pour l'alpha et la bêta, avec le compte gratuit de « distribution limitée » de Google, qui couvre **20 appareils** (décision [QO-20](09-decisions-a-prendre.md)) ; canal public choisi avant la V1 (question [QO-54](../DAT/14-decisions-a-prendre.md), ouverte). [REVUE]

### US-CNF-05 — Exporter un rapport de test

**En tant que** testeur, **je veux** exporter un rapport de mes séances et de mes signalements **afin de** le transmettre au porteur sans serveur.

> **Priorité** S · **V/E/R** É/F/F · **Exigences** ENF-MAINT-04, RGPD-02 · **Écran** E33 · **Dépendances** US-RES-08, [QO-28](09-decisions-a-prendre.md) · **Incrément** I6 · **Statut** Ferme

**Critères d'acceptation**
1. Réglages > Données > « Exporter un rapport de test », par le partage système. [TÉL]
2. Contenu : version, modèle d'appareil et version d'Android, calibrages, résumé des séances (jour sans heure, mode, source, scores, taux d'écarts incertains, morceau désigné par un identifiant neutre « morceau 3 » sauf pour les démonstrations), signalements. [AUTO]
3. Ni audio, ni partition, ni titre de morceau importé, ni nom, ni identifiant de l'appareil (Android ID, identifiant publicitaire, numéro de série), ni texte libre non relu. [AUTO]
4. L'écran d'export et l'en-tête du rapport affichent la notice de la [revue JUR §4.4](revues/juriste-conformite.md) ([EDB 11 §2.4](../EDB/11-juridique-et-conformite.md)) : contenu, destinataire (« Ronan Launay, à rlaunay@free.fr », décision [QO-34](09-decisions-a-prendre.md)), usage, conservation (fin de bêta + 6 mois), droits, consigne pour les moins de 15 ans ; envoi recommandé par e-mail plutôt qu'en ticket public. [REVUE]
5. Le rapport est lisible avant l'envoi, par un humain et par un script. [TÉL]

### US-CNF-06 — Consulter la notice de confidentialité

**En tant qu'**utilisateur (ou parent), **je veux** savoir clairement ce que l'application fait de mes données **afin de** l'utiliser en confiance.

> **Priorité** M · **V/E/R** M/F/F · **Exigences** RGPD-01, ENF-CONF-01 · **Écran** E30 · **Dépendances** US-CNF-02, [QO-34](09-decisions-a-prendre.md), [QO-36](09-decisions-a-prendre.md) · **Incrément** I6 · **Statut** Ferme

**Critères d'acceptation**
1. La notice (texte de la [revue JUR §4.3](revues/juriste-conformite.md)) indique : qui est responsable et comment le contacter (Ronan Launay, rlaunay@free.fr : nom civil et adresse e-mail personnelle du porteur, décision [QO-34](09-decisions-a-prendre.md)) ; les données traitées et leur emplacement ; le micro (finalité, traitement en mémoire, ni enregistrement ni transmission) ; les autorisations ; la sauvegarde Android ; les partages faits par l'utilisateur (export, rapport de test) ; la suppression ; les droits et la CNIL ; la date de mise à jour. [REVUE]
2. Consultable dans l'application, hors ligne, depuis « À propos » ; publiée dans le dépôt (`PRIVACY.md`) et liée depuis chaque page de diffusion. [TÉL]
3. Version complète de l'écran « À propos » (présentation, contact rlaunay@free.fr). [TÉL]
4. Textes validés par JUR, puis par le porteur. [PORTEUR]
