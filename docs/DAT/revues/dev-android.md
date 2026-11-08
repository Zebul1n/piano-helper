# Revue du DAT v0.1 — point de vue `dev-android`

| Élément | Valeur |
|---|---|
| Document relu | DAT v0.1 (README, fichiers 01 à 13, ADR-0001 à ADR-0021) |
| Références | PRD v0.3 (`03-user-stories-mvp.md`, `05-plan-de-release.md`), EDB v0.6 (`08-exigences-non-fonctionnelles.md`) |
| Relecteur | `dev-android` (Kotlin, Jetpack Compose, Room, NDK/JNI) |
| Date | 2026-11-08 |
| Cadre | minSdk 33 ; appareil de test Android 14 et émulateur ; `targetSdk` 36 (exigence Google Play selon l'architecte), `compileSdk` 37 ; pas de permission INTERNET ; sauvegarde Android désactivée |
| Hors champ | Incohérences INC-01 à INC-10 (fichier 13 §3), déjà connues : non reprises |

## Verdict

**Favorable avec réserves.** Le DAT peut être mis en œuvre côté Android. Les choix structurants tiennent : activité unique en Compose, Room, façade JNI étroite avec sondage à chaque image, MidiManager plus AMidi, pas de service de premier plan, son en mémoire. Aucune remarque n'est bloquante pour lancer les POC.

**11 remarques majeures** sont à corriger avant les incréments qu'elles touchent :
- **avant POC-04 et I1** : DA-01, DA-02, DA-08, DA-09, DA-13, DA-16 et DA-23 ;
- **avant I3** (micro) : DA-03, DA-04, DA-05 et DA-06.

Trois d'entre elles viennent de comportements récents d'Android que le fichier 04 ne prend pas en compte :
- **DA-01** : verrouillage d'orientation ignoré sur tablette avec `targetSdk` 36 ;
- **DA-03** : plusieurs activités au premier plan à la fois (multi-reprise) depuis Android 10 ;
- **DA-05** : processus tué quand l'autorisation est retirée.

| Gravité | Nombre |
|---|:-:|
| Bloquante | 0 |
| Majeure | 11 |
| Mineure | 14 |
| Information | 4 |
| **Total** | **29** |

Légende des gravités : **Bloquante** = empêche un POC ou un incrément ; **Majeure** = conception fausse ou incomplète, à corriger dans le DAT avant l'incrément concerné ; **Mineure** = précision d'implémentation, à intégrer au fil de l'eau ; **Information** = point à connaître, sans changement demandé.

Statut des sources : **[V]** = vérifié le 2026-11-07 ou le 2026-11-08 sur developer.android.com (page citée) ; **[S]** = supposé (connaissance du relecteur, non relu à la source pendant cette revue), à vérifier avant l'implémentation.

## Réponses aux points demandés par l'architecte

| Point | Réponse courte | Remarques |
|---|---|---|
| Fichier 04 : découpage en modules | Bon dans l'ensemble. Deux sens de dépendance à corriger (`:bridge` ↔ `:platform`, `:domain` → `:data`) | DA-20 |
| Fichier 04 : cycle de vie d'une séance, interruptions, points de reprise | Le déclencheur `ON_PAUSE` ne suffit pas et suspend à tort. Il manque : l'appel entrant sans pause de l'activité, la coupure du micro par le système (son remplacé par du silence), le retrait d'autorisation (processus tué), la déconnexion d'un flux AAudio, les changements de configuration autres que la rotation, et l'écriture des points de reprise hors de `viewModelScope` | DA-02 à DA-07 |
| Rotation verrouillée | Ignorée sur les écrans de largeur minimale ≥ 600 dp avec `targetSdk` 36 ; plus aucune dérogation possible avec `targetSdk` 37 | DA-01 |
| `dataExtractionRules` et `allowBackup` | La syntaxe générale est juste. Il manque les domaines `device_*`. La section `cross-platform-transfer` exige `<platform-specific-params>` et ne doit pas être recopiée telle quelle. Syntaxe proposée en annexe A | DA-13 |
| Pont JNI (ADR-0003) | Bon principe. À préciser : le sondage dans la couche d'interface et non dans le modèle de vue, l'état lu dans la seule phase de dessin, le JSON transmis en `byte[]` UTF-8, l'attente du chargement de la bibliothèque, le contrat de fils de `finish()` | DA-08, DA-19 |
| ADR-0007 (dessin par Compose) | Faisable. Préciser l'enregistrement de chaque tuile dans un calque graphique et l'absence d'allocation par image | DA-18 |
| ADR-0021 (import dans un processus isolé) | `:import` est un processus privé, **pas un bac à sable** : même UID, accès à tous les fichiers. `Application.onCreate` s'exécute aussi dans ce processus. Il faut choisir explicitement entre processus privé et `isolatedProcess` | DA-16, DA-17 |
| `postVsyncCallback` | Existe depuis l'API 33 et ne s'exécute qu'une fois : il faut le reposter à chaque image. Son ordre par rapport à l'image Compose n'est pas documenté : à mesurer en POC-02 | DA-08 |
| ADR-0005 (MidiManager, AMidi, appareil virtuel) | Bon choix. Utiliser les API non dépréciées en API 33 (`getDevicesForTransport`, `registerDeviceCallback(transport, Executor, …)`). Le débranchement simulé reste à concevoir | DA-21, DA-22 |
| Permissions Android 13 à 17 | `RECORD_AUDIO` seule. Ni `POST_NOTIFICATIONS`, ni Bluetooth, ni service de premier plan au MVP | DA-10, DA-12, DA-24, DA-29 |
| Service de premier plan pour une séance avec micro | **Aucun au MVP**, ce qui est cohérent avec la suspension en arrière-plan. Si une séance doit un jour continuer écran éteint : type `microphone`, permission `FOREGROUND_SERVICE_MICROPHONE`, démarrage seulement quand l'application est visible, notification, donc `POST_NOTIFICATIONS` | DA-12 |

## Remarques

| ID | Gravité | Référence | Constat | Correction proposée | Source |
|---|---|---|---|---|---|
| DA-01 | Majeure | 04 §1 et §3 ; US-VIS-07 CA3 ; ENF-COMP-03 | Avec `targetSdk` 36, sur les écrans de largeur minimale ≥ 600 dp (tablettes, pliables ouverts), le système ignore `screenOrientation`, `setRequestedOrientation()`, `resizeableActivity`, `minAspectRatio` et `maxAspectRatio`. Le verrouillage d'orientation de l'écran de séance n'a donc **aucun effet sur tablette**. La dérogation `PROPERTY_COMPAT_ALLOW_RESTRICTED_RESIZABILITY` est temporaire et disparaît avec `targetSdk` 37. Exceptions : jeux, choix explicite de l'utilisateur, écrans de moins de 600 dp. | (1) Garder le verrouillage sur téléphone. (2) Concevoir l'écran de séance pour **survivre à une rotation et à un redimensionnement** sans recréer la séance ni la suspendre (DA-02), avec une disposition en portrait et en paysage sur tablette. (3) Ne pas utiliser la dérogation, puisqu'elle est temporaire. (4) Signaler au PO une incohérence nouvelle (NI-1) : US-VIS-07 CA3 n'est pas tenable sur tablette. (5) Ajouter à la CI un profil d'émulateur de largeur ≥ 600 dp et le drapeau de compatibilité `UNIVERSAL_RESIZABLE_BY_DEFAULT`. | [V] developer.android.com/about/versions/16/behavior-changes-16 (« Ignore orientation, resizability, and aspect ratio restrictions ») ; /about/versions/17/behavior-changes-17 (dérogation supprimée en API 37) |
| DA-02 | Majeure | 04 §4 (« la séance en cours n'est pas recréée ») et §6 | D'autres changements de configuration que la rotation recréent l'activité : redimensionnement, écran partagé, fenêtres de bureau, pliage, thème sombre, taille de police, langue, densité. La recréation passe par `ON_PAUSE`, `ON_STOP` et `ON_DESTROY`. Avec la règle « `ON_PAUSE` → séance suspendue », un passage automatique au thème sombre ou un pliage suspendrait la séance. Le fichier 04 ne dit pas non plus **qui possède** la séance native. | (1) La poignée native de la séance appartient à un objet qui survit à l'activité : modèle de vue rattaché à l'entrée de navigation, ou contrôleur de séance de portée application. Jamais l'activité. (2) Ignorer les événements de cycle de vie quand `isChangingConfigurations` est vrai. (3) Déclarer sur l'activité unique `android:configChanges="orientation\|screenSize\|smallestScreenSize\|screenLayout\|uiMode\|density\|fontScale\|keyboard\|keyboardHidden\|navigation"` : Compose recalcule la mise en page sans recréation. Garder la restauration par `SavedStateHandle` pour la mort du processus. (4) Test instrumenté : rotation, redimensionnement et changement de thème pendant une séance, sans suspension ni perte. | [V] developer.android.com/develop/ui/compose/layouts/adaptive/support-multi-window-mode (« Configuration changes », « configChanges ») ; [S] liste exacte des valeurs utiles à `configChanges` pour Compose |
| DA-03 | Majeure | 04 §6, ligne `Lifecycle.ON_PAUSE` ; US-PRA-06 CA1 ; ENF-COMP-06 | Depuis Android 10, en multifenêtre (« multi-reprise »), **toutes les activités visibles restent `RESUMED`**. Le passage en écran partagé ou en fenêtre flottante ne déclenche donc **pas** `ON_PAUSE`. À l'inverse, `ON_PAUSE` survient quand une activité transparente se place au-dessus. L'ouverture du volet de notifications retire le focus sans mettre l'activité en pause. Le tableau du 04 attribue donc à `ON_PAUSE` des cas qu'il ne couvre pas. Autre incohérence nouvelle (NI-2) : ENF-COMP-06 (C, « multifenêtre sans perte de captation ») contredit US-PRA-06 CA1 (« écran partagé : séance suspendue »). | Déclencheurs à retenir : **`ON_STOP`** (application plus visible) → suspension, fermeture du micro, point de reprise ; **`onTopResumedActivityChanged(false)`** (API 29) → suspension, conformément à US-PRA-06 CA1 au MVP ; `ON_PAUSE` seul → pas de suspension, ou suspension prudente par décision du PO. Faire trancher NI-2 par le PO, puis écrire la règle dans le 04 §6. | [V] support-multi-window-mode (« Multi-resume », « Exclusive resource access ») |
| DA-04 | Majeure | 04 §6 (appel, focus audio, micro muet) ; US-PRA-06 CA1 ; US-ECO-04 CA5 ; US-ECO-11 CA5 | (1) Le DAT ne dit pas si la séance **demande le focus audio** (ni l'usage, ni le type de gain). Sans focus détenu, `AUDIOFOCUS_LOSS` n'arrive jamais. (2) Un appel entrant affiché en bandeau pendant que l'application est visible ne met pas forcément l'activité en pause [S]. (3) Pendant un appel (`MODE_IN_CALL`, `MODE_IN_COMMUNICATION`), une application ordinaire reçoit du silence. Un assistant vocal ou une autre application plus prioritaire en multifenêtre fait aussi taire le micro. (4) `LOSS_TRANSIENT_CAN_DUCK` (notification) ne doit pas suspendre la séance (US-ECO-11 CA5). | (a) Demander le focus audio au début de **toute** séance, y compris au micro sans son, et le libérer à la fin. `LOSS` ou `LOSS_TRANSIENT` → suspension ; `CAN_DUCK` → aucune suspension. (b) `AudioManager.addOnModeChangedListener` (API 31) : `MODE_RINGTONE`, `MODE_IN_CALL` ou `MODE_IN_COMMUNICATION` → suspension, sans `READ_PHONE_STATE`. (c) `AudioManager.registerAudioRecordingCallback` et `AudioRecordingConfiguration.isClientSilenced()` → « micro coupé par le système », en complément de la détection de silence de US-ECO-04 CA5. Le rapprochement avec un flux AAudio par identifiant de session est à confirmer en POC-02 [S]. | [V] developer.android.com/media/platform/sharing-audio-input (priorités, appel, `isClientSilenced`) ; [S] comportement exact du bandeau d'appel et focus pris par la sonnerie |
| DA-05 | Majeure | 04 §6, ligne « Autorisation du micro retirée » ; US-ECO-03 CA6 | Retirer une autorisation, y compris une autorisation « uniquement cette fois », **termine le processus**. La suspension avec explication dans le processus n'existe donc pas. Le seul chemin possible est la mort du processus, puis la séance « interrompue » au lancement suivant. Incohérence nouvelle (NI-3) : la formulation de US-ECO-03 CA6 suppose une suspension en direct. | Réécrire la ligne : « Autorisation retirée → le système termine le processus ; au lancement suivant, la séance `en_cours` devient `interrompue` (dernier point de reprise) et l'écran explique que le micro n'est plus autorisé (texte de US-ECO-03 CA3). » Faire reformuler CA6 par le PO. Garder la suspension pour « micro coupé ou occupé » (DA-04). | [V] developer.android.com/training/permissions/requesting (« App process terminates when permission revoked ») |
| DA-06 | Majeure | 04 §6 (diagramme d'états, tableau) ; ADR-0004 ; ENF-PERF-03 | Deux cas manquent. (1) Un flux AAudio est **déconnecté** quand le routage change : écouteurs branchés ou débranchés, appareil Bluetooth ou audio USB. Il faut alors le fermer et le rouvrir. (2) L'ouverture des flux peut **échouer** (micro occupé, flux exclusif refusé). Le diagramme ne montre ni `Ouverture → échec`, ni la reprise après une déconnexion. Après réouverture de la sortie, la latence et les horodatages changent, ce qui touche la synchronisation du curseur. | Ajouter au tableau : « Flux d'entrée déconnecté → suspension et message ; flux de sortie déconnecté → réouverture transparente, recalage de la correspondance position → temps, avertissement si le nouvel appareil est Bluetooth. » Ajouter au diagramme `Ouverture → EchecOuverture` (message, retour à la préparation). Le noyau signale la déconnexion par la file sondée et ne rappelle jamais Kotlin (ADR-0003). | [S] documentation Oboe et AAudio (`AAUDIO_ERROR_DISCONNECTED`), à relire en POC-02 |
| DA-07 | Mineure | 04 §6 (points de reprise) ; 06 §5 ; US-PRA-06 CA3 ; US-HIS-01 CA2 | (1) Si le point de reprise est écrit dans `viewModelScope`, il est annulé quand le modèle de vue est détruit, par exemple quand l'utilisateur ferme l'application depuis les applications récentes. (2) Après la mort du processus en arrière-plan, la navigation restaure l'écran de séance (`SavedStateHandle`) alors que la séance native n'existe plus. (3) Le ménage du démarrage, qui passe `en_cours` à `interrompue`, ne doit pas viser une séance vivante du même processus. | (1) Écrire le point de reprise dans une portée de coroutine de l'application, et aussi sur `ON_STOP`, pas seulement à la suspension. (2) Quand l'écran de séance est restauré sans séance native, aller vers E28 « séance interrompue » ou vers le compte-rendu partiel. (3) Faire le ménage une seule fois, au démarrage du processus principal, avant toute création de séance (voir aussi DA-16). | [S] |
| DA-08 | Majeure | 04 §4 ; ADR-0003 ; ADR-0020 ; 05 §3 (E6, E7) | (1) `postVsyncCallback` ne s'exécute qu'**une fois** : il faut le reposter à chaque image et le retirer à l'arrêt. Il appartient au `Looper` du fil d'interface, donc à la couche d'affichage et non au modèle de vue, qui survit à l'activité (risque de fuite). (2) Passer les données de chaque image par `StateFlow` puis `collectAsState` ajoute une distribution de coroutine et une recomposition : risque d'**une image de retard** (17 ms, non prévue au budget E6) et d'allocations à chaque image. (3) L'ordre entre le rappel de synchronisation verticale et l'image de Compose n'est pas documenté. | (a) Sonder le noyau dans la couche d'interface : effet rattaché au composable de séance, piloté par image. (b) Écrire le résultat dans un état observable par Compose (*snapshot state*), lu **seulement en phase de dessin** (`Canvas`, `drawWithContent`, lambda de `graphicsLayer`) : seul le dessin est refait, sans recomposition ni mise en page. (c) Ne pas utiliser `StateFlow` pour les données de chaque image ; le garder pour l'état de l'écran (pause, fin). (d) Garder `postVsyncCallback` pour obtenir `FrameTimeline` et mesurer en POC-02 que la décision s'affiche à l'image suivante (T_décision → T_présentation ≤ 1 image + composition). | [V] developer.android.com/reference/android/view/Choreographer (`postVsyncCallback`, API 33 : « runs once then is automatically removed ») ; [V] developer.android.com/develop/ui/compose/phases ; [S] latence ajoutée par `StateFlow` |
| DA-09 | Majeure | 04 §3 ; US-PRA-07 CA2 (cibles dans les coins) ; US-PRA-06 | Avec `targetSdk` 36, l'affichage **bord à bord** est obligatoire (la dérogation est désactivée). Le **retour prédictif** est actif par défaut : `onBackPressed` n'est plus appelé et `KEYCODE_BACK` n'est plus transmis. Le fichier 04 n'en parle pas, alors que l'écran de séance place des commandes dans les coins, en paysage, près des encoches et des zones de geste. | (1) Gérer les marges du système (`WindowInsets.safeDrawing`, `displayCutout`, `systemGestures`) pour les commandes de séance et la bande. (2) Gérer le retour par `BackHandler` ou `PredictiveBackHandler` : en séance, retour = suspension puis E28 ; jamais une sortie silencieuse. (3) Ajouter des captures avec encoche et navigation par gestes aux contrôles de US-VIS-07 CA1. | [V] behavior-changes-16 (« Edge to edge opt-out going away », « predictive back ») |
| DA-10 | Mineure | 04 §5 (« uniquement cette fois » détectée) ; US-ECO-03 CA4 et CA5 | Aucune API ne dit qu'une autorisation était « uniquement cette fois ». Le refus définitif ne se lit qu'indirectement. | CA5 : garder un indicateur « micro accordé lors de la dernière séance ». Au lancement suivant, s'il est vrai et que l'autorisation est absente, afficher le bandeau, sans pouvoir distinguer d'un retrait dans les réglages. CA4 : refus immédiat avec `shouldShowRequestPermissionRationale` faux → guider vers les réglages. | [V] requesting (refus répété = « don't ask again ») ; [S] heuristique |
| DA-11 | Mineure | 05 §3.3 ; ADR-0004 ; ENF-RES-01 | Le flux de sortie reste ouvert tant qu'une partition est affichée. Rien ne dit qu'il est fermé quand l'application passe en arrière-plan. Sous Android 17, pour **toutes** les applications, la lecture et le focus audio sans activité visible ni service de premier plan sont rendus muets ou refusés. | Fermer le flux de sortie et mettre la lecture en pause sur `ON_STOP` ; rouvrir sur `ON_START` si une partition est affichée (rouvrir prend quelques dizaines de millisecondes, la cible ENF-PERF-10 reste tenue au premier appui suivant [S]). | [V] developer.android.com/about/versions/17/changes/bg-audio |
| DA-12 | Information | 04 §5, ligne `FOREGROUND_SERVICE` | Le choix « pas de service de premier plan » est juste et cohérent avec la suspension en arrière-plan : en arrière-plan, une application sans service reçoit du silence. Pour mémoire, une séance écran éteint (V1 ?) demanderait : type `microphone`, permissions `FOREGROUND_SERVICE` et `FOREGROUND_SERVICE_MICROPHONE`, `RECORD_AUDIO` accordée, démarrage **seulement quand l'application est visible** (restriction « pendant l'utilisation »), notification (donc `POST_NOTIFICATIONS`), plus `mediaPlayback` si l'application joue du son. | Ajouter cette phrase au 04 §5 pour fermer la question ; toute évolution demandera une ADR. | [V] developer.android.com/develop/background-work/services/fgs/service-types (« Microphone ») ; [V] sharing-audio-input (« only apps running in the foreground (or a foreground service) can capture ») |
| DA-13 | Majeure | 06 §8 ; K9 ; US-CNF-01 CA3 ; US-CPT-05 CA4 | La syntaxe proposée est presque juste, mais : (1) elle oublie les domaines `device_root`, `device_file`, `device_database` et `device_sharedpref` (stockage protégé de l'appareil) ; (2) `<cross-platform-transfer platform="ios">` exige l'élément `<platform-specific-params bundleId teamId contentVersion>`, avec attributs obligatoires : la section ne peut pas être recopiée « avec les mêmes exclusions » sans ces paramètres, et il n'existe ni application iOS ni identifiant d'équipe ; (3) `disableIfNoEncryptionCapabilities` n'est pas utilisé. La documentation confirme que `allowBackup="false"` ne désactive pas le transfert d'appareil à appareil chez certains constructeurs, et qu'une section absente rend ce mode entièrement actif. | Syntaxe de l'annexe A : `cloud-backup` et `device-transfer` excluent les neuf domaines ; **pas** de section `cross-platform-transfer`, car il n'existe aucune application iOS à associer. Conséquence à vérifier : « sans section, pas de transfert multiplateforme possible » est une déduction [S] ; tester sur un émulateur Android 16 QPR2 (API 36.1) quand l'outil le permet et consigner le résultat. Contrôle de CI : présence des deux sections et des neuf exclusions dans le XML compilé. | [V] developer.android.com/identity/data/autobackup (syntaxe, domaines, « If there are no rules for a particular backup mode… fully enabled », note sur les constructeurs, `cross-platform-transfer`, `platform-specific-params`) |
| DA-14 | Mineure | 06 §5 (`PRAGMA synchronous=FULL` dans un rappel Room) | `synchronous` se règle pour chaque connexion [S]. Room et Android utilisent un ensemble de connexions ; régler la valeur dans `onOpen` ne vise qu'une connexion. | Vérifier par un test instrumenté que la connexion d'**écriture** est bien en `FULL` (lecture de `PRAGMA synchronous` dans une transaction d'écriture). Sinon, appliquer le réglage à chaque connexion par l'API du pilote (Room 2.8). Compléter la vérification déjà prévue sur sqlite.org. | [S] |
| DA-15 | Mineure | 06 §7 (« Supprimer toutes mes données… puis redémarrage ») ; US-CPT-05 | Aucune API publique ne redémarre une application. Il existe `ActivityManager.clearApplicationUserData()` : efface les données internes et externes de l'application, retire les autorisations, puis termine le processus [S]. | Choisir et écrire le mécanisme : (a) `clearApplicationUserData()`, qui couvre CA3 et CA4 d'un coup mais ferme l'application (texte E31 à adapter) ; ou (b) suppression manuelle puis recréation de la pile d'activités, sans bibliothèque tierce. Recommandation : (a). | [S] référence `ActivityManager.clearApplicationUserData` à relire |
| DA-16 | Majeure | ADR-0021 ; 02 §2 ; 09 §2 (« Isolement ») ; 06 §4 (ménage au démarrage) | (1) `android:process=":import"` crée un processus **privé à l'application, sous le même UID**. C'est une isolation contre les plantages, **pas une isolation de sécurité** : un code natif compromis par un fichier piégé y lit et écrit toute la base et tous les fichiers. Seul `android:isolatedProcess="true"` donne un processus sans permission, qui ne communique que par l'API Service. (2) `Application.onCreate` s'exécute **dans chaque processus** : conteneur d'injection, ouverture de Room et ménage du démarrage (« supprime les `.tmp` et les dossiers sans ligne en base ») tourneraient aussi dans `:import`, au risque d'effacer le dossier de travail d'un import en cours. (3) Android ne borne pas la mémoire native d'un processus : « limite de mémoire séparée » n'est vrai que pour le tas Java [S]. (4) Tuer le processus au bout de 5 s depuis le processus principal n'est possible que sous le même UID [S]. | (a) Écrire dans l'ADR ce qu'on vise : MVP = **processus privé** (contre les plantages et le manque de mémoire), avec la limite de sécurité dite telle quelle ; `isolatedProcess` étudié en POC-04 (entrées et sorties passées en `ParcelFileDescriptor` ouverts par le processus principal, aucun accès à `filesDir`). (b) Dans `Application.onCreate`, tester `Application.getProcessName()` (API 28) : dans `:import`, ne rien initialiser d'autre que le service. (c) Minuterie de surveillance **dans** le processus d'import, qui se termine lui-même au bout de 5 s, plus déliaison côté principal. (d) Plafond explicite de l'allocateur natif ou `setrlimit` dans `:import`, à valider en POC-04 sur l'archive de 1 Go. | [V] developer.android.com/guide/topics/manifest/service-element (`isolatedProcess`, `process` avec « : ») ; [S] points 2 à 4 |
| DA-17 | Mineure | 07 §1 ; 04 §5 ; US-IMP-03 CA1 à CA4 ; US-IMP-06 CA2 | (1) Le droit de lecture reçu avec `VIEW` ou `SEND` est temporaire ; la taille fournie (`OpenableColumns.SIZE`) peut être absente ; les fichiers de stockage en ligne peuvent être lents. (2) Les URI `content://` n'ont pas d'extension fiable : un filtre d'intention par motif de chemin ne fonctionne pas, seul le type MIME compte. | (1) Le processus principal copie aussitôt le flux dans un fichier de transit privé, en comptant les octets (refus au-delà de 20 Mio sans attendre la fin), calcule le SHA-256 (doublons, US-IMP-07 CA4), puis donne au processus d'import un descripteur de cette copie. (2) Filtres `VIEW` et `SEND` par types MIME : `application/vnd.recordare.musicxml+xml`, `application/vnd.recordare.musicxml`, `audio/midi`, `audio/x-midi`, `application/xml`, `text/xml`, et `application/octet-stream` (l'application apparaîtra pour tout fichier binaire : choix à valider par le PO), contrôle du contenu à l'import. Vérifier CA2 et CA4 sur le téléphone. | [S] |
| DA-18 | Mineure | ADR-0007 ; 07 §6 et §7 ; ENF-PERF-08 | Le principe est bon, mais l'implémentation qui tient 60 images/s n'est pas décrite. | Préciser : chemins des glyphes construits **une fois** ; chaque tuile enregistrée dans un `GraphicsLayer` Compose (ou `RenderNode`), redessinée seulement quand son contenu change ; défilement et curseur par la lambda de `graphicsLayer` (phase de dessin seule) ; textes mis en forme une fois (`TextMeasurer`) ; pas de calque hors écran plus grand que la taille de texture maximale du processeur graphique. Mesure en POC-04 par Macrobenchmark (`FrameTimingMetric`). | [V] compose/phases ; [S] le reste |
| DA-19 | Mineure | ADR-0003 ; 04 §2 et §7 ; 03 §3.6 | (1) `NewStringUTF` et `GetStringUTFChars` utilisent l'UTF-8 **modifié** : un caractère hors du plan de base (émoji dans un titre ou une métadonnée du rapport d'import) casse l'échange [S]. (2) La bibliothèque est chargée en arrière-plan : tout appel natif avant la fin du chargement provoque `UnsatisfiedLinkError`. (3) `finish()` tourne sur un fil d'arrière-plan pendant que le fil d'interface sonde peut-être encore. (4) Le format du `ByteBuffer` direct n'est pas précisé. | (1) JSON échangé en `ByteArray` UTF-8 standard, décodé par kotlinx.serialization. (2) `:bridge` expose une attente suspendue (`ensureLoaded()`) devant tout appel. (3) Contrat : arrêter le sondage, puis appeler `finish()` ; la poignée est libérée dans `onCleared` après l'arrêt du sondage. (4) `ByteBuffer` direct alloué une fois, `order(ByteOrder.nativeOrder())`, lectures absolues sans allocation, en-tête versionné et nombre d'enregistrements. | [S] developer.android.com/training/articles/perf-jni (UTF-8 modifié), non relu dans cette revue |
| DA-20 | Mineure | 04 §2 ; 02 §2 | (1) Le 02 fait dépendre `platform` de `bridge` (`PLAT → BR`) ; le 04 fait l'inverse (`:bridge` dépend de `:platform`). (2) `:domain` dépend de `:data` : le domaine devrait définir les interfaces de dépôt, implémentées par `:data`. (3) `:feature:practice` appelle `:bridge` directement : c'est acceptable pour le temps réel, à dire explicitement. | `:platform` et `:bridge` ne dépendent pas l'un de l'autre : c'est `:app` ou le cas d'usage qui transmet le `MidiDevice` et l'identifiant du micro au pont. `:data` dépend de `:domain` (dépôts déclarés dans le domaine), ce qui garde `:domain` pur pour Kotlin Multiplatform. Écrire l'exception temps réel `:feature:practice → :bridge`. | [S] |
| DA-21 | Mineure | ADR-0005 ; 03 §3.2 ; US-ECO-02 | `getDevices()` et `registerDeviceCallback(callback, handler)` sont **dépréciées depuis l'API 33**. Les claviers qui parlent par défaut en paquets MIDI universels (MIDI 2.0) ne sont pas listés en `TRANSPORT_MIDI_BYTE_STREAM`. Un `MidiDevice` perdu par le ramasse-miettes est fermé automatiquement (documenté pour le Bluetooth [V], supposé général [S]). | Utiliser `getDevicesForTransport(TRANSPORT_MIDI_BYTE_STREAM)` et `registerDeviceCallback(TRANSPORT_MIDI_BYTE_STREAM, executor, callback)`. Exclure les appareils en paquets universels au MVP et l'inscrire au registre de la bêta. Garder une référence forte au `MidiDevice` pendant la séance ; ordre de fermeture : `AMidiOutputPort_close`, `AMidiDevice_release`, puis `MidiDevice.close()`. | [V] developer.android.com/reference/android/media/midi/MidiManager |
| DA-22 | Mineure | ADR-0005 (simulation) ; 08 §5 ; US-QUA-03 CA2 | Le `MidiDeviceService` doit être déclaré avec la permission `android.permission.BIND_MIDI_DEVICE_SERVICE`, le filtre `android.media.midi.MidiDeviceService` et la métadonnée `device_info`. Le « débranchement simulé » d'un appareil virtuel n'a pas d'API directe : il faut désactiver le composant (`PackageManager.setComponentEnabledSetting`) pour provoquer `onDeviceRemoved` [S]. | Déclarer le service seulement dans `app/src/interne/AndroidManifest.xml` (ou dans le manifeste de `:tools`). Prototyper en I0 le débranchement par désactivation du composant ; à défaut, simuler le débranchement au niveau de `:platform` et le signaler comme limite de US-QUA-03 CA2. | [S] |
| DA-23 | Majeure | ADR-0013 ; C-T4 ; C-T8 ; 04 §8 ; US-QUA-05 CA2 | Le contrôle de l'APK ne vérifie que l'absence des **classes Kotlin** des outils. Le code natif de l'enregistreur et de l'injection de fichiers (`FileAudioIO`, dr_libs) serait compilé dans `libpianocore.so` **de toutes les variantes** si la construction native ne dépend pas de la variante. La promesse « aucune API de fichier dans les modules audio hors variante interne » ne serait alors vérifiée que par la revue. | Arguments CMake par variante (`productFlavors { interne { externalNativeBuild { cmake { arguments += "-DPIANO_INTERNAL_TOOLS=ON" } } } }`, `OFF` ailleurs), avec des cibles distinctes. Contrôle de CI sur le `.so` bêta et public : absence des symboles de l'enregistreur et de dr_wav (`llvm-nm -D`, `llvm-objdump -t`). Temps de construction natif doublé : acceptable. | [S] |
| DA-24 | Mineure | 09 §1 (liste blanche « `RECORD_AUDIO` seulement ») | AndroidX Core déclare et utilise une permission de signature propre à l'application, `<applicationId>.DYNAMIC_RECEIVER_NOT_EXPORTED_PERMISSION` [S]. La variante interne ajoute un service protégé par permission (DA-22). Le contrôle tel qu'il est écrit échouerait, ou serait assoupli à la main. | Liste blanche par variante, qui distingue (a) les permissions système demandées (`RECORD_AUDIO`) et (b) les permissions de signature de l'application, acceptées par motif. Liste noire explicite : `INTERNET`, `ACCESS_NETWORK_STATE`, `FOREGROUND_SERVICE*`, `POST_NOTIFICATIONS`, `READ_*`, `BLUETOOTH*`. | [S] |
| DA-25 | Mineure | 05 §7 (« Mémoire basse (`onTrimMemory`) ») | Sur les versions récentes, la plupart des niveaux `TRIM_MEMORY_RUNNING_*` sont dépréciés et ne sont plus envoyés au premier plan [S]. On ne peut pas compter dessus pendant une séance. | Borner les caches par conception (nombre de tuiles en mémoire, liste d'affichage mappée et non copiée). Utiliser `onTrimMemory(TRIM_MEMORY_UI_HIDDEN)` seulement pour libérer à la sortie de l'écran. | [S] |
| DA-26 | Mineure | 10 §4 ; 08 §1 ; RT-10 | Les comportements nouveaux se testent par des drapeaux de compatibilité et des commandes `adb` que la CI nocturne ne prévoit pas. | Ajouter aux tests instrumentés nocturnes : profil d'émulateur de largeur ≥ 600 dp ; `UNIVERSAL_RESIZABLE_BY_DEFAULT` ; `adb shell cmd audio set-enable-hardening enable` (Android 17) ; scénario d'appel simulé de l'émulateur ; rotation et redimensionnement en séance (DA-01, DA-02, DA-03, DA-11). | [V] behavior-changes-16 ; [V] 17/changes/bg-audio |
| DA-27 | Information | 05 §6 (mode « performance soutenue ») ; ENF-RES-02 | `Window.setSustainedPerformanceMode` n'a d'effet que si `PowerManager.isSustainedPerformanceModeSupported()` est vrai, ce qui est rare [S]. | Dans la variante interne, journaliser l'état thermique (`PowerManager.addThermalStatusListener`, API 29) pendant POC-02 ; les indications de performance d'Oboe relèvent de `dev-natif-cpp`. | [S] |
| DA-28 | Information | US-ECO-11 CA5 (conseil « Ne pas déranger ») | Changer le mode « Ne pas déranger » exige `ACCESS_NOTIFICATION_POLICY` et un accord de l'utilisateur, hors liste blanche. | Proposer seulement d'ouvrir le réglage du système, sans permission. | [S] |
| DA-29 | Information | 04 §6, ligne « Sortie Bluetooth détectée » ; US-ECO-11 CA3 | La détection se fait par `AudioManager.registerAudioDeviceCallback` et `getDevices(GET_DEVICES_OUTPUTS)`, sans permission Bluetooth [S]. | Lister les types concernés : `TYPE_BLUETOOTH_A2DP`, `TYPE_BLE_HEADSET`, `TYPE_BLE_SPEAKER`, `TYPE_BLE_BROADCAST`, `TYPE_HEARING_AID`. Aucune permission Bluetooth au MVP : à confirmer à l'implémentation. | [S] |

## Incohérences nouvelles relevées dans le PRD et l'EDB

Elles s'ajoutent à INC-01 à INC-10 ; les corrections reviennent au PO et au porteur.

| ID | Où | Constat | Proposition |
|---|---|---|---|
| NI-1 | US-VIS-07 CA3 | Le verrouillage d'orientation en séance est ignoré sur les écrans de largeur minimale ≥ 600 dp dès `targetSdk` 36, sans dérogation possible avec 37 (DA-01). | « Pendant une séance, l'orientation est verrouillée **sur téléphone** ; sur tablette, la séance continue sans interruption après une rotation. » |
| NI-2 | ENF-COMP-06 (C) et US-PRA-06 CA1 | L'EDB veut une captation sans perte en multifenêtre ; le PRD suspend la séance en écran partagé (DA-03). | Au MVP, suspension quand l'application perd la position d'activité principale (`onTopResumedActivityChanged`) ; ENF-COMP-06 en V1. |
| NI-3 | US-ECO-03 CA6 | « Autorisation retirée pendant une séance : séance suspendue » : en réalité le système termine le processus (DA-05). | « … le système ferme l'application ; au retour, la séance est enregistrée « interrompue » et l'écran explique que le micro n'est plus autorisé. » |

## Annexe A — Règles de sauvegarde proposées

Tous les éléments et attributs ci-dessous viennent de la syntaxe officielle [V] (developer.android.com/identity/data/autobackup). Seule l'omission volontaire de `cross-platform-transfer` est une déduction [S] (DA-13).

```xml
<!-- AndroidManifest.xml -->
<application
    android:allowBackup="false"
    android:dataExtractionRules="@xml/data_extraction_rules"
    ... >
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<!-- res/xml/data_extraction_rules.xml — tout exclure (QO-36, ENF-CONF-01) -->
<data-extraction-rules>
    <cloud-backup disableIfNoEncryptionCapabilities="true">
        <exclude domain="root" path="." />
        <exclude domain="file" path="." />
        <exclude domain="database" path="." />
        <exclude domain="sharedpref" path="." />
        <exclude domain="external" path="." />
        <exclude domain="device_root" path="." />
        <exclude domain="device_file" path="." />
        <exclude domain="device_database" path="." />
        <exclude domain="device_sharedpref" path="." />
    </cloud-backup>
    <device-transfer>
        <exclude domain="root" path="." />
        <exclude domain="file" path="." />
        <exclude domain="database" path="." />
        <exclude domain="sharedpref" path="." />
        <exclude domain="external" path="." />
        <exclude domain="device_root" path="." />
        <exclude domain="device_file" path="." />
        <exclude domain="device_database" path="." />
        <exclude domain="device_sharedpref" path="." />
    </device-transfer>
    <!-- Pas de <cross-platform-transfer> : il exige <platform-specific-params
         bundleId teamId contentVersion> et aucune application iOS n'existe.
         À vérifier sur un émulateur Android 16 QPR2 (API 36.1). -->
</data-extraction-rules>
```

Remarques :
- `path="."` reprend l'exemple officiel (`<include domain="sharedpref" path="."/>`) ; la documentation écrit aussi `./`. Les deux désignent le dossier courant et s'appliquent récursivement.
- `cacheDir`, `codeCacheDir` et `noBackupFilesDir` sont toujours exclus par le système, quelles que soient les règles.
- `android:fullBackupContent` ne sert qu'aux appareils Android 11 et moins : inutile avec minSdk 33.

## Sources consultées

Vérifiées le 2026-11-07 ou le 2026-11-08 :

- developer.android.com/identity/data/autobackup
- developer.android.com/about/versions/16/behavior-changes-16
- developer.android.com/about/versions/17/behavior-changes-17
- developer.android.com/about/versions/17/changes/bg-audio
- developer.android.com/develop/background-work/services/fgs/service-types
- developer.android.com/media/platform/sharing-audio-input
- developer.android.com/reference/android/media/midi/MidiManager
- developer.android.com/reference/android/view/Choreographer
- developer.android.com/develop/ui/compose/phases
- developer.android.com/training/permissions/requesting
- developer.android.com/develop/ui/compose/layouts/adaptive/support-multi-window-mode
- developer.android.com/guide/topics/manifest/service-element

À relire avant l'implémentation (points marqués [S]) :

- developer.android.com/training/articles/perf-jni (UTF-8 modifié)
- documentation Oboe et AAudio sur les flux déconnectés
- référence `ActivityManager.clearApplicationUserData`
- référence `ComponentCallbacks2` (niveaux de `onTrimMemory`)
- sqlite.org/pragma.html (`synchronous` par connexion)
- permission `DYNAMIC_RECEIVER_NOT_EXPORTED_PERMISSION` d'AndroidX Core

## Suite donnée (architecte, DAT v0.2, 2026-11-08)

| ID | Statut | Où c'est traité |
|---|---|---|
| DA-01 | Intégrée ; NI-1 transmise au PO (MDAT-30) | [04 §1](../04-application-android.md#1-socle) et [§4](../04-application-android.md#4-état-et-changements-de-configuration), [08 §1](../08-strategie-de-test-et-banc-d-evaluation.md#1-niveaux-de-test) |
| DA-02 | Intégrée | [04 §4](../04-application-android.md#4-état-et-changements-de-configuration) |
| DA-03 | Intégrée ; NI-2 transmise (MDAT-18, MDAT-31) | [04 §6](../04-application-android.md#6-cycle-de-vie-dune-séance-et-interruptions) |
| DA-04 | Intégrée | [04 §6](../04-application-android.md#6-cycle-de-vie-dune-séance-et-interruptions), [12 POC-02](../12-specifications-techniques-des-poc.md#poc-02--latence-et-captation-sur-lappareil) |
| DA-05 | Intégrée ; NI-3 transmise (MDAT-32) | [04 §6](../04-application-android.md#6-cycle-de-vie-dune-séance-et-interruptions) |
| DA-06 | Intégrée | [04 §6](../04-application-android.md#6-cycle-de-vie-dune-séance-et-interruptions), [03 §3.1](../03-noyau-natif.md#31-captation-audio) |
| DA-07 | Intégrée | [04 §6](../04-application-android.md#6-cycle-de-vie-dune-séance-et-interruptions), [06 §4](../06-donnees-et-stockage.md#4-fichiers) |
| DA-08 | Intégrée | [04 §4](../04-application-android.md#4-état-et-changements-de-configuration), [12 POC-02](../12-specifications-techniques-des-poc.md#poc-02--latence-et-captation-sur-lappareil) |
| DA-09 | Intégrée | [04 §1](../04-application-android.md#1-socle), [07 §7](../07-import-et-rendu-de-partition.md#7-bande-horizontale-et-curseur) |
| DA-10 | Intégrée | [04 §5](../04-application-android.md#5-permissions) |
| DA-11 | Intégrée | [04 §6](../04-application-android.md#6-cycle-de-vie-dune-séance-et-interruptions), [05 §3](../05-flux-temps-reel-et-performance.md#3-budgets-de-latence-par-étape) |
| DA-12 | Intégrée | [04 §5](../04-application-android.md#5-permissions) |
| DA-13 | Intégrée (syntaxe de l'annexe A reprise) | [06 §8](../06-donnees-et-stockage.md#8-sauvegarde-android-désactivée) |
| DA-14 | Intégrée | [06 §5](../06-donnees-et-stockage.md#5-durabilité-et-arrêts-brutaux), [08 §6](../08-strategie-de-test-et-banc-d-evaluation.md#6-robustesse-et-données) |
| DA-15 | Intégrée (mécanisme a) ; texte de l'écran transmis au PO (MDAT-33) | [06 §7](../06-donnees-et-stockage.md#7-export-et-suppression) |
| DA-16 | Intégrée | [ADR-0021](../adr/0021-import-en-processus-isole.md), [04 §2](../04-application-android.md#2-couches-et-modules), [09 §2](../09-securite-confidentialite-licences.md#2-fichiers-importés-non-fiables) |
| DA-17 | Intégrée ; choix de `application/octet-stream` transmis au PO (MDAT-34) | [06 §4](../06-donnees-et-stockage.md#4-fichiers), [07 §1](../07-import-et-rendu-de-partition.md#1-vue-densemble) |
| DA-18 | Intégrée | [07 §6](../07-import-et-rendu-de-partition.md#6-rendu-et-mise-en-cache), [ADR-0007](../adr/0007-affichage-liste-d-affichage-compose.md) |
| DA-19 | Intégrée | [04 §7](../04-application-android.md#7-pont-jni), [ADR-0003](../adr/0003-pont-jni-et-echanges.md) |
| DA-20 | Intégrée | [04 §2](../04-application-android.md#2-couches-et-modules), [02](../02-vue-d-ensemble.md) |
| DA-21 | Intégrée ; registre de la bêta (MDAT-38) | [03 §3.2](../03-noyau-natif.md#32-midi), [ADR-0005](../adr/0005-midi-midimanager-amidi.md) |
| DA-22 | Intégrée | [04 §8](../04-application-android.md#8-variantes-de-construction), [08 §5](../08-strategie-de-test-et-banc-d-evaluation.md#5-simulation-midi) |
| DA-23 | Intégrée | [04 §8](../04-application-android.md#8-variantes-de-construction), [ADR-0013](../adr/0013-son-en-memoire-uniquement.md), [10 §4](../10-build-ci-et-distribution.md#4-intégration-continue) |
| DA-24 | Intégrée | [09 §1](../09-securite-confidentialite-licences.md#1-permissions-et-absence-de-réseau), [04 §5](../04-application-android.md#5-permissions) |
| DA-25 | Intégrée | [04 §6](../04-application-android.md#6-cycle-de-vie-dune-séance-et-interruptions) |
| DA-26 | Intégrée | [08 §1](../08-strategie-de-test-et-banc-d-evaluation.md#1-niveaux-de-test), [10 §4](../10-build-ci-et-distribution.md#4-intégration-continue) |
| DA-27 | Intégrée | [05 §5.1](../05-flux-temps-reel-et-performance.md#51-instrumentation-interne-variante-interne), [05 §6](../05-flux-temps-reel-et-performance.md#6-ressources-batterie-échauffement-mémoire) |
| DA-28 | Intégrée ; texte transmis au PO (MDAT-35) | [04 §6](../04-application-android.md#6-cycle-de-vie-dune-séance-et-interruptions) |
| DA-29 | Intégrée | [04 §5](../04-application-android.md#5-permissions) et [§6](../04-application-android.md#6-cycle-de-vie-dune-séance-et-interruptions) |

Bilan : 29 intégrées, 0 rejetée, 0 en attente. NI-1 à NI-3 sont reprises au [fichier 13 §3](../13-risques-techniques-et-questions.md#3-incohérences-relevées-dans-ledb-et-le-prd) et au [fichier 15](../15-modifications-edb-prd-proposees.md). Arbitrage : ARB-08 ([README](../README.md)).
