# 14 — Décisions qui reviennent au porteur

Ce fichier rassemble **toutes** les décisions techniques qui reviennent au porteur du projet après la relecture du DAT v0.1 par cinq spécialistes ([revues](revues/)). Il remplace, pour la prise de décision, le §2 du [fichier 13](13-risques-techniques-et-questions.md). Pour avoir une seule liste, il reprend aussi la question QO-44 du PRD (texte complet dans [PRD 09 §2.3](../PRD/09-decisions-a-prendre.md)).

**Le porteur a tranché le 2026-11-08** toutes les questions de ce fichier sauf QO-54 :
1. il valide **en bloc les recommandations** : QO-44, QO-45, QO-46, QO-47, QO-48, QO-50, QO-52, QO-53 et QO-55 prennent l'option recommandée ;
2. **QO-46** : l'identifiant est fixé au format `io.github.zebul1n.pianohelper`, sur le compte GitHub du porteur, **Zebul1n** (fourni le 2026-11-08 ; en minuscules dans l'identifiant) ;
3. **QO-51** : **option B**, aucune permission supplémentaire n'est ajoutée à la GPL pour l'instant ; la question sera reposée avant d'accepter la première contribution extérieure ;
4. **QO-49** : **option A**, avec la licence **CC0** ;
5. **QO-54** reste **ouverte** (urgence « plus tard », liée à QO-20).

Les décisions sont appliquées dans le DAT v0.3, l'[EDB v0.7](../EDB/README.md) (résumé en [EDB 12 §0](../EDB/12-risques-hypotheses-questions.md#0-décisions-prises)) et le [PRD v0.4](../PRD/README.md), avec les 42 modifications du [fichier 15](15-modifications-edb-prd-proposees.md). Chaque question se lit seule : la question en une phrase, ce qu'il faut savoir, les options, la recommandation (et qui la soutenait), la décision, l'impact et l'échéance.

Abréviations des relecteurs : **ARCH** (`architecte`), **MIR** (`expert-audio-mir`), **AND** (`dev-android`), **CPP** (`dev-natif-cpp`), **JUR** (`juriste-conformite`), **DEV** (`devops-release`), **PO** (`product-owner`).

## 1. Récapitulatif

| Urgence | QO | Question en une phrase | Décision du 2026-11-08 | Appliquée |
|---|---|---|---|---|
| 1 | QO-45 | Où héberger le code et les vérifications automatiques ? | **A** : GitHub, avec trois précautions | Avant I0 ; [ADR-0017](adr/0017-integration-continue-et-diffusion.md) acceptée |
| 1 | QO-46 | Quel identifiant technique définitif donner à l'application ? | **A** : `io.github.zebul1n.pianohelper` (compte GitHub Zebul1n) | Avant I0 ; [10 §3](10-build-ci-et-distribution.md#3-variantes-et-signature) |
| 1 | QO-47 | Pour quels types de processeurs construire l'application diffusée ? | **A** : processeurs 64 bits seulement ; `x86_64` dans la variante interne | Avant I0 ; réexamen avant la V1 |
| 1 | QO-48 | Quels outils installer sur votre ordinateur ? | **A** : WSL 2 avec Ubuntu 24.04, et Android Studio | Avant I0 et les POC (action 6) |
| 1 | QO-49 | Où ranger les enregistrements de piano publics, et sous quelle licence ? | **A**, licence **CC0** : dépôt séparé, fichiers audio joints aux versions | Avant I0 |
| 1 | QO-51 | Autoriser dès maintenant, dans la licence, la diffusion sur les boutiques d'applications ? | **B** : rien n'est ajouté pour l'instant | Question reposée avant la première contribution extérieure |
| 2 | QO-44 | Comment faire les tests qui demandent plusieurs personnes avant la bêta ? | **A** (PRD) : tests faits seul, refaits en bêta | I1, I4, J2 |
| 3 | QO-50 | Exclure des versions diffusées le moteur d'IA tout fait de Microsoft ? | **Oui** : C si possible, sinon B ; tout fait permis en essai interne | Jalon J1 ; [ADR-0011](adr/0011-moteur-d-inference-onnx-runtime.md) |
| 3 | QO-52 | En mode attente, le compte-rendu peut-il ajouter des erreurs qui n'ont pas été montrées pendant le jeu ? | **A** : non | Jalon J1, I2, I3 ; [ADR-0010](adr/0010-transcription-deux-etages.md) |
| 3 | QO-55 | Si le micro est trop lent dans le grave seulement, accepter une cible de temps de réaction propre au grave ? | **A** : oui, si le médium tient la cible | Jalon J1, seulement si la mesure l'exige |
| 4 | QO-53 | Demander à l'éditeur de la police musicale le droit de l'alléger ? | **A** : seulement si la taille l'exige | Après POC-04 |
| 4 | QO-54 | Comment diffuser la V1 hors de Google Play après 2027, sachant que le compte complet coûte 25 USD ? | **Ouverte** | Avec QO-20, avant la V1 |

