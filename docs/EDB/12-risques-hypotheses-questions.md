# 12 — Risques, hypothèses et questions ouvertes

## 0. Décisions prises

| Date | Question | Décision |
|---|---|---|
| 2026-11-07 | QO-02 — Plateforme | **Android d'abord.** iOS visé à terme : les choix techniques doivent préserver la portabilité (voir [07 §14](07-contraintes-et-pistes-techniques.md#14-conséquences-des-décisions-de-cadrage)). |
| 2026-11-07 | QO-14 — Équipe, budget, échéance | **Porteur unique**, assisté d'une équipe d'agents IA (voir [docs/README](../README.md#équipe-dagents)). **Budget nul** : uniquement des outils et composants gratuits et/ou open source. **Pas d'échéance.** |
| 2026-11-07 | QO-09 — Versions minimales | **Android 13 (API 33) minimum** (voir [07 §13](07-contraintes-et-pistes-techniques.md#13-android--fonctionnalités-par-version)). iOS : sans objet pour l'instant. |
| 2026-11-07 | QO-19 — Licence du projet | **Projet open source.** Licence retenue : **GPL-3.0-or-later**, sauf objection du porteur avant la première publication du code (voir [11 §3](11-juridique-et-conformite.md#3-licences-logicielles-et-contenus)). |
| 2026-11-07 | QO-21 — Matériel de test | Un téléphone **Android 14**, l'**émulateur Android** (gratuit), un **piano droit acoustique**. **Pas de piano numérique.** Conséquences : voir QO-22. |
| 2026-11-07 | QO-22 — Place du micro dans le MVP | **Décidé** : la captation micro entre dans le MVP (au moins le mode attente), car le seul piano disponible est acoustique. Le chemin MIDI reste développé et testé avec des entrées simulées (voir [04 §2](04-perimetre-fonctionnel.md#2-découpage-en-versions)). |
| 2026-11-07 | QO-10 — Son de piano embarqué | **Banque légère libre embarquée** (exemple vérifié : « Upright Piano KW », CC0, variante *small* : 2,9 à 6,0 Mio selon le format ; SF2 complet : 27 Mio) ; banque de meilleure qualité téléchargeable plus tard ; qualité validée à l'oreille par le porteur. Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-11 — Conservation du détail des séances | **Tout garder au MVP** ; mesurer la place occupée pendant la bêta, puis décider d'un compactage avant la V1. Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-12 — Tutoiement ou vouvoiement | Tranchée par QO-24 g : au MVP, formulations **impersonnelles**, **vouvoiement** quand il faut s'adresser à l'utilisateur ; tutoiement réservé au style ludique de la V1 ([10 §6](10-ecrans-et-ergonomie.md#6-contenus-et-ton)). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-16 — Langues au lancement | Tranchée par QO-24 f : **français et anglais (américain) dès le MVP**, avec un lexique bilingue validé par `pedagogue-piano` (ENF-I18N-01, ENF-I18N-03). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-20 — Canal de distribution | **APK sur la page de publication du dépôt** pour l'alpha et la bêta, avec le compte gratuit de « distribution limitée » de Google ; canal public (F-Droid ou Google Play) choisi avant la V1 ([11 §4](11-juridique-et-conformite.md#4-distribution-et-commerce)). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-23 — Protections nécessaires au micro | **Les trois au MVP**, en forme minimale : tolérance de diapason (EF-ECO-10), indicateur de signal (EF-ECO-07), sons de l'application non pris pour du jeu (EF-ECO-08 : métronome et décompte visuels, écouteurs filaires). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-24 — Sept arbitrages de périmètre | a : jusqu'à 3 passages à retravailler avec lien « Voir sur la partition » ; b : autre main jouée en MIDI, au micro seulement avec écouteurs filaires ; c : trois niveaux d'exigence et reprises simples analysées au MVP ; d : pas d'étiquette à l'import au MVP ; e : thème clair seul au MVP ; f : français et anglais ; g : style unique sobre et encourageant ([04 §3](04-perimetre-fonctionnel.md#3-fonctionnalités-par-version), [05](05-exigences-fonctionnelles.md)). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-25 — Arrêt, pause et fin de séance | En jeu continu, **pause** après 7 / 6 / 5 s sans attaque (indulgent / standard / exigeant) ; **reprise en jouant** une note (décompte, reprise au début de la mesure précédente, un arrêt compté) ; **fin** après 30 s de pause ; pas de pause avant 2 min en mode attente ; seuil d'arrêt = max(seuil du niveau ; 2 temps) (EF-PRA-08, [06 §4.1](06-analyse-du-jeu-et-erreurs.md#41-niveaux-dexigence)). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-26 — Boucle de pratique | **Oui au MVP** : répétition du passage en jeu continu, 3 fois par défaut, décompte et score à chaque répétition, tempo constant (nouvelle exigence EF-PRA-11). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-27 — Affichage de la partition sur le téléphone | **Décidé par un test au pupitre fait par le porteur seul** avant I1 (il ne peut pas réunir d'autres lecteurs) ; position retenue en attendant : **bande horizontale en paysage**, qui avance par paliers. La décision issue du test est **provisoire** et sera reconfirmée en bêta avec des testeurs (EF-VIS-02). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-28 — Retours des testeurs sans serveur | Bouton « Ce n'était pas une erreur » dans l'application et **rapport de test anonymisé** exporté volontairement par e-mail, avec un questionnaire court ; conditions de protection des données de [11 §2.4](11-juridique-et-conformite.md#24-rapport-de-test-de-la-bêta). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-29 — Présentation du jeu continu au micro | Si POC-01 réussit en jeu continu : décision du porteur au vu du rapport relu par `qa-testeur` et `architecte` ; fonction marquée **« expérimental »**, sans retour en direct par défaut, revérifiée en bêta ([13 §3](13-jalons-et-demarche.md#3-phase-1--preuves-de-concept-poc)). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-30 — Démarrer « voir et entendre » avant J1 | **Oui** : l'import, l'affichage et la lecture démarrent dès que POC-04 est concluant, sans attendre J1. Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-31 — Morceaux de démonstration | **Gravure maison en priorité**, publiée sous **CC0**, complétée si besoin par des fichiers libres existants (Mutopia, CC BY ou CC BY-SA) ; compositeurs morts avant 1900 ; **saisie par un agent**, relue note par note par `pedagogue-piano`, puis **validée à l'écoute par le porteur** (EF-BIB-05). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-32 — Précision du rythme au micro | Tolérance et fenêtre d'accord jamais sous **2σ** de l'incertitude de détection ; niveau exigeant au micro seulement si 2σ ≤ 50 ms ; marque de rythme à 1,5 fois la tolérance ; formule précisée le 2026-11-08 (par registre, T_eff = max(T ; 2σ ; u), W_eff = max(W ; 2√2·σ), exigeant si max(2σ ; u) ≤ 50 ms en médium) ([06 §4.1](06-analyse-du-jeu-et-erreurs.md#41-niveaux-dexigence)). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-34 — Identité affichée | **Nom civil et adresse e-mail personnelle** du porteur (option C), soit Ronan Launay et rlaunay@free.fr (fournis le 2026-11-08) ([11 §5](11-juridique-et-conformite.md#5-documents-à-produire-avant-publication)). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-35 — Nom technique et clé de signature | **Nom technique définitif et clé de diffusion pérenne** fixés avant I0, clé hors du dépôt et sauvegardée hors ligne ([11 §4](11-juridique-et-conformite.md#4-distribution-et-commerce)). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-36 — Sauvegarde automatique d'Android | **Désactivée** ; rappel d'exporter ses partitions avant de changer de téléphone ; sauvegarde maîtrisée possible en V1 (ENF-CONF-01). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-37 — Jeux de données non commerciaux | **Utilisables** pour des mesures complémentaires, à conditions strictes : hors du dépôt et de l'application, téléchargés par un script, jamais pour entraîner un modèle, hors du contrôle bloquant ([06 §9.1](06-analyse-du-jeu-et-erreurs.md#91-corpus-de-test)). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-38 — Licence de la documentation | **CC BY-SA 4.0** ([11 §3](11-juridique-et-conformite.md#3-licences-logicielles-et-contenus)). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-39 — Calcul du score | **Formule multiplicative** du score global ; en mode attente, note juste seulement du premier coup ; continuité rapportée à la longueur jouée ([06 §5](06-analyse-du-jeu-et-erreurs.md#5-calcul-des-scores)). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-40 — Points d'orgue et ralentis écrits | **Rythme non évalué** autour des points d'orgue et césures et sous rit., rall., allarg., accel. ; les notes restent évaluées ([06 §4.3](06-analyse-du-jeu-et-erreurs.md#43-cas-particuliers)). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-41 — Modèle entraîné sur des données non commerciales | **Accepté au MVP** si les poids sont sous licence libre, risque résiduel documenté ; avis d'avocat avant la V1 si une version payante est envisagée ; modèles à licence NC ou à usage restreint exclus ([11 §3](11-juridique-et-conformite.md#3-licences-logicielles-et-contenus)). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-42 — Si le micro échoue même en mode attente | Micro limité aux morceaux à une main, « expérimental », **si ceux-ci atteignent les objectifs** ; **sinon**, retravailler la reconnaissance avant de continuer ; la partie du MVP indépendante du micro continue ([13 §3](13-jalons-et-demarche.md#3-phase-1--preuves-de-concept-poc)). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-07 | QO-43 — Délai du compte-rendu au micro | **Décider après mesure** : mesurer en I5, puis choisir entre garder 2 s et accepter jusqu'à 10 s avec progression (ENF-PERF-04). Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-08 | QO-44 — Tests qui demandent plusieurs personnes avant la bêta | **Option A** : le porteur fait seul les tests de lisibilité (I1) et de premier lancement (I4) ; ils sont refaits avec les premiers testeurs bénévoles de la bêta et inscrits au registre des vérifications reportées ; J2 garde ses 3 testeurs, recrutés pour la bêta fermée. Détail : [PRD 09](../PRD/09-decisions-a-prendre.md). |
| 2026-11-08 | QO-45 — Hébergement du code et des vérifications automatiques | **GitHub** (option A), avec trois précautions : machines de vérification d'une version figée (Ubuntu 24.04), clé de signature dans un espace protégé qui exige l'accord du porteur, copie de secours du code ([07 §14.2](07-contraintes-et-pistes-techniques.md#142-budget-nul)). Détail : [DAT 14](../DAT/14-decisions-a-prendre.md). |
| 2026-11-08 | QO-46 — Identifiant technique définitif | **`io.github.zebul1n.pianohelper`** (option A) ; le porteur a un compte GitHub dont il doit fournir le nom ; un tiret dans ce nom devient `_` ; le compte ne doit jamais être renommé ([11 §4](11-juridique-et-conformite.md#4-distribution-et-commerce)). Détail : [DAT 14](../DAT/14-decisions-a-prendre.md). |
| 2026-11-08 | QO-47 — Processeurs visés par les versions diffusées | **64 bits seulement** (`arm64-v8a`, option A) ; la version de test interne ajoute le type de l'émulateur (`x86_64`) ; à réexaminer avant la V1 avec les appareils des testeurs (ENF-RES-03). Détail : [DAT 14](../DAT/14-decisions-a-prendre.md). |
| 2026-11-08 | QO-48 — Outils du poste du porteur | **WSL 2 avec Ubuntu 24.04** (option A) : Python, compilateur C++ et outils de licence, identiques aux machines de vérification ; Android Studio avec l'hyperviseur Windows pour l'émulateur ([07 §14.2](07-contraintes-et-pistes-techniques.md#142-budget-nul)). Détail : [DAT 14](../DAT/14-decisions-a-prendre.md). |
| 2026-11-08 | QO-49 — Enregistrements de piano publics | **Option A, licence CC0** : dépôt séparé `piano-helper-corpus` ; partitions, annotations et MIDI dans ce dépôt ; fichiers audio joints à chaque version, avec leurs empreintes ([06 §9.1](06-analyse-du-jeu-et-erreurs.md#91-corpus-de-test)). Détail : [DAT 14](../DAT/14-decisions-a-prendre.md). |
| 2026-11-08 | QO-50 — Moteur d'IA tout fait de Microsoft (ONNX Runtime) | **Exclu des versions diffusées** : calcul du modèle écrit dans le projet si POC-01 retient un petit modèle et que l'effort est raisonnable, sinon moteur construit par le projet sans télémétrie ; le moteur tout fait reste permis pour les essais internes ([07 §7.3](07-contraintes-et-pistes-techniques.md#73-exécution-sur-lappareil)). Détail : [DAT 14](../DAT/14-decisions-a-prendre.md). |
| 2026-11-08 | QO-51 — Permission de diffusion sur les boutiques d'applications | **Option B** : aucune permission supplémentaire (GPL-3.0 §7) n'est ajoutée pour l'instant ; la question sera reposée avant d'accepter la première contribution extérieure ([11 §3](11-juridique-et-conformite.md#3-licences-logicielles-et-contenus)). Détail : [DAT 14](../DAT/14-decisions-a-prendre.md). |
| 2026-11-08 | QO-52 — Compte-rendu du mode attente | **Option A** : le compte-rendu reprend les décisions prises pendant le jeu ; l'analyse différée, facultative dans ce mode, peut seulement rendre une décision « incertaine » (hors score), jamais créer une erreur ; le jeu continu n'est pas concerné (EF-ANA-13). Détail : [DAT 14](../DAT/14-decisions-a-prendre.md). |
| 2026-11-08 | QO-53 — Allègement des polices musicales | **Option A** : écrire à RISM pour demander le droit d'alléger les polices seulement si la taille de l'application l'exige, après POC-04 ([11 §3](11-juridique-et-conformite.md#3-licences-logicielles-et-contenus)). Détail : [DAT 14](../DAT/14-decisions-a-prendre.md). |
| 2026-11-08 | QO-55 — Cible de latence propre au grave | **Option A** : si, au micro, seul le grave dépasse 150 ms alors que le médium tient la cible, une cible propre au grave est retenue, avec une valeur fixée au vu des mesures de POC-01 ; 150 ms ailleurs (ENF-PERF-01). Appliquée au jalon J1, seulement si la mesure l'exige. Détail : [DAT 14](../DAT/14-decisions-a-prendre.md). |

Décisions du 2026-11-07 (QO-10 à QO-43) : le porteur a validé en bloc les recommandations du [PRD, fichier 09](../PRD/09-decisions-a-prendre.md), sauf QO-34 (option C) ; il a précisé QO-31 (saisie) et QO-27 (test fait seul). QO-33 avait été close par la v0.5. Les actions qui restent au porteur sont regroupées après les décisions du 2026-11-08, ci-dessous.

Décisions du 2026-11-08 (QO-44 à QO-53 et QO-55) : le porteur a validé **en bloc les recommandations** du [DAT 14](../DAT/14-decisions-a-prendre.md) (et de [PRD 09](../PRD/09-decisions-a-prendre.md) pour QO-44), avec trois précisions : **QO-46**, format `io.github.zebul1n.pianohelper` retenu, compte GitHub **Zebul1n** (fourni le 2026-11-08) ; **QO-51**, option B (aucune option n'était recommandée) ; **QO-49**, option A avec la licence **CC0**. **QO-54 reste ouverte** (diffusion de la V1 hors de Google Play, à trancher avec le canal public de QO-20). Les modifications de l'EDB proposées par le DAT (MDAT-01 à MDAT-19) sont appliquées dans l'EDB v0.7.

**Actions qui restent au porteur** (liste unique, reprise dans [PRD 09 §2.1](../PRD/09-decisions-a-prendre.md) et [DAT 14](../DAT/14-decisions-a-prendre.md)) :
1. fournir le **nom civil** et l'**adresse e-mail** à afficher, ✅ fournis le 2026-11-08 : Ronan Launay, rlaunay@free.fr (QO-34) — avant I0 ;
2. donner le **nom du compte GitHub** ✅ fourni le 2026-11-08 : Zebul1n (`io.github.zebul1n.pianohelper`) (QO-46) — avant I0 ;
3. relever sur le téléphone les **libellés Android** de la demande d'autorisation du micro — avant I3 ;
4. faire seul le **test d'affichage au pupitre** (QO-27) — avant I1 ;
5. **sauvegarder hors ligne la clé de signature** dès sa création (QO-35) — en I0 ;
6. installer **WSL 2 (Ubuntu 24.04) et Android Studio** selon la liste du [DAT 10 §7](../DAT/10-build-ci-et-distribution.md#7-prérequis-du-poste-du-porteur) (QO-48) — avant I0 et avant POC-01 et POC-05.

## 1. Questions ouvertes

Les questions marquées **bloquantes** doivent être tranchées avant le démarrage du MVP.

| ID | Question | Options | Impact | Bloquante |
|---|---|---|---|:-:|
| QO-01 | Quel modèle économique ? | Gratuit avec fonctions premium (analyse micro, OMR, progression avancée) ; abonnement mensuel / annuel ; achat unique ; licence professeur / école | Architecture des comptes, backend, CGV, priorisation | Non (MVP), oui (V1) |
| QO-02 | Quelle plateforme en premier ? | ✅ Décidé : Android d'abord, iOS à terme | Coûts, planning, choix techno | Tranchée |
| QO-03 | Le compte est-il obligatoire ? | Non (local d'abord, compte optionnel pour la sauvegarde) ; oui dès V1 | Parcours d'accueil, RGPD, monétisation | Non |
| QO-04 | OMR sur l'appareil ou sur serveur ? | Serveur (qualité, modèles lourds) ; appareil (confidentialité, hors ligne, pas de coûts serveur) ; hybride | Backend, coûts, confidentialité | Non (V1) |
| QO-05 | Proposer à terme un catalogue de partitions ? | Non ; partenariats avec éditeurs ; catalogue domaine public gravé en interne | Juridique, modèle économique | Non |
| QO-06 | Nom définitif du produit ? | — | Marque, stores | Non |
| QO-07 | Comment gérer la famille (parent / enfant) ? | Multi-profils sur un appareil ; compte parent avec profils enfants synchronisés sur plusieurs appareils | Modèle de données, RGPD mineurs | Non (V1) |
| QO-08 | Prendre en charge les fichiers MuseScore ? | Non ; conversion via un outil tiers ; lecteur maison | Coût, maintenance | Non |
| QO-09 | Versions minimales d'iOS et d'Android ? | ✅ Décidé : Android 13 (API 33) | Base installée, effort de test | Tranchée |
| QO-10 | Qualité de son embarquée vs taille de l'application ? | ✅ Décidé : banque légère libre embarquée, haute qualité téléchargeable plus tard | Taille, expérience | Tranchée |
| QO-11 | Durée de conservation du détail des séances ? | ✅ Décidé : tout au MVP ; compactage décidé avant la V1 après mesure en bêta | Stockage, fonctions de progression | Tranchée (MVP) |
| QO-12 | Tutoiement ou vouvoiement ? | ✅ Décidé (QO-24 g) : impersonnel et vouvoiement au MVP ; tutoiement dans le style ludique de la V1 | Rédaction | Tranchée |
| QO-13 | Cibler officiellement les enfants sur les stores ? | Oui (catégorie Enfants / programme Familles) ; non (public général, avec parcours parental) | Contraintes analytiques, publicité, conformité | Non (V1) |
| QO-14 | Équipe, budget et échéance cible ? | ✅ Décidé : porteur unique + agents IA, budget nul, outils gratuits / open source, sans échéance | Faisabilité du planning | Tranchée |
| QO-15 | Demander aux utilisateurs de partager des enregistrements pour améliorer l'analyse ? | Non ; oui en option explicite (opt-in) | RGPD, qualité à long terme | Non |
| QO-16 | Quelles langues au lancement ? | ✅ Décidé (QO-24 f) : français et anglais (américain) dès le MVP | Traduction, marché | Tranchée |
| QO-17 | Faut-il viser aussi le clavier « débutant » (61 touches, sans toucher dynamique) ? | Oui ; non | Tessiture, analyse de dynamique | Non |
| QO-18 | Positionnement vis-à-vis des applications concurrentes ? | Outil de travail pour ses propres partitions (positionnement actuel) ; hybride avec contenus pédagogiques | Marketing, périmètre | Non |
| QO-19 | Le projet sera-t-il lui-même open source, et sous quelle licence ? | ✅ Décidé : open source, GPL-3.0-or-later (sauf objection) | Composants tiers utilisables (LGPL, GPL, AGPL), distribution sur F-Droid, contributions externes (voir [11 §3](11-juridique-et-conformite.md#3-licences-logicielles-et-contenus)) | Tranchée |
| QO-20 | Quel canal de distribution ? | ✅ Décidé pour l'alpha et la bêta : APK sur la page de publication du dépôt, avec le compte gratuit de distribution limitée de Google. Canal public à choisir avant la V1 : Google Play (inscription payante, 25 $ une seule fois) ou F-Droid (gratuit, application entièrement libre exigée) | Budget, visibilité, mises à jour, vérification des développeurs (2027) | Tranchée (bêta) ; oui (V1) |
| QO-21 | De quel matériel de test dispose-t-on ? | ✅ Réponse : téléphone Android 14, émulateur, piano droit acoustique, pas de piano numérique | Faisabilité des POC-01 et POC-02 sans budget | Tranchée |
| QO-22 | La captation micro entre-t-elle dans le MVP ? | ✅ Décidé : oui, au moins en mode attente ; MIDI testé par entrées simulées | Périmètre du MVP, ordre des POC, possibilité pour le porteur d'utiliser lui-même l'application | Tranchée |

### Questions ouvertes par le PRD (QO-23 et suivantes)

Elles sont décrites en détail dans le [PRD, fichier 09](../PRD/09-decisions-a-prendre.md) ; les décisions sont résumées au §0.

| ID | Question | État |
|---|---|---|
| QO-23 | Avancer au MVP trois protections nécessaires au micro ? | ✅ Tranchée le 2026-11-07 |
| QO-24 | Sept arbitrages de périmètre (a à g) | ✅ Tranchée le 2026-11-07 (absorbe QO-12 et QO-16) |
| QO-25 | Arrêt, pause et fin de séance | ✅ Tranchée le 2026-11-07 |
| QO-26 | Boucle de pratique au MVP ? | ✅ Tranchée le 2026-11-07 |
| QO-27 | Mode d'affichage du MVP | ✅ Tranchée le 2026-11-07 (test au pupitre par le porteur, à reconfirmer en bêta) |
| QO-28 | Retours des testeurs sans serveur | ✅ Tranchée le 2026-11-07 |
| QO-29 | Présentation du jeu continu au micro | ✅ Tranchée le 2026-11-07 (appliquée à J1) |
| QO-30 | Démarrer « voir et entendre » avant J1 ? | ✅ Tranchée le 2026-11-07 |
| QO-31 | Morceaux de démonstration : source, saisie, licence | ✅ Tranchée le 2026-11-07 |
| QO-32 | Précision du rythme au micro | ✅ Tranchée le 2026-11-07 (valeurs mesurées par POC-01) |
| QO-33 | Critères du MVP après la décision QO-22 | ✅ Close par l'EDB v0.5 |
| QO-34 | Identité affichée | ✅ Tranchée le 2026-11-07 (valeurs à fournir) |
| QO-35 | Nom technique et clé de signature | ✅ Tranchée le 2026-11-07 |
| QO-36 | Sauvegarde automatique d'Android | ✅ Tranchée le 2026-11-07 |
| QO-37 | Jeux de données non commerciaux pour l'évaluation | ✅ Tranchée le 2026-11-07 |
| QO-38 | Licence de la documentation | ✅ Tranchée le 2026-11-07 |
| QO-39 | Calcul du score | ✅ Tranchée le 2026-11-07 |
| QO-40 | Points d'orgue et ralentis écrits | ✅ Tranchée le 2026-11-07 |
| QO-41 | Modèle entraîné sur des données non commerciales | ✅ Tranchée le 2026-11-07 |
| QO-42 | Si le micro échoue même en mode attente | ✅ Tranchée le 2026-11-07 (appliquée à J1 si le cas se produit) |
| QO-43 | Délai du compte-rendu au micro | ✅ Tranchée le 2026-11-07 (choix final après mesure en I5) |
| QO-44 | Tests qui demandent plusieurs personnes avant la bêta (lisibilité, premier lancement, J2) | ✅ Tranchée le 2026-11-08 (option A : tests faits seul par le porteur, refaits en bêta) |

### Questions ouvertes par le DAT (QO-45 et suivantes)

Elles sont décrites en détail dans le [DAT 14](../DAT/14-decisions-a-prendre.md) ; les décisions sont résumées au §0.

| ID | Question | État |
|---|---|---|
| QO-45 | Où héberger le code et les vérifications automatiques ? | ✅ Tranchée le 2026-11-08 (GitHub) |
| QO-46 | Quel identifiant technique définitif donner à l'application ? | ✅ Tranchée le 2026-11-08 (`io.github.zebul1n.pianohelper`, compte GitHub Zebul1n) |
| QO-47 | Pour quels processeurs construire l'application diffusée ? | ✅ Tranchée le 2026-11-08 (64 bits seulement) |
| QO-48 | Quels outils installer sur l'ordinateur du porteur ? | ✅ Tranchée le 2026-11-08 (WSL 2 avec Ubuntu 24.04, Android Studio ; installation à faire) |
| QO-49 | Où ranger les enregistrements de piano publics, et sous quelle licence ? | ✅ Tranchée le 2026-11-08 (dépôt séparé, CC0) |
| QO-50 | Exclure des versions diffusées le moteur d'IA tout fait de Microsoft ? | ✅ Tranchée le 2026-11-08 (oui ; choix entre calcul maison et moteur construit par le projet au jalon J1) |
| QO-51 | Autoriser dès maintenant, dans la licence, la diffusion sur les boutiques d'applications ? | ✅ Tranchée le 2026-11-08 (non pour l'instant ; à reposer avant la première contribution extérieure) |
| QO-52 | En mode attente, le compte-rendu peut-il ajouter des erreurs non montrées pendant le jeu ? | ✅ Tranchée le 2026-11-08 (non) |
| QO-53 | Demander à l'éditeur des polices musicales le droit de les alléger ? | ✅ Tranchée le 2026-11-08 (seulement si la taille l'exige, après POC-04) |
| QO-54 | Comment diffuser la V1 hors de Google Play après 2027 (compte complet à 25 USD) ? | Ouverte, à trancher avec le canal public (QO-20) avant la V1 ; voir le [DAT 14](../DAT/14-decisions-a-prendre.md) |
| QO-55 | Si le micro est trop lent dans le grave seulement, accepter une cible propre au grave ? | ✅ Tranchée le 2026-11-08 (oui, si le médium tient la cible ; appliquée à J1 si la mesure l'exige) |

## 2. Hypothèses

| ID | Hypothèse | Comment la vérifier |
|---|---|---|
| H-01 | Les apprenants veulent travailler **leurs** partitions plutôt qu'un catalogue imposé. | Entretiens utilisateurs (10 à 15), test de l'alpha |
| H-02 | Une proportion significative des utilisateurs cibles ont un piano numérique connectable (USB/Bluetooth). | Enquête courte auprès de la cible ; données de marché |
| H-03 | La captation micro peut atteindre ≤ 5 % de fausses erreurs sur des pièces de niveau débutant à intermédiaire. | POC-01 |
| H-04 | Un retour hiérarchisé (1 à 3 passages prioritaires) est plus utile qu'une liste exhaustive. | Test utilisateur sur maquettes |
| H-05 | Les professeurs prescriront l'application si l'analyse est fiable. | Entretiens avec 5 à 10 professeurs |
| H-06 | Les partitions des utilisateurs sont majoritairement en PDF. | Enquête ; observation lors des tests |
| H-07 | Les tolérances proposées en [06 §4](06-analyse-du-jeu-et-erreurs.md#4-règles-de-tolérance) sont perçues comme justes. | Tests utilisateurs par niveau |
| H-08 | Le mode attente est le mode le plus utilisé par les débutants. | Télémétrie de l'alpha |
| H-09 | Une tablette posée sur le pupitre capte assez bien un piano droit. | POC-01 |

## 3. Risques

Échelle : Probabilité (P) et Impact (I) de 1 (faible) à 3 (fort). Criticité = P × I.

| ID | Risque | P | I | Crit. | Mitigation |
|---|---|:-:|:-:|:-:|---|
| R-01 | La transcription au micro génère trop de fausses erreurs ; l'utilisateur perd confiance. | 3 | 3 | **9** | MIDI (simulé) pour valider le moteur d'analyse avec des entrées exactes ; analyse informée par la partition ; filtrage par confiance ; micro limité au mode attente au MVP si POC-01 l'impose ; évaluation continue sur corpus |
| R-02 | L'OMR est trop imparfaite sur les partitions de piano ; l'import PDF déçoit. | 3 | 2 | **6** | Écran de vérification et de correction ; communication honnête (« bêta ») ; solution commerciale si nécessaire ; MusicXML mis en avant |
| R-03 | Latence audio trop élevée sur une partie du parc Android. | 2 | 2 | 4 | Mesure par appareil ; compensation par calibrage ; liste d'appareils recommandés ; modes moins sensibles à la latence (attente) |
| R-04 | L'alignement se perd quand l'utilisateur hésite, recommence ou saute des passages. | 2 | 3 | **6** | POC-05 dédié ; corpus avec jeux hésitants ; resynchronisation |
| R-05 | Concurrence forte d'applications établies avec catalogues et budgets marketing. | 3 | 2 | **6** | Positionnement différenciant (ses propres partitions, outil de travail, précision) ; cible professeurs |
| R-06 | Problème de licence (LGPL/AGPL, sons, modèles, données) découvert tard. | 2 | 2 | 4 | Inventaire des licences dès les POC ; revue juridique avant MVP |
| R-07 | Taille de l'application trop importante (modèles, sons). | 2 | 1 | 2 | Contenus téléchargeables à la demande ; compression des modèles |
| R-08 | Consommation de batterie et échauffement en séance longue. | 2 | 2 | 4 | Mesure dès le POC ; analyse différée allégée ; modes économes |
| R-09 | Non-conformité RGPD sur les mineurs. | 1 | 3 | 3 | Revue juridique ; parcours parental ; local d'abord |
| R-10 | Rendu de partition incorrect sur des fichiers MusicXML « exotiques ». | 2 | 2 | 4 | Corpus de fichiers issus de plusieurs éditeurs ; rapport d'import ; réimport possible |
| R-11 | Sous-estimation de l'effort global (sujet multidisciplinaire : audio, IA, gravure musicale, mobile). | 3 | 3 | **9** | POC avant engagement ; MVP resserré ; recours à des bibliothèques éprouvées ; expertise musicale dans l'équipe |
| R-12 | Retour perçu comme décourageant (trop d'erreurs affichées). | 2 | 2 | 4 | Hiérarchisation, niveaux d'exigence, style ludique, tests utilisateurs |
| R-13 | Budget nul : parc de test limité aux appareils disponibles, pas de serveur payant, pas de solution commerciale (OMR, sons). | 3 | 2 | **6** | Local d'abord ; composants open source uniquement ; appel à des testeurs bénévoles équipés d'appareils variés ; OMR sur l'appareil ou sur ordinateur |
| R-14 | Porteur unique : disponibilité, et dépendance à des agents IA qui peuvent produire des erreurs plausibles. | 2 | 3 | **6** | Revue croisée entre agents (QA, juriste, pédagogue), tests automatisés, décisions consignées, validation humaine des choix structurants |
| R-15 | Un seul appareil physique et un seul piano : les mesures de qualité et de latence risquent de ne pas être représentatives des autres appareils et pianos. | 3 | 2 | **6** | Résultats présentés comme valables pour cet appareil seulement ; jeux de données publics pour l'évaluation ; recrutement de testeurs bénévoles équipés différemment avant la V1 |
| R-16 | Aucun piano numérique : le chemin MIDI ne peut pas être testé en conditions réelles. | 3 | 1 | 3 | Entrées MIDI simulées (fichiers rejoués, périphérique MIDI virtuel, clavier visuel) ; tests réels confiés à des testeurs bénévoles équipés |
