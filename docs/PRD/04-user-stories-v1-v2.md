# 04 — User stories V1 et V2

Stories au **niveau résumé** : titre, exigences couvertes, critères principaux. Elles seront détaillées au format du [fichier 03](03-user-stories-mvp.md) avant d'entrer dans un incrément (voir la [définition de prêt](07-definition-of-ready-done.md)).

Le contenu de la V1 dépend aussi des résultats du MVP et de la bêta : il sera revu au jalon J3.

---

## 1. V1

### EP-13 — Import PDF par reconnaissance et correction (OMR)

Prérequis : POC-03 concluant ; [QO-04](../EDB/12-risques-hypotheses-questions.md) tranchée (sur l'appareil, sur l'ordinateur de l'utilisateur ou sur un serveur gratuit, compte tenu du budget nul).

| ID | Titre | Exigences | Critères principaux |
|---|---|---|---|
| US-OMR-01 | Importer un PDF de partition | EF-IMP-03, ENF-PERF-07 | PDF générés reconnus sous le seuil d'erreur fixé après POC-03 ; PDF scannés ≥ 300 dpi acceptés, les autres avec avertissement ; plusieurs pages = une partition ; progression affichée, notification à la fin ; 4 pages en ≤ 60 s [HYP] |
| US-OMR-02 | Vérifier le résultat en vis-à-vis de l'original | EF-IMP-07 | Image d'origine et résultat côte à côte ou superposés ; zones à faible confiance surlignées ; navigation de zone douteuse en zone douteuse ; écoute du résultat |
| US-OMR-03 | Corriger simplement la partition reconnue | EF-IMP-08, RG-02, RG-03 | Hauteur, altération, durée, ajout et suppression de note, armure, chiffrage ; chaque correction annulable ; identifiants des notes non modifiées conservés ; séances antérieures signalées |
| US-OMR-04 | Comprendre ce qui nécessite une connexion | ENF-OFF-02, ENF-SEC-05 | Si un traitement à distance est retenu : indication claire, file d'attente hors ligne, fichiers supprimés après traitement dans le délai fixé |

### EP-14 — Confort de lecture et organisation (CNT)

| ID | Titre | Exigences | Critères principaux |
|---|---|---|---|
| US-CNT-01 | Choisir la partie que je joue | EF-IMP-11 | Choix d'une ou plusieurs parties ; les autres affichées et jouées en accompagnement, non analysées |
| US-CNT-02 | Organiser sa bibliothèque | EF-BIB-02 | Étiquettes libres, niveau déclaratif, favoris, statut (à découvrir, en cours, maîtrisé) ; filtres ; étiquette à l'import |
| US-CNT-03 | Basculer entre mode page et défilement | EF-VIS-02 | Ajoute le mode non retenu au MVP ([QO-27](09-decisions-a-prendre.md) : bande horizontale en paysage, sauf conclusion contraire du test du porteur ou de la bêta), en principe le mode page pour la tablette et le portrait ; changement sans perte de position |
| US-CNT-04 | Voir un clavier virtuel | EF-VIS-07 | Touches à jouer (lecture) et jouées (pratique) ; étendue adaptée au passage ; attendu / joué et MG / MD distingués sans dépendre de la seule couleur |
| US-CNT-05 | Afficher le nom des notes | EF-VIS-08, ENF-I18N-02 | Noms sous les têtes de note en notation latine ou anglo-saxonne ; option notation allemande (H) |
| US-CNT-06 | Choisir un thème sombre ou contrasté | EF-VIS-10, ENF-ACC-03 | Clair, sombre, contraste élevé ; contrastes WCAG 2.2 AA pour textes et commandes |
| US-CNT-07 | Suivre tous les sauts de la partition | EF-LEC-07, EF-ANA-10 | D.C., D.S., al Fine, al Coda en lecture, curseur et analyse ; option pour ignorer les reprises |
| US-CNT-08 | Régler séparément les volumes | EF-LEC-10 | Volumes de la lecture, du métronome et des sons de retour |

### EP-15 — Pratique et captation avancées (PRV)

| ID | Titre | Exigences | Critères principaux |
|---|---|---|---|
| US-PRV-01 | Être écouté au micro dans tous les modes | EF-ECO-04 | Jeu continu (si non livré au MVP), boucle, suivi au micro ; seuils de qualité micro atteints sur au moins 3 appareils et plusieurs pianos (bêta) |
| US-PRV-02 | Connecter un piano en Bluetooth MIDI | EF-ECO-03 | Écran d'appairage intégré (E20) ; sans permission de localisation ; latence et gigue compensées par l'horodatage |
| US-PRV-03 | Être suivi à mon propre tempo | EF-PRA-04 | Curseur et tournes suivent le joueur ; tolère rubato modéré, hésitations, petites erreurs ; recalage en ≤ 2 mesures [HYP] après un retour en arrière |
| US-PRV-04 | Travailler en boucle progressive | EF-PRA-06 | Tempo de départ, cible, incrément, seuil de réussite ; redescente après N échecs ; synthèse des scores et tempos de chaque boucle |
| US-PRV-05 | Être accompagné par l'autre main | EF-PRA-07, EF-ECO-08 | L'application joue l'autre main au tempo du jeu ; au micro, l'accompagnement n'est jamais analysé comme du jeu (soustraction des notes connues, écouteurs recommandés) |
| US-PRV-06 | Enregistrer une séance | EF-ECO-09 | Audio désactivé par défaut, activable globalement ou par séance ; MIDI enregistré ; stockage local ; suppression unitaire ou en masse |
| US-PRV-07 | Commander la séance sans toucher l'écran | ENF-ACC-01 | Pause et reprise par une action au clavier MIDI ou une pédale Bluetooth de tourne de page ; grandes cibles tactiles |

### EP-16 — Analyse étendue (ANX)

| ID | Titre | Exigences | Critères principaux |
|---|---|---|---|
| US-ANX-01 | Connaître mon tempo réel et sa stabilité | EF-ANA-05 | Tempo moyen effectif, instabilité, accélérations et ralentissements non écrits (hors rit., accel.) ([06 §3.3](../EDB/06-analyse-du-jeu-et-erreurs.md)) |
| US-ANX-02 | Avoir des résultats par main | EF-ANA-07 | Chaque note jouée attribuée à une main ; scores et retours par main ([06 §5.5](../EDB/06-analyse-du-jeu-et-erreurs.md)) |
| US-ANX-03 | Régler toutes les tolérances par niveau | EF-ANA-08 | Les trois niveaux couvrent aussi les ornements ; tolérances validées en test utilisateur (H-07) |
| US-ANX-04 | Être évalué sur les ornements | EF-ANA-09 | Trilles, mordants, gruppettos, appogiatures évalués globalement (présence, hauteurs, nombre de notes plausible) |
| US-ANX-05 | Savoir si mon appareil capte assez bien | ENF-QA-03 | Qualité micro mesurée par catégorie d'appareil ; information de l'utilisateur si son appareil n'atteint pas la qualité minimale |
| US-ANX-06 | Ajuster les paramètres d'analyse sans nouvelle version | ENF-MAINT-02 | Tolérances et seuils regroupés et versionnés ; au MVP, ils changent avec une nouvelle version (EDB v0.7) ; modification à distance seulement quand un serveur existera, compatible avec le budget nul et le « local d'abord » (à arbitrer) |

### EP-17 — Restitution étendue (RSX)

| ID | Titre | Exigences | Critères principaux |
|---|---|---|---|
| US-RSX-01 | Connaître mes passages prioritaires | EF-RES-06 | 1 à 3 passages avec type d'erreur dominant et action suggérée ; motifs regroupés ([06 §7](../EDB/06-analyse-du-jeu-et-erreurs.md)) |
| US-RSX-02 | Parcourir la liste de mes erreurs | EF-RES-07 | Liste chronologique filtrable par type et par main ; chaque entrée ouvre la partition au bon endroit |
| US-RSX-03 | Comparer avec mes séances précédentes | EF-RES-08 | Comparaison avec la séance précédente et la meilleure sur le même passage ; erreurs corrigées et nouvelles ; progrès valorisés |
| US-RSX-04 | Travailler directement un passage signalé | EF-RES-09 | Depuis un passage signalé, boucle lancée avec tempo réduit et main concernée suggérés |
| US-RSX-05 | Réécouter mon jeu | EF-RES-10 | Réécoute audio ou MIDI avec curseur synchronisé ; alternance avec la version de référence ; écran E16 |
| US-RSX-06 | Choisir un style de retour ludique ou sobre | EF-RES-12, ENF-ACC-06 | Style ludique (encouragements, animations) ou sobre ; interface utilisable par un enfant de 8 ans en style ludique ; tutoiement en style ludique, impersonnel et vouvoiement en style sobre ([QO-12](../EDB/12-risques-hypotheses-questions.md), tranchée par QO-24 g) |

### EP-18 — Progression (PRG)

| ID | Titre | Exigences | Critères principaux |
|---|---|---|---|
| US-PRG-01 | Voir mon tableau de bord | EF-PRO-01 | Temps de pratique (jour, semaine, mois), morceaux en cours, dernières séances, tendance ; écran E17 |
| US-PRG-02 | Suivre l'évolution d'un morceau | EF-PRO-03 | Courbe des scores et du tempo atteint ; écran E18 |
| US-PRG-03 | Voir où se concentrent mes erreurs | EF-PRO-04 | Carte de chaleur par mesure sur les N dernières séances, lisible sans dépendre de la seule couleur |
| US-PRG-04 | Garder un rythme de pratique | EF-PRO-05 | Série de jours, objectif quotidien, rappel optionnel (permission de notification demandée au moment de l'activation) |
| US-PRG-05 | Exporter mon historique | EF-PRO-09, RGPD-03 | Export CSV de l'historique, export d'un compte-rendu (PDF ou image) par le partage système |

### EP-19 — Comptes, profils et sauvegarde (CPX)

| ID | Titre | Exigences | Critères principaux |
|---|---|---|---|
| US-CPX-01 | Sauvegarder et restaurer mes données | EF-CPT-03, ENF-SEC-01, ENF-SEC-02, ENF-SEC-03, ENF-OFF-02, ENF-OFF-03 | Compte facultatif ; sauvegarde et restauration ; échanges chiffrés, pas de mot de passe en clair, chiffrement au repos ; indisponibilité sans impact sur la pratique ; solution compatible budget nul (à arbitrer, [QO-03](../EDB/12-risques-hypotheses-questions.md)) |
| US-CPX-02 | Gérer plusieurs profils sur un appareil | EF-CPT-04 | Profils famille, chacun avec bibliothèque et historique (ou bibliothèque partagée selon [QO-07](../EDB/12-risques-hypotheses-questions.md)) ; écran E21 ; profil mineur identifié |
| US-CPX-03 | Gérer un éventuel abonnement | EF-CPT-07 | Seulement si [QO-01](../EDB/12-risques-hypotheses-questions.md) retient un modèle payant, et compatible avec le canal public choisi avant la V1 ([QO-20](../EDB/12-risques-hypotheses-questions.md)) |

### EP-20 — Publication publique (PUB)

| ID | Titre | Exigences | Critères principaux |
|---|---|---|---|
| US-PUB-01 | Publier l'application sur le canal retenu | [QO-20](../EDB/12-risques-hypotheses-questions.md), [11 §5](../EDB/11-juridique-et-conformite.md), RGPD-08 | CGU, politique de confidentialité, mentions légales, fiches de confidentialité du store ; parcours parental selon [QO-13](../EDB/12-risques-hypotheses-questions.md) ; nom technique enregistré auprès de Google (vérification des développeurs, 2027) ; archives des sources de chaque version |
| US-PUB-02 | Remonter plantages et métriques avec consentement | ENF-MAINT-03, ENF-CONF-02, ENF-FIA-05 | Consentement préalable, aucun audio ni partition transmis ; taux de sessions sans plantage ≥ 99,5 % mesuré |
| US-PUB-03 | Tenir les performances sur 3 appareils de référence | ENF-COMP-04, ENF-PERF-08, ENF-PERF-09, ENF-RES-01, ENF-RES-02, ENF-RES-03 | 3 appareils dont 1 d'entrée de gamme (testeurs bénévoles) ; 60 images/s ; démarrage ≤ 3 s ; batterie ≤ 15 %/h [HYP] ; pas d'échauffement sur 45 min ; application ≤ 150 Mo [HYP] |
| US-PUB-04 | Être compatible avec les pianos numériques courants | ENF-COMP-05 | 6 modèles de marques différentes testés par des testeurs équipés |
| US-PUB-05 | Respecter la taille de texte du système | ENF-ACC-04 | Tailles de texte système prises en charge hors partition |
| US-PUB-06 | Utiliser une terminologie musicale juste | ENF-I18N-03 | Terminologie validée par un musicien dans chaque langue |

---

## 2. V2

### EP-21 — Import photo et autres formats (PHO)

| ID | Titre | Exigences | Critères principaux |
|---|---|---|---|
| US-PHO-01 | Photographier une partition papier | EF-IMP-04 | Détection des bords, redressement, contraste ; ajout, suppression, réordonnancement des pages ; mêmes règles que l'OMR ; avis juridique préalable sur la reproduction ([11 §1.2](../EDB/11-juridique-et-conformite.md)) |
| US-PHO-02 | Importer un fichier MuseScore | EF-IMP-09 | Selon [QO-08](../EDB/12-risques-hypotheses-questions.md) |
| US-PHO-03 | Importer MEI et ABC | EF-IMP-10 | Mêmes règles d'import et de rapport que MusicXML |

### EP-22 — Espace professeur (PRF)

| ID | Titre | Exigences | Critères principaux |
|---|---|---|---|
| US-PRF-01 | Créer une classe et inviter des élèves | EF-PRF-01 | Invitation par code ou lien ; gestion des élèves |
| US-PRF-02 | Envoyer un devoir | EF-PRF-02 | Partition, passage, main, tempo cible, échéance, annotations |
| US-PRF-03 | Consulter le travail des élèves | EF-PRF-03 | Séances et comptes-rendus, avec l'accord de l'élève |
| US-PRF-04 | Commenter une séance | EF-PRF-04 | Commentaire sur une séance ou un passage |
| US-PRF-05 | Donner son accord au partage | EF-PRF-05, RG-05 | Accord explicite de l'élève ou du responsable légal pour un mineur ; révocable |

### EP-23 — Synchronisation multi-appareils (SYN)

| ID | Titre | Exigences | Critères principaux |
|---|---|---|---|
| US-SYN-01 | Retrouver ma bibliothèque sur ma tablette | EF-BIB-04 | Bibliothèque et historique synchronisés entre appareils d'un même compte ; résolution des conflits |

### EP-24 — Motivation et suivi parental (MOT)

| ID | Titre | Exigences | Critères principaux |
|---|---|---|---|
| US-MOT-01 | Recevoir un objectif pour la prochaine séance | EF-PRO-06 | Passage, main, tempo proposés d'après l'historique |
| US-MOT-02 | Suivre la pratique de mon enfant | EF-PRO-07 | Résumé non technique (jours, durée, morceaux, tendance) ; [QO-07](../EDB/12-risques-hypotheses-questions.md) ; écran E22 |
| US-MOT-03 | Gagner des badges | EF-PRO-08 | Badges, niveaux, défis ; désactivable |
| US-MOT-04 | Comprendre mes erreurs récurrentes | EF-RES-13 | Motifs détectés (altération de l'armure oubliée, octave, confusion de clé, précipitation) formulés en langage clair ; relu par `pedagogue-piano` |

### EP-25 — Expression et outils experts (EXP)

| ID | Titre | Exigences | Critères principaux |
|---|---|---|---|
| US-EXP-01 | Être évalué sur les nuances | EF-ANA-11 | Nuances écrites comparées à l'intensité jouée, en relatif ; fiable surtout en MIDI |
| US-EXP-02 | Être évalué sur la pédale | EF-ANA-12 | MIDI uniquement |
| US-EXP-03 | Être évalué sur la durée des notes | EF-ANA-14 | Notes relâchées trop tôt ou tenues trop longtemps ; MIDI surtout |
| US-EXP-04 | Comparer en piano-roll | EF-RES-11 | Notes attendues et jouées superposées sur un axe temporel |
| US-EXP-05 | Entendre une lecture expressive | EF-LEC-08 | Nuances, pédale, articulations à la lecture |
| US-EXP-06 | Faire jouer mon piano numérique | EF-LEC-09 | Lecture envoyée au piano connecté |
| US-EXP-07 | Colorer les mains | EF-VIS-06 | Couleur distincte par main, doublée d'un autre indice visuel |
| US-EXP-08 | Annoter ma partition | EF-VIS-09 | Doigtés, textes, surlignages, masquables, conservés avec la partition |
| US-EXP-09 | Écouter puis rejouer | EF-PRA-09 | L'application joue un fragment, l'utilisateur le rejoue |
| US-EXP-10 | Jouer en mode performance | EF-PRA-10 | Morceau complet sans retour en direct, compte-rendu à la fin |
| US-EXP-11 | Naviguer avec un lecteur d'écran | ENF-ACC-05 | TalkBack pour la navigation, la bibliothèque et les comptes-rendus |
| US-EXP-12 | Pratiquer en écran partagé | ENF-COMP-06 | Multifenêtre sur tablette sans perte de captation |
| US-EXP-13 | Exporter un journal de diagnostic | ENF-MAINT-04 | Journal complet pour le support, sans audio par défaut (forme réduite au MVP : US-CNF-05) |

---

## 3. Décompte

| Version | Epics | Stories |
|---|---|---|
| V1 | EP-13 à EP-20 | 45 |
| V2 | EP-21 à EP-25 | 26 |