## 2. Actions qui restent au porteur

Liste unique, qui cumule les actions du [PRD 09 §2.1](../PRD/09-decisions-a-prendre.md) et celles nées des décisions du 2026-11-08 ; la même liste figure en [EDB 12 §0](../EDB/12-risques-hypotheses-questions.md#0-décisions-prises).

| # | Action | Échéance | Pour |
|---|---|---|---|
| 1 | **Fournir le nom civil et l'adresse e-mail** à afficher, ✅ fournis le 2026-11-08 : Ronan Launay, rlaunay@free.fr (QO-34) | Avant I0 | US-CNF-02, US-CNF-05, US-CNF-06 |
| 2 | **Donner le nom du compte GitHub** ✅ fourni le 2026-11-08 : Zebul1n (identifiant `io.github.zebul1n.pianohelper`, dépôt github.com/Zebul1n) (QO-46, QO-45) | Avant I0 | US-CNF-04, [10 §3](10-build-ci-et-distribution.md#3-variantes-et-signature) |
| 3 | **Relever sur le téléphone les libellés Android** de la demande d'autorisation du micro | Avant I3 | US-ECO-03 |
| 4 | **Faire seul le test d'affichage au pupitre** (QO-27) | Avant I1 | US-VIS-02, US-VIS-05 |
| 5 | **Sauvegarder la clé de signature hors ligne** dès sa création (QO-35) | En I0 | US-CNF-04, [10 §3](10-build-ci-et-distribution.md#3-variantes-et-signature) |
| 6 | **Installer WSL 2 (Ubuntu 24.04) et Android Studio** selon la liste du [10 §7](10-build-ci-et-distribution.md#7-prérequis-du-poste-du-porteur) (QO-48) | Avant I0, et avant POC-01 et POC-05 | Banc, prototypes, émulateur |

Les modifications de l'EDB et du PRD proposées par le DAT ([fichier 15](15-modifications-edb-prd-proposees.md)) ont été validées avec les décisions et sont toutes appliquées. Reste ouverte : **QO-54** (§7).

## 3. Urgence 1 — avant la première version de test (I0)

### QO-45 — Où héberger le code et les vérifications automatiques ?

**Statut : décidée le 2026-11-08 — option A (GitHub).**

**Question.** Sur quel site publier le code du projet, ses versions et ses vérifications automatiques ?

**Ce qu'il faut savoir.** Chaque modification du code doit être vérifiée automatiquement (tests, mesure de la qualité de l'analyse, licences). Ces vérifications tournent sur des machines prêtées par l'hébergeur. QO-20 prévoit de diffuser l'application « sur la page de publication du dépôt ».

| Option | Pour | Contre |
|---|---|---|
| A — GitHub | Vérifications gratuites et illimitées pour un projet public, y compris l'émulateur Android et des machines ARM ; protection de la clé de signature par validation du porteur ; versions verrouillables | Plateforme propriétaire (Microsoft) |
| B — GitLab.com | Plateforme dont le cœur est libre | 400 minutes de vérification par mois : insuffisant |
| C — Codeberg | Association à but non lucratif | Vérifications en version d'essai, sans garantie |

**Recommandation : A** (ARCH, DEV), avec trois précautions de DEV : machines de vérification d'une version figée (Ubuntu 24.04), clé de signature dans un espace protégé qui exige votre accord, copie de secours du code (Software Heritage ou miroir en lecture seule). **Décision : A, avec les trois précautions.**
**Impact.** [ADR-0017](adr/0017-integration-continue-et-diffusion.md) passe à « Acceptée » ; chaîne du [10 §4](10-build-ci-et-distribution.md#4-intégration-continue) ; identifiant de QO-46 ; EDB 07 §14.2 ; US-CNF-04 CA2.
**Échéance.** Avant I0.

### QO-46 — Quel identifiant technique définitif donner à l'application ?

**Statut : décidée le 2026-11-08 — option A : `io.github.zebul1n.pianohelper` ; compte GitHub Zebul1n.**

**Question.** Quel « nom technique » (invisible pour l'utilisateur, mais définitif) donner à l'application ?

**Ce qu'il faut savoir.** Android ne met à jour une application que si elle garde le même nom technique et la même signature. Vous avez décidé de le fixer avant I0 (QO-35) ; reste à choisir sa valeur. Ce nom sera enregistré auprès de Google en 2027.

| Option | Exemple | Remarque |
|---|---|---|
| A — Adresse de votre compte d'hébergement | `io.github.zebul1n.pianohelper` | Gratuit, vérifiable |
| B — Nom de domaine acheté | `org.pianohelper.app` | Payant |
| C — Nom de domaine que vous ne possédez pas | `org.pianohelper.app` | Risque de conflit futur |

**Recommandation : A** (ARCH, DEV), avec deux précautions (DEV) : un tiret dans le nom du compte devient `_` ; **ne jamais renommer le compte** ensuite. **Décision : A.** Le porteur a déjà un compte GitHub ; son nom, pas encore donné, s'écrit **Zebul1n** dans tous les documents jusqu'à ce qu'il le fournisse (action 2 du §2). Aucune valeur n'est devinée ; aucune version n'est diffusée tant que Zebul1n n'est pas remplacé (US-CNF-04 CA4).
**Impact.** Définitif dès la première version diffusée ; [10 §3](10-build-ci-et-distribution.md#3-variantes-et-signature) ; EDB 11 §4.
**Échéance.** Avant I0.

### QO-47 — Pour quels types de processeurs construire l'application diffusée ?

**Statut : décidée le 2026-11-08 — option A.**

**Question.** Faut-il construire l'application diffusée pour les seuls processeurs 64 bits des téléphones, ou aussi pour les anciens processeurs 32 bits ?

**Ce qu'il faut savoir.** Chaque type de processeur ajoute une copie du code natif dans le fichier d'installation. Les relecteurs ont mesuré qu'un composant envisagé pèse 34 Mo par type de processeur : la limite de 150 Mo (ENF-RES-03) est vite atteinte. Votre téléphone est en 64 bits.

| Option | Couverture | Coût |
|---|---|---|
| A — 64 bits seulement (`arm64-v8a`) ; la version de test interne ajoute le type de l'émulateur (`x86_64`) | Téléphones 64 bits ; exclut de rares téléphones Android 13 d'entrée de gamme en 32 bits | Application légère, un seul jeu de mesures |
| B — A + 32 bits | Ajoute ces téléphones | Taille, et tests impossibles sans un tel téléphone |

**Recommandation : A** (ARCH, CPP, DEV) ; à réexaminer avant la V1 avec les appareils des testeurs. **Décision : A.**
**Impact.** [10 §3](10-build-ci-et-distribution.md#3-variantes-et-signature) (`abiFilters`) et contrôle de l'APK ; EDB ENF-RES-03 ; US-CNF-04 CA2.
**Échéance.** Avant I0 ; réexamen avant la V1.

### QO-48 — Quels outils installer sur votre ordinateur ?

**Statut : décidée le 2026-11-08 — option A ; installation à faire (action 6 du §2).**

**Question.** Acceptez-vous d'installer sur votre ordinateur un environnement Linux intégré à Windows (WSL 2), qui contiendra Python et le compilateur C++ ?

**Ce qu'il faut savoir.** Python n'est pas installé. Il sert aux prototypes de reconnaissance du piano (POC-01) et d'alignement (POC-05). Un compilateur C++ est nécessaire pour mesurer la qualité de l'analyse sur vos enregistrements privés, qui ne peuvent pas être publiés. Pour que ces mesures soient comparables à celles faites automatiquement en ligne, il faut **le même environnement** que les machines de vérification.

| Option | Description |
|---|---|
| A — WSL 2 avec Ubuntu 24.04 | Python, compilateur, outils de licence, tout au même endroit, identique aux machines de vérification ; gratuit ; quelques Go de disque |
| B — Python et compilateur installés directement sous Windows | Résultats moins comparables (autre compilateur) |
| C — Rien | Corpus privé rejoué sur le téléphone seulement ; prototypes impossibles |

**Recommandation : A** (ARCH, DEV, CPP, MIR). À installer aussi : Android Studio, avec l'hyperviseur Windows (WHPX) pour l'émulateur ; liste complète au [10 §7](10-build-ci-et-distribution.md#7-prérequis-du-poste-du-porteur). **Décision : A.**
**Impact.** [ADR-0002](adr/0002-langages-et-normes.md), [ADR-0015](adr/0015-banc-d-evaluation.md), [ADR-0022](adr/0022-reproductibilite-ordinateur-telephone.md) ; EDB 07 §14.2.
**Échéance.** Avant I0 et avant de lancer POC-01 et POC-05.

### QO-49 — Où ranger les enregistrements de piano publics, et sous quelle licence ?

**Statut : décidée le 2026-11-08 — option A, licence CC0.**

**Question.** Où conserver vos enregistrements de piano destinés à être publiés, et sous quelle licence les publier ?

**Ce qu'il faut savoir.** Les enregistrements servent à mesurer la qualité de l'analyse. Ils pèseront des dizaines à des centaines de mégaoctets. Les mettre dans le dépôt du code l'alourdirait à chaque nouvel enregistrement.

| Option | Description |
|---|---|
| A — Dépôt séparé `piano-helper-corpus` : partitions, annotations et MIDI dans le dépôt ; fichiers audio joints à chaque version de ce dépôt, avec leurs empreintes ; licence **CC0** | Dépôt du code léger ; réutilisation libre |
| B — Dans le dépôt du code, avec un stockage spécial (Git LFS) | Archives de sources incomplètes (JUR-03) |
| C — A avec la licence CC BY 4.0 | Votre nom est cité à chaque réutilisation |

**Recommandation : A** (ARCH, DEV pour le rangement ; CC0 recommandé par ARCH ; pour la chaîne technique, CC0 et CC BY sont équivalents, DEV). Le choix de licence est le vôtre (US-QUA-01 CA4). **Décision : A, avec la licence CC0.**
**Impact.** [02 §4](02-vue-d-ensemble.md#4-outils-hors-application), [10 §1](10-build-ci-et-distribution.md#1-structure-du-dépôt) ; EDB 06 §9.1 et 11 §3 ; US-QUA-01 CA4.
**Échéance.** Avant I0.

### QO-51 — Autoriser dès maintenant, dans la licence, la diffusion sur les boutiques d'applications ?

**Statut : décidée le 2026-11-08 — option B ; question à reposer avant d'accepter la première contribution extérieure.**

**Question.** Faut-il ajouter dès maintenant à la licence du projet une permission qui autorise sa diffusion sur l'App Store d'Apple, en vue d'une future version iOS ?

**Ce qu'il faut savoir.** La licence du projet (GPL) est jugée par la Free Software Foundation incompatible avec les règles de l'App Store ; l'application VLC en a été retirée en 2011 à la demande d'un contributeur. Vous pouvez diffuser votre propre code où vous voulez, mais pas celui des autres : **dès qu'une personne extérieure aura contribué**, ajouter cette permission exigera son accord. Une permission supplémentaire (prévue par la GPL, §7) affaiblit en contrepartie la protection du logiciel libre. Elle ne règle pas la question de Verovio (bibliothèque de tiers), pour laquelle il faudra demander une autorisation à ses auteurs au moment du portage.

| Option | Conséquence |
|---|---|
| A — Ajouter la permission maintenant | Portage iOS facilité ; copyleft affaibli |
| B — Ne rien ajouter | Choix acceptable si iOS reste lointain ; il faudra plus tard l'accord de chaque contributeur |

**Recommandation : aucune option recommandée** (JUR n'a pas d'avis tranché ; ARCH non plus). Un avis d'avocat est conseillé avant le portage. **Décision : B.** Aucune permission supplémentaire n'est ajoutée pour l'instant ; la question sera **reposée avant d'accepter la première contribution extérieure** (rappel à faire figurer dans `CONTRIBUTING.md` quand il sera rédigé).
**Impact.** Licence du projet inchangée (GPL-3.0-or-later) ; [11 §4](11-portabilite-ios.md#4-obstacles-connus) ; [09 §7](09-securite-confidentialite-licences.md) ; EDB 11 §3.
**Échéance.** Avant d'accepter toute contribution extérieure.

## 4. Urgence 2 — avant I1

### QO-44 — Comment faire les tests qui demandent plusieurs personnes avant la bêta ?

**Statut : décidée le 2026-11-08 — option A.**

**Question, options et recommandation** : voir [PRD 09 §2.3](../PRD/09-decisions-a-prendre.md). En résumé : vous faites seul les tests de lisibilité (I1) et de premier lancement (I4), et ils sont refaits avec les premiers testeurs de la bêta (option A, recommandée par le PO). Aucun élément technique nouveau du DAT ne changeait cette recommandation. **Décision : A.**
**Impact.** US-VIS-03 CA3, US-CPT-06 CA6, registre des vérifications reportées du PRD 05 §5.
**Échéance.** Avant I1.

## 5. Urgence 3 — au jalon J1, après les preuves de concept

### QO-50 — Exclure des versions diffusées le moteur d'IA tout fait de Microsoft ?

**Statut : décidée le 2026-11-08 — oui (recommandation) ; choix entre C et B au jalon J1.**

**Question.** Si la reconnaissance du piano utilise un modèle d'intelligence artificielle, faut-il interdire, dans les versions diffusées, le moteur d'exécution tout fait fourni par Microsoft (ONNX Runtime) ?

**Ce qu'il faut savoir.** Ce moteur tout fait (1) contient un **module de télémétrie de Microsoft, activé par défaut**, qui crée un identifiant de l'appareil et stocke des événements sur le téléphone, contraire à la règle « aucune télémétrie » ; (2) pèse 34 Mo ; (3) ne fournit pas les mentions de licence de ses composants. Rien ne partirait sur Internet, puisque l'application n'y a pas accès, mais la promesse faite aux utilisateurs ne serait plus tenue. Les alternatives : écrire nous-mêmes le calcul du modèle (possible pour un petit modèle), ou construire nous-mêmes une version sans télémétrie. POC-01 dira aussi si un modèle est utile : un vérificateur sans IA pourrait suffire.

| Option | Description |
|---|---|
| A — Moteur tout fait dans les versions diffusées, télémétrie désactivée par un réglage | Simple ; promesse fragile ; 34 Mo ; pas de F-Droid |
| B — Moteur construit par le projet, sans télémétrie | Plus léger ; Python dans la chaîne de construction, construction longue |
| C — Calcul du modèle écrit dans le projet | Aucun composant tiers, aucun poids ajouté ; effort de développement |

**Recommandation : exclure le moteur tout fait des versions diffusées (pas A)** ; **C** si POC-01 retient un petit modèle et que l'effort est raisonnable, sinon **B** ; le moteur tout fait reste permis pour les essais internes. Soutenue par JUR (bloquant), CPP, DEV, ARCH ; MIR doute du modèle lui-même (qualité de 70,9 % sur le piano) et propose d'abord un vérificateur sans IA. **Décision : la recommandation.** Si POC-01 ne retient aucun modèle, aucun moteur n'entre dans l'application.
**Impact.** [ADR-0011](adr/0011-moteur-d-inference-onnx-runtime.md) (reste « Proposée — à confirmer par POC-01 » pour le choix entre C, B et aucun moteur), [09 §1](09-securite-confidentialite-licences.md#1-permissions-et-absence-de-réseau), taille de l'application, diffusion sur F-Droid ; US-CNF-01 CA4 ; EDB 07 §7.3. Cette question absorbe la question n° 1 de la revue juridique.
**Échéance.** Jalon J1, au vu du rapport de POC-01.

### QO-52 — En mode attente, le compte-rendu peut-il ajouter des erreurs qui n'ont pas été montrées pendant le jeu ?

**Statut : décidée le 2026-11-08 — option A.**

**Question.** En mode attente, quand l'analyse détaillée faite à la fin de la séance n'est pas d'accord avec ce qui a été montré pendant le jeu, laquelle fait foi ?

**Ce qu'il faut savoir.** En mode attente, la partition avance quand la bonne note est entendue (« trouvée du premier coup »). À la fin, une analyse plus lente revoit toute la séance. Si elle « fait foi », elle pourrait déclarer fausse une note que l'élève a vue validée : c'est déroutant, et contraire au principe « une fausse erreur coûte plus cher qu'une erreur non signalée ». Faire tourner cette analyse pendant de longues attentes coûte aussi de la batterie.

| Option | Description |
|---|---|
| A — Le compte-rendu reprend les décisions prises pendant le jeu ; l'analyse détaillée peut seulement rendre une décision « incertaine » (hors score), jamais créer une erreur ; elle est facultative dans ce mode | Cohérent avec ce que l'élève a vu |
| B — L'analyse détaillée fait foi, comme en jeu continu | Plus précis, mais contradictions possibles |

**Recommandation : A** (MIR, ARCH). Le jeu continu n'est pas concerné : l'analyse détaillée y fait foi (EF-ANA-13). **Décision : A.**
**Impact.** EF-ANA-13 (nouveau CA2, MDAT-19) et EDB 06 §8 ; US-ANA-09 CA2 et CA3 (MDAT-29) ; [ADR-0010](adr/0010-transcription-deux-etages.md) ; [03 §3.6](03-noyau-natif.md) ; [05 §1](05-flux-temps-reel-et-performance.md).
**Échéance.** Jalon J1 (avant I3, première utilisation du micro).

### QO-55 — Si le micro est trop lent dans le grave seulement, accepter une cible propre au grave ?

**Statut : décidée le 2026-11-08 — option A, appliquée seulement si la mesure l'exige.**

**Question.** Si les mesures montrent que l'application réagit assez vite (150 ms) au micro dans le médium et l'aigu, mais pas dans le grave, faut-il accepter une cible plus longue pour le grave seulement ?

**Ce qu'il faut savoir.** Reconnaître une note grave au micro prend physiquement plus de temps (ses vibrations sont lentes, et le micro d'un téléphone capte mal le grave). L'expert estime 40 à 60 ms de décision dans le médium, 60 à 100 ms sous le do 2. Cette question ne se pose que si la mesure de POC-01 le montre.

| Option | Description |
|---|---|
| A — Cible propre au grave (par exemple 200 ms sous le do 2), 150 ms ailleurs | L'application garde sa réactivité dans la plus grande partie du clavier |
| B — Relâcher la cible pour tout le clavier | Plus simple, mais moins bien partout |
| C — Garder 150 ms partout et ne pas utiliser le micro dans le grave | Fonction réduite |

**Recommandation : A** (MIR, ARCH), avec une valeur fixée au vu des mesures. **Décision : A.** La valeur et la limite du registre grave seront fixées au jalon J1, au vu du rapport de POC-01, et inscrites dans ENF-PERF-01.
**Impact.** ENF-PERF-01 (EDB v0.7, MDAT-07) ; US-ECO-07 CA1, US-QUA-04 CA1 ; [05 §3](05-flux-temps-reel-et-performance.md#3-budgets-de-latence-par-étape) ; [12](12-specifications-techniques-des-poc.md).
**Échéance.** Jalon J1, seulement si le cas se présente.

## 6. Urgence 4 — plus tard

### QO-53 — Demander à l'éditeur de la police musicale le droit de l'alléger ?

**Statut : décidée le 2026-11-08 — option A.**

**Question.** Faut-il écrire au centre RISM, auteur de Verovio et des polices musicales Leipzig et Bravura, pour obtenir l'autorisation de réduire ces polices en gardant leur nom ?

**Ce qu'il faut savoir.** La licence de ces polices réserve leur nom : une version modifiée (par exemple allégée) ne peut pas le garder sans autorisation. Au MVP, les polices sont embarquées telles quelles, ce qui est permis. Les réduire ferait gagner un peu de place (moins d'1 Mo pour les deux). La demande est simple et gratuite (info@rism.digital).

| Option | Description |
|---|---|
| A — Écrire seulement si la taille de l'application l'exige | Pas de démarche inutile |
| B — Écrire tout de suite | Autorisation acquise d'avance |
| C — Ne jamais réduire les polices | Aucune démarche |

**Recommandation : A** (JUR, ARCH). La même lettre pourra, au moment du portage iOS, demander la position de RISM sur l'App Store (QO-51). **Décision : A.**
**Impact.** [07 §6](07-import-et-rendu-de-partition.md#6-rendu-et-mise-en-cache), [ADR-0006](adr/0006-moteur-de-rendu-verovio.md), [09 §7](09-securite-confidentialite-licences.md) ; EDB 11 §3 ; US-CNF-03 CA4.
**Échéance.** Après POC-04 (mesure de la taille).

## 7. Question encore ouverte

### QO-54 — Comment diffuser la V1 hors de Google Play après 2027 ?

**Statut : ouverte** (urgence « plus tard », liée à QO-20).

**Question.** Pour la V1 publique, comment permettre aux utilisateurs d'installer l'application hors de Google Play, alors que Google exigera en 2027 que les applications soient enregistrées, et que le compte de diffusion complète coûte 25 USD ?

**Ce qu'il faut savoir.** Le compte gratuit de « distribution limitée » suffit pour la bêta, mais seulement pour **20 appareils**. Pour une diffusion large hors Google Play, Google demande un compte complet à 25 USD, payé une fois, ce qui heurte le budget nul. Sans enregistrement, l'utilisateur doit passer par une procédure « avancée » (mode développeur, redémarrage, attente de 24 h).

| Option | Description |
|---|---|
| A — Payer 25 USD une fois (compte complet) | Installation simple pour tous ; entorse au budget nul |
| B — F-Droid, avec une construction reproductible qui garde votre clé, et procédure avancée pour les autres canaux | Gratuit ; suppose de construire nous-mêmes tous les composants (voir QO-50) |
| C — Procédure avancée seule | Gratuit ; forte friction pour les utilisateurs |

**Recommandation** : à trancher avec le choix du canal public (QO-20) ; ARCH penche pour B si QO-50 aboutit à C, sinon A ; DEV a posé la question sans trancher. La décision QO-50 du 2026-11-08 (moteur tout fait exclu) laisse la voie B ouverte.
**Échéance.** Avec QO-20, avant la V1.

## 8. Ce qui ne revient pas au porteur

Les autres points des revues sont des décisions d'architecture ou de mise en œuvre, intégrées au DAT (« Suite donnée » de chaque [revue](revues/)), ou des corrections du PRD qui reviennent au PO ([fichier 15](15-modifications-edb-prd-proposees.md)). Un choix reste au PO, avec l'avis de `designer-ux`, à l'entrée de US-IMP-03 : déclarer ou non le type `application/octet-stream` (MDAT-34).
