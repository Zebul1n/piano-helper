# 09 — Décisions du porteur du projet

Ce fichier rassemble **toutes** les décisions qui reviennent au porteur du projet, après les relectures croisées du PRD v0.1 ([revues](revues/)). Il remplace, pour la prise de décision, la liste du [fichier 08](08-questions-ouvertes.md), qui garde l'historique et les arbitrages du Product Owner.

**Le porteur a tranché les 23 décisions le 2026-11-07.** Ses réponses :
1. il valide **en bloc les recommandations** : pour chaque question, l'option recommandée devient la décision ;
2. **QO-34** (identité affichée) : il choisit l'**option C**, nom civil et adresse e-mail personnelle ; valeurs fournies le 2026-11-08 : **Ronan Launay**, **rlaunay@free.fr** ;
3. **QO-31** (morceaux de démonstration) : la saisie est faite **par un agent**, relue note par note par `pedagogue-piano`, puis validée **à l'écoute** par le porteur ; la licence CC0 reste la décision ;
4. **QO-27** (affichage) : le porteur ne peut pas réunir 3 ou 4 lecteurs ; il fait **le test seul**. La décision issue de ce test est **provisoire** et sera reconfirmée en bêta avec des testeurs (registre de [05 §5](05-plan-de-release.md)). Position retenue en attendant le test : **bande horizontale en paysage**.

Les décisions sont appliquées dans l'[EDB v0.6](../EDB/README.md) (résumé en [EDB 12 §0](../EDB/12-risques-hypotheses-questions.md)) et dans les stories du PRD v0.3. Chaque décision ci-dessous se lit seule : question, options, recommandation (et qui la soutenait), décision, impact, échéance.

**Le 2026-11-08, le porteur a tranché QO-44** (§2.3, option A) **et les questions du DAT QO-45 à QO-53 et QO-55** (validation en bloc des recommandations du [DAT 14](../DAT/14-decisions-a-prendre.md), avec QO-46 au format `io.github.zebul1n.pianohelper`, compte GitHub Zebul1n et noté Zebul1n ; QO-51 : option B ; QO-49 : option A, licence CC0). Elles sont appliquées dans l'EDB v0.7 et le PRD v0.4 (récapitulatif au §1). **QO-54 reste ouverte.**

Abréviations des relecteurs : **PO** (`product-owner`), **PED** (`pedagogue-piano`), **UX** (`designer-ux`), **QA** (`qa-testeur`), **JUR** (`juriste-conformite`).

Rappel des incréments du MVP (voir [05](05-plan-de-release.md)) : **I0** outillage et premières versions de test, **I1** voir et entendre sa partition, **I2** moteur d'analyse avec un piano simulé, **I3** piano droit au micro en mode attente, **I4** carnet de travail, **I5** jeu continu au micro, **I6** stabilisation avant la bêta. **J1** est le go / no-go technique après les preuves de concept (POC).

## 1. Récapitulatif des décisions

| QO | Sujet | Décision du 2026-11-07 | Appliquée |
|---|---|---|---|
| QO-34 | Identité affichée | **C** : nom civil et adresse e-mail personnelle (Ronan Launay, rlaunay@free.fr) | I0 |
| QO-35 | Nom technique et clé de signature | **A** : nom définitif et clé de diffusion pérenne, hors du dépôt, sauvegardée hors ligne | I0 |
| QO-36 | Sauvegarde automatique d'Android | **A** : désactivée ; rappel d'exporter ses partitions | I0 |
| QO-37 | Jeux de données « non commerciaux » | **A** : oui, à conditions strictes, jamais dans le contrôle bloquant | I0 |
| QO-10 | Son de piano embarqué | **A** : banque légère libre embarquée, meilleure banque téléchargeable plus tard | I1 |
| QO-27 | Affichage de la partition | **Test au pupitre fait par le porteur seul** ; en attendant : **bande horizontale en paysage** ; décision provisoire, reconfirmée en bêta | I1 |
| QO-30 | I1 avant la fin des POC | **A** : I1 démarre dès que POC-04 est concluant | I1 |
| QO-31 | Morceaux de démonstration | **C, avec A en priorité** : gravure maison sous **CC0**, complétée si besoin par Mutopia ; saisie par un agent, relue par PED, validée à l'écoute par le porteur | I1 |
| QO-38 | Licence de la documentation | **A** : CC BY-SA 4.0 | I1 |
| QO-23 | Protections nécessaires au micro | **A** : les trois au MVP (diapason, signal, sons de l'application), formes minimales | I3 |
| QO-24 | Sept arbitrages de périmètre | **Positions recommandées a à g** (passages, autre main, niveaux et reprises, pas d'étiquette, thème clair, français et anglais, style sobre impersonnel) | I2 à I6 |
| QO-25 | Arrêt, pause et fin de séance | **Recommandation** : pause à 7 / 6 / 5 s, reprise en jouant, fin après 30 s de pause, 2 min en mode attente | I2, I3 |
| QO-39 | Calcul du score | **B** : formule multiplicative, avec les deux précisions | I2 |
| QO-40 | Points d'orgue et ralentis écrits | **A** : rythme non évalué à ces endroits | I2 |
| QO-29 | Présentation du jeu continu au micro | **A** : décision au vu du rapport de POC-01, mention « expérimental », revérifié en bêta | J1 |
| QO-32 | Précision du rythme au micro | **Recommandation** : jamais sous 2σ ; « exigeant » au micro si 2σ ≤ 50 ms ; marque à 1,5 × la tolérance | J1, I3, I5 |
| QO-41 | Modèle entraîné sur des données NC | **A** : accepté au MVP, risque documenté, avis d'avocat avant une V1 payante | Après POC-01 |
| QO-42 | Micro en échec même en mode attente | **A si les morceaux à une main atteignent les objectifs, sinon C** ; I4 démarre après I2 | J1 (si le cas se produit) |
| QO-11 | Détail des séances | **Tout garder au MVP**, mesurer en bêta, décider avant la V1 | I4, bêta |
| QO-20 | Canal de diffusion | **A** pour l'alpha et la bêta (APK sur le dépôt, distribution limitée de Google) ; canal public avant la V1 | Bêta, V1 |
| QO-26 | Boucle de pratique | **Oui** : 3 répétitions par défaut, décompte et score à chaque fois | I5 |
| QO-28 | Retours des testeurs sans serveur | **A** : signalement dans l'application, rapport de test anonymisé, questionnaire | I6 |
| QO-43 | Délai du compte-rendu au micro | **C** : mesurer en I5, puis choisir entre 2 s et 10 s avec progression | I5 |

Close avant ces décisions : QO-33 (résolue par l'EDB v0.5). Les questions QO-12 et QO-16 de l'EDB sont tranchées par QO-24 g et f.

### Décisions du 2026-11-08

Le porteur a validé **en bloc les recommandations** de QO-44 (§2.3) et du [DAT 14](../DAT/14-decisions-a-prendre.md), qui détaille QO-45 à QO-55 (question, options, avis des relecteurs, impact). Trois précisions : QO-46, compte GitHub Zebul1n (fourni le 2026-11-08) ; QO-51, option B ; QO-49, option A avec la licence CC0.

| QO | Sujet | Décision du 2026-11-08 | Appliquée |
|---|---|---|---|
| QO-44 | Tests qui demandent plusieurs personnes avant la bêta | **A** : tests de lisibilité (I1) et de premier lancement (I4) faits par le porteur seul, refaits avec les premiers testeurs de la bêta ; J2 garde ses 3 testeurs | I1, I4, J2 |
| QO-45 | Hébergement du code et des vérifications | **A** : GitHub, avec machines figées (Ubuntu 24.04), clé de signature protégée, copie de secours | I0 |
| QO-46 | Identifiant technique définitif | **A** : `io.github.zebul1n.pianohelper` (compte GitHub Zebul1n) | I0 |
| QO-47 | Processeurs des versions diffusées | **A** : 64 bits (`arm64-v8a`) ; `x86_64` dans la variante interne ; à réexaminer avant la V1 | I0 |
| QO-48 | Outils du poste du porteur | **A** : WSL 2 avec Ubuntu 24.04, et Android Studio | I0, POC |
| QO-49 | Corpus de piano public | **A, CC0** : dépôt séparé `piano-helper-corpus`, fichiers audio joints à ses versions | I0 |
| QO-50 | Moteur d'IA tout fait de Microsoft | **Exclu des versions diffusées** : calcul écrit dans le projet si possible, sinon moteur construit par le projet ; tout fait permis en essai interne | J1 |
| QO-51 | Permission de diffusion sur les boutiques d'applications | **B** : rien n'est ajouté ; question reposée avant la première contribution extérieure | Avant toute contribution extérieure |
| QO-52 | Compte-rendu du mode attente | **A** : reprend les décisions prises pendant le jeu ; le différé ne crée jamais d'erreur | J1, I2, I3 |
| QO-53 | Allègement des polices musicales | **A** : demander à RISM seulement si la taille l'exige | Après POC-04 |
| QO-55 | Cible de latence propre au grave | **A** : cible propre au grave si seul le grave dépasse, valeur fixée au vu des mesures | J1, si la mesure l'exige |

Reste ouverte : **QO-54** (diffusion de la V1 hors de Google Play, compte complet à 25 USD), à trancher avec le canal public de QO-20 avant la V1 ([DAT 14](../DAT/14-decisions-a-prendre.md)).

## 2. Ce qui reste à faire

### 2.1 Actions qui restent au porteur

| # | Action | Échéance | Pour |
|---|---|---|---|
| 1 | **Fournir le nom civil et l'adresse e-mail personnelle** à afficher (QO-34), ✅ fournis le 2026-11-08 : Ronan Launay, rlaunay@free.fr | Avant I0 | US-CNF-02, US-CNF-05, US-CNF-06 |
| 2 | **Donner le nom du compte GitHub** (QO-46) ✅ fourni le 2026-11-08 : Zebul1n → `io.github.zebul1n.pianohelper` | Avant I0 | US-CNF-04 |
| 3 | **Relever sur le téléphone les libellés Android** de la demande d'autorisation du micro (titre et options) | Avant I3 | US-ECO-03 §8.1 |
| 4 | **Faire seul le test d'affichage au pupitre** selon le protocole du §4 (QO-27) et noter le résultat | Avant I1 | US-VIS-02, US-VIS-05 |
| 5 | **Sauvegarder la clé de signature hors ligne** dès qu'elle sera créée (QO-35) | I0, à la création de la clé | US-CNF-04 |
| 6 | **Installer WSL 2 (Ubuntu 24.04) et Android Studio** selon la liste du [DAT 10 §7](../DAT/10-build-ci-et-distribution.md#7-prérequis-du-poste-du-porteur) (QO-48) | Avant I0, et avant POC-01 et POC-05 | US-QUA-02 CA4, POC-01, POC-05 |

Cette liste est la même que celle de l'[EDB 12 §0](../EDB/12-risques-hypotheses-questions.md#0-décisions-prises) et du [DAT 14](../DAT/14-decisions-a-prendre.md). Question encore ouverte : **QO-54** (diffusion de la V1 hors de Google Play), à trancher avec le canal public de QO-20 avant la V1.

### 2.2 Décisions qui s'appliquent plus tard

Certaines décisions fixent une règle qui ne joue qu'au vu d'une mesure : QO-29 et QO-42 au jalon J1 (rapport de POC-01) ; QO-32 avec l'incertitude mesurée par POC-01 ; QO-41 au choix du modèle ; QO-43 après la mesure en I5 ; QO-11 et le canal public de QO-20 avant la V1. Décisions du 2026-11-08 : QO-50 au jalon J1 (choix entre le calcul écrit dans le projet et le moteur construit par le projet) ; QO-55 à J1, seulement si la mesure l'exige ; QO-53 après POC-04 ; QO-47 réexaminée avant la V1 ; QO-51 reposée avant d'accepter la première contribution extérieure. Elles n'appellent pas de nouvelle décision, sauf si la mesure sort des cas prévus.

### 2.3 QO-44 — Tests qui demandent plusieurs personnes avant la bêta

**Statut : décidée le 2026-11-08 — option A.**

**Question.** Le porteur ne peut pas réunir 3 ou 4 lecteurs pour le test d'affichage (QO-27). D'autres critères du MVP demandent aussi plusieurs personnes avant la bêta. Comment les vérifier ?

| Critère | Ce qu'il demande |
|---|---|
| US-VIS-03 CA3 (I1) | Protocole de lisibilité à 60 cm avec au moins 4 lecteurs, dont une personne de plus de 45 ans |
| US-CPT-06 CA6 (I4) | 3 personnes qui ne connaissent pas l'application atteignent le premier compte-rendu en 5 minutes, sans aide |
| J2, critère 2 ([05 §3](05-plan-de-release.md)) | 3 testeurs qui ne connaissent pas l'application réalisent seuls le scénario de recette |

| Option | Description |
|---|---|
| A | Comme pour QO-27 : le porteur fait seul les tests d'I1 et d'I4 ; ils sont refaits avec les premiers testeurs bénévoles de la bêta et inscrits au registre des vérifications reportées. J2 garde ses 3 testeurs, recrutés pour la bêta fermée |
| B | Le porteur cherche malgré tout 3 personnes pour I4 et J2 ; seul US-VIS-03 CA3 est fait par le porteur seul |
| C | Garder tous les critères tels quels |

**Recommandation : A** (PO). J2 marque l'ouverture aux testeurs : ses 3 testeurs sont les premiers testeurs bénévoles, pas l'entourage du porteur. **Décision : A** (validation en bloc du 2026-11-08), appliquée dans US-VIS-03 CA3, US-CPT-06 CA6 et le registre de [05 §5](05-plan-de-release.md). **Échéance.** Avant I1.

---

## 3. Urgence 1 — avant la première version de test diffusée (I0)

### QO-34 — Quelle identité afficher dans l'application ?

**Statut : décidée le 2026-11-07 — option C** (nom civil et adresse e-mail personnelle).

**Question.** Quel nom, quelle adresse de contact et quelle mention de droits d'auteur faut-il afficher dans l'écran « À propos » et la notice de confidentialité ?

**Pourquoi maintenant.** La licence GPL demande d'afficher une mention de droits d'auteur dès la première version diffusée ; la notice de confidentialité doit indiquer qui contacter.

| Option | Description |
|---|---|
| A | Nom civil du porteur et adresse e-mail dédiée au projet |
| B | Pseudonyme stable et adresse e-mail dédiée au projet |
| C | Nom civil et adresse e-mail personnelle |

**Recommandation des relecteurs : A ou B, avec une adresse dédiée au projet** (JUR). **Décision : C**, choisie par le porteur. Valeurs fournies par le porteur le 2026-11-08 : **Ronan Launay**, **rlaunay@free.fr**.
**Impact.** Texte « © 2026 Ronan Launay et les contributeurs » dans chaque version (US-CNF-02) ; rlaunay@free.fr utilisée pour les rapports de test (US-CNF-05) et les demandes relatives aux données (US-CNF-06). JUR signalait qu'un store pourra exiger une identité vérifiée plus tard ; avec l'option C, l'adresse personnelle est publiée dans le dépôt et dans l'application.
**Échéance.** Avant I0.

### QO-35 — Nom technique de l'application et clé de signature

**Statut : décidée le 2026-11-07 — option A.**

**Question.** Faut-il fixer dès maintenant le nom technique définitif de l'application (exemple : `org.pianohelper.app`) et créer la clé de signature qui servira à toutes les versions ?

**Pourquoi.** Android n'accepte une mise à jour que si elle est signée avec la même clé et porte le même nom technique. Google annonce en outre, pour 2027 en Europe, l'obligation d'enregistrer ce nom auprès d'un développeur vérifié. Changer plus tard obligerait les testeurs à désinstaller l'application et à perdre leurs données.

| Option | Description |
|---|---|
| A | Fixer maintenant un nom définitif et une clé de diffusion, gardée secrète (hors du dépôt) et sauvegardée hors ligne |
| B | Utiliser un nom et une clé provisoires jusqu'à la V1 |

**Recommandation : A** (JUR, PO). **Décision : A.** **Impact.** Une action de 30 minutes (`devops-release`) ; la perte de la clé empêcherait toute mise à jour : le porteur la sauvegarde hors ligne dès sa création (action 4 du §2.1). US-CNF-04 CA4.
**Échéance.** Avant I0.

### QO-36 — Sauvegarde automatique d'Android

**Statut : décidée le 2026-11-07 — option A.**

**Question.** Faut-il empêcher Android de copier les données de l'application dans le Google Drive de l'utilisateur ?

**Pourquoi.** Par défaut, Android sauvegarde les données des applications dans le compte Google. La promesse « aucune donnée ne quitte le téléphone » serait alors fausse, et « supprimer toutes mes données » pourrait être annulé par une restauration.

| Option | Conséquence |
|---|---|
| A — Désactiver | Promesse simple et vraie ; en changeant de téléphone, l'utilisateur perd ses données sauf s'il a exporté ses partitions |
| B — Garder | Données retrouvées sur un nouveau téléphone ; il faut l'expliquer dans la notice de confidentialité |

**Recommandation : A** (JUR, QA, PO), avec un rappel d'exporter ses partitions avant de changer de téléphone. Une sauvegarde maîtrisée pourra être proposée en V1. **Décision : A.**
**Impact.** US-CNF-01 et US-CPT-05 vérifient l'absence de sauvegarde ; EDB ENF-CONF-01.
**Échéance.** Avant I0.

### QO-37 — Jeux de données « non commerciaux » pour mesurer la qualité

**Statut : décidée le 2026-11-07 — option A.**

**Question.** Peut-on utiliser des enregistrements publics de piano sous licence « non commerciale » (exemple : MAESTRO) pour mesurer la qualité de l'écoute au micro ?

| Option | Description |
|---|---|
| A | Oui, à des conditions strictes : jamais copiés dans le dépôt ni dans l'application ; téléchargés par un script ; jamais utilisés pour entraîner un modèle ; ne servent qu'à des mesures complémentaires, pas au contrôle qui bloque une livraison |
| B | Non : uniquement des données libres et les enregistrements du porteur |

**Recommandation : A** (JUR, QA, PED). Ces données apportent d'autres pianos que celui du porteur. PED précise qu'elles ne servent pas à mesurer les fausses erreurs de rythme (ce sont des interprétations libres). **Décision : A.**
**Impact.** Le contrôle bloquant repose sur les données libres et les enregistrements du porteur (US-QUA-01 CA4, US-QUA-02 CA4 ; EDB 06 §9.1). À réexaminer si une version payante est décidée.
**Échéance.** Avant I0 (constitution du corpus).

---

## 4. Urgence 2 — avant I1

### QO-10 — Quel son de piano embarquer ?

**Statut : décidée le 2026-11-07 — option A.**

**Question.** Quelle banque de sons de piano intégrer dans l'application ?

| Option | Description |
|---|---|
| A | Une banque légère embarquée, libre de droits (exemple vérifié : « Upright Piano KW », licence CC0, 5,8 Mo) ; une banque de meilleure qualité téléchargeable plus tard (exemple : « Salamander Grand Piano », CC BY, 296 Mo) |
| B | Directement une banque de haute qualité embarquée (application plus lourde) |

**Recommandation : A** (JUR, PO). **Décision : A.** **Impact.** Application légère ; qualité du son validée à l'oreille par le porteur (US-LEC-01 CA5).
**Échéance.** Avant I1.

### QO-27 — Comment afficher la partition sur le téléphone ?

**Statut : décidée le 2026-11-07 — test au pupitre fait par le porteur seul ; en attendant, bande horizontale en paysage ; décision provisoire, reconfirmée en bêta.**

**Question.** Au MVP, la partition s'affiche-t-elle par pages (comme un livre, avec tourne de page) ou en une bande horizontale qui défile ?

**Pourquoi.** Sur un téléphone posé sur le pupitre, pour lire à 60 cm, la portée doit mesurer au moins 7,6 mm. En paysage, une seule ligne de musique tient alors à l'écran : en mode page, on ne voit pas la ligne suivante avant d'arriver au bout.

| Option | Pour | Contre |
|---|---|---|
| A — Bande horizontale, en paysage, qui avance par paliers | Toujours au moins une mesure visible d'avance ; pas de tourne ; reprises déroulées dans l'ordre de jeu | Pas de vue d'ensemble ; portrait peu pratique |
| B — Mode page, avec tourne en deux temps | Habitude du papier et des pupitres numériques (PED) | En paysage, ne montre pas la suite ; en portrait, une mesure par ligne |

**Recommandation : décider par un test au pupitre** (UX, accepté par PED et PO), avant I1, avec 4 lecteurs. **Pronostic** : A en paysage (UX).

**Décision.** Le porteur ne peut pas réunir 3 ou 4 lecteurs : **il fait le test seul**, selon le protocole ci-dessous. **Position retenue en attendant le test : A (bande horizontale en paysage).** Le résultat du test fixe le mode du MVP, mais cette décision reste **provisoire** : elle est reconfirmée en bêta avec des testeurs, et inscrite au registre des vérifications reportées ([05 §5](05-plan-de-release.md)).

**Protocole du test au pupitre, adapté à un seul lecteur** (adaptation du test de la [revue UX §3.1](revues/designer-ux.md)) :

| Élément | Contenu |
|---|---|
| Prototype | Page HTML locale en plein écran dans le navigateur du téléphone ; partition rendue en SVG par la version JavaScript du moteur évalué dans POC-04 ; curseur animé au tempo par un minuteur. Trois variantes : (A) page en portrait, tourne en deux temps ; (B) page en paysage à la taille cible ; (C) bande horizontale en paysage par paliers. |
| Matériel musical | Pour **chaque variante, des extraits différents**, que le porteur n'a jamais joués : un à une main de niveau débutant ; un à deux mains de niveau débutant avec altérations, doigtés et deux lignes supplémentaires ; un à deux mains de niveau intermédiaire avec une reprise. Extraits choisis par PED parmi les morceaux de démonstration pressentis et le corpus. |
| Participant | Le porteur seul, avec sa correction visuelle habituelle. |
| Installation | Téléphone sur le pupitre du piano droit, distance des yeux à l'écran mesurée au mètre ruban (60 ± 5 cm), éclairage habituel du soir, luminosité automatique. |
| Séances | **Deux séances**, à deux jours différents, avec l'ordre des variantes inversé (A-B-C puis C-B-A), pour limiter l'effet d'apprentissage et de fatigue. |
| Tâches | 1. Lire à voix haute les notes et les doigtés de 8 mesures. 2. Jouer en suivant un curseur qui s'arrête à chaque note (simulation du mode attente, avancé à la main ou au minuteur). 3. Jouer en suivant le curseur à 60 %, puis à 80 %. |
| Mesures | Objectives : hauteur de portée mesurée à la règle sur l'écran ; nombre de temps et de mesures visibles après le curseur au pire moment (PED : relever si **deux mesures** restent visibles). Observées par le porteur lui-même, notées tout de suite : erreurs de lecture dues à la taille (une erreur qui disparaît quand on relit à 30 cm), « j'ai perdu ma place », arrêts dus à l'affichage, confort de 1 à 5, orientation choisie spontanément. Enregistrement vidéo du jeu, si possible, pour recompter les pertes de place. |
| Règle de décision (fixée avant le test) | Retenir la variante qui (a) ne produit **aucune erreur de lecture due à la taille** sur les deux séances, (b) montre toujours **au moins une mesure entière** après le curseur (mesure objective), (c) produit **le moins de pertes de place** sur les deux séances. En cas d'égalité, la variante préférée. Si C l'emporte en paysage et A en portrait, choisir selon l'orientation spontanément adoptée au pupitre. |
| Limites connues | Un seul lecteur, qui connaît l'application et qui n'est ni un débutant ni forcément presbyte : le test ne dit rien de ces profils. D'où la reconfirmation en bêta. |
| Preuve | Fiche de test, photos du montage, mesures, version du prototype ; archivée (D1) et citée dans la décision. |
| Reconfirmation en bêta | Même tâches avec au moins 3 testeurs, dont un débutant et une personne de plus de 45 ans, sur le mode retenu (et, si le mode page de la V1 est prêt, sur les deux) ; voir [05 §5](05-plan-de-release.md). Si le mode retenu échoue, le porteur réexamine QO-27. |

**Impact.** Fixe la conception des écrans de lecture et de jeu (US-VIS-02, US-VIS-05) ; l'autre mode arrive en V1 (US-CNT-03). EDB EF-VIS-02 CA2.
**Échéance.** Avant I1.

### QO-30 — Commencer « voir et entendre » avant la fin des POC ?

**Statut : décidée le 2026-11-07 — option A.**

**Question.** Peut-on développer l'incrément I1 (importer, afficher, écouter une partition) dès que la preuve de concept du rendu (POC-04) est concluante, sans attendre celles du micro et de l'analyse ?

| Option | Description |
|---|---|
| A | Oui : I1 ne dépend pas de l'analyse ; l'application devient vite utile comme pupitre et lecteur |
| B | Non : attendre le go / no-go J1 comme prévu par l'EDB |

**Recommandation : A** (PO ; aucun relecteur contre). **Décision : A.** **Impact.** Retours plus précoces ; aucun risque technique supplémentaire ([05](05-plan-de-release.md), EDB 13 §4).
**Échéance.** Avant I1.

### QO-31 — D'où viennent les morceaux de démonstration, et qui les saisit ?

**Statut : décidée le 2026-11-07 — option C, avec A en priorité ; licence CC0 ; saisie par un agent, relue par PED, validée à l'écoute par le porteur.**

**Question.** Les 5 à 10 morceaux intégrés à l'application sont-ils saisis par le projet lui-même (« gravure maison ») ou repris de fichiers existants ? Qui fait la saisie, et sous quelle licence publier les fichiers ?

**Règle déjà appliquée** (arbitrage du PO, voir [08 §3](08-questions-ouvertes.md)) : tout morceau intégré ou publié dans le dépôt est d'un compositeur **mort avant 1900** (ou traditionnel), saisi d'après une édition ancienne, sans arrangement moderne. Cela écarte la *Gymnopédie n°1* de Satie (mort en 1925), remplacée par le Prélude op. 28 n°4 de Chopin.

| Option | Description |
|---|---|
| A — Gravure maison | Saisie dans MuseScore (logiciel libre) d'après une partition ancienne numérisée ; relue note par note par PED ; publiée sous licence **CC0** (aucune condition) |
| B — Fichiers existants | Fichiers du projet Mutopia (licence CC BY ou CC BY-SA, attribution obligatoire), à convertir |
| C — Mixte | A en priorité, B pour compléter |

**Recommandation : C, avec A en priorité** (JUR, PED, PO). **Décision : C, avec A en priorité, licence CC0** pour la gravure maison. **Qui saisit** : **un agent**, avec relecture note par note par PED, puis validation **à l'écoute** par le porteur. Environ 200 mesures au total.
**Impact.** Fichiers exacts, adaptés à la pédagogie et sans risque juridique. Liste et critères dans US-BIB-06 ; EDB EF-BIB-05.
**Échéance.** Avant I1.

### QO-38 — Sous quelle licence publier la documentation ?

**Statut : décidée le 2026-11-07 — option A.**

**Question.** Sous quelle licence publier les documents du projet (EDB, PRD, guides) ?

| Option | Description |
|---|---|
| A | CC BY-SA 4.0 : réutilisation libre avec attribution ; compatible avec la licence du code |
| B | GPL-3.0, comme le code |
| C | CC BY 4.0 (sans obligation de partage à l'identique) |

**Recommandation : A** (JUR). **Décision : A.** **Impact.** Une ligne dans le README du dépôt (EDB 11 §3).
**Échéance.** Avant I1 (le dépôt est public).

---

## 5. Urgence 3 — avant I2

### QO-23 — Avancer au MVP trois protections nécessaires au micro

**Statut : décidée le 2026-11-07 — option A.**

**Question.** Faut-il intégrer dès le MVP trois fonctions prévues pour la V1, parce que le micro les rend nécessaires ?

| Fonction | Sans elle |
|---|---|
| Tolérance d'un piano un peu désaccordé (EF-ECO-10, story US-ECO-09) | Un piano accordé à 435 Hz ou « étiré » dans les aigus produit de fausses erreurs |
| Indicateur de mauvais signal (EF-ECO-07, US-ECO-10) | L'utilisateur reçoit de mauvais retours sans savoir pourquoi |
| Pas de sons de l'application pris pour du jeu (EF-ECO-08, US-ECO-11) : métronome et décompte visuels au micro, son d'erreur coupé, autre main jouée seulement avec écouteurs filaires | Le micro capte le métronome comme des notes jouées |

Options : **A** les trois (formes minimales) ; **B** seulement le diapason et les sons de l'application ; **C** aucune.

**Recommandation : A** (PO, PED, UX). **Décision : A.** La détection d'arrêt (EF-PRA-08), qui faisait partie de la question, était déjà entrée dans le MVP avec l'EDB v0.5.
**Impact.** US-ECO-09, US-ECO-10 et US-ECO-11 deviennent fermes, en I3.
**Échéance.** Avant I2 (conception), réalisation en I3.

### QO-24 — Sept petits arbitrages de périmètre

**Statut : décidée le 2026-11-07 — positions recommandées a à g.**

**Question.** L'EDB rendait obligatoires au MVP des éléments qui dépendent de fonctions prévues plus tard. Pour chacun, le PRD avait pris une position provisoire ; il fallait la confirmer.

| Point | Question simple | Décision (position recommandée) | Soutien | Autre option écartée |
|---|---|---|---|---|
| a | Le compte-rendu montre-t-il les « passages à retravailler » ? | Oui : jusqu'à 3 passages, et un lien « Voir sur la partition » qui ouvre le passage déjà sélectionné, prêt à pratiquer | PO, UX, PED | Pas de passages au MVP |
| b | L'application peut-elle jouer l'autre main pendant qu'on travaille une main au micro ? | Oui en MIDI ; au micro seulement avec des écouteurs filaires | PO, UX | Jamais au micro au MVP |
| c | Garde-t-on les trois niveaux d'exigence et l'analyse des reprises au MVP ? | Oui, les deux ; le niveau se choisit aussi à chaque séance | PO, PED | Un seul niveau ; reprises ignorées |
| d | Peut-on ajouter une étiquette à l'import ? | Non au MVP (étiquettes en V1) | PO | Étiquettes simples dès le MVP |
| e | Mode sombre au MVP ? | Non, thème clair seul ; couleurs préparées pour le mode sombre de la V1 | PO, UX | Mode sombre dès le MVP |
| f | L'application est-elle en anglais dès le MVP ? (question QO-16 de l'EDB) | Oui, français et anglais (anglais américain), avec un lexique des termes validé par PED | PO, PED | Français seul au MVP |
| g | Quel ton pour les messages ? Tutoiement ou vouvoiement ? (QO-12 de l'EDB) | Un seul style, sobre et encourageant ; tournures impersonnelles (« Passage joué sans erreur ») ; vouvoiement quand il faut s'adresser à l'utilisateur ; style ludique avec tutoiement en V1 | PED, UX, JUR, PO | Tutoiement partout |

**Impact.** Fixe le contenu de plusieurs écrans du MVP ; l'EDB v0.6 précise la part de chaque exigence S ou C nécessaire au MVP (EF-RES-06, EF-ANA-08, EF-ANA-10, EF-ECO-08) et retire du MVP l'étiquette (EF-IMP-06), le style de retour (EF-CPT-02) et le choix du thème (EF-CPT-05). **Échéance.** Avant I2 (points a, b, c, g) ; avant I4 (d, e, f).

### QO-25 — Quand l'application considère-t-elle qu'on s'est arrêté ?

**Statut : décidée le 2026-11-07 — recommandation retenue.**

**Question.** En jeu continu, après combien de secondes sans jouer l'application met-elle la séance en pause, et que se passe-t-il ensuite ?

**Recommandation** (PED, UX, PO), **retenue** :
- **pause** après 7 s sans nouvelle note en niveau indulgent, 6 s en standard, 5 s en exigeant ; le curseur reste sur la dernière note jouée et le micro reste ouvert ;
- **reprise sans toucher l'écran** : jouer une note relance le décompte et la reprise au début de la mesure précédente ; l'interruption compte pour un « arrêt » ;
- **fin automatique** après 30 s de pause ;
- en mode attente (où l'on peut chercher une note longtemps), pas de pause avant 2 minutes.

Autres options écartées : délai fixe de 8 s ; fin de séance immédiate (pas de pause).
**Impact.** Mains toujours sur le clavier ; un arrêt suivi d'une reprise est bien compté (US-PRA-08 ; EDB EF-PRA-08 et 06 §4.1).
**Échéance.** Avant I2.

### QO-39 — Comment calculer le score ?

**Statut : décidée le 2026-11-07 — option B, avec les deux précisions.**

**Question.** Faut-il changer la formule du score global proposée par l'EDB ?

**Pourquoi.** Avec la formule de l'EDB v0.5, un élève qui joue 15 % de fausses notes, mais en rythme et sans s'arrêter, obtient environ 92 / 100. Un professeur ne lui dirait jamais « 92 » : l'élève croirait le morceau acquis.

| Option | Effet |
|---|---|
| A — Formule de l'EDB v0.5 (somme pondérée) | 15 % de fausses notes → 92 |
| B — Formule « multiplicative » : le score global ne dépasse jamais le score des notes | 15 % de fausses notes → 85 ; notes parfaites mais rythme très irrégulier → 85 |

Deux précisions vont avec : en **mode attente**, une note compte comme juste seulement si elle est trouvée **du premier coup** (sinon le score vaudrait toujours 100) ; le score de **continuité** tient compte de la longueur jouée (3 hésitations sur 4 mesures ne valent pas 3 hésitations sur 3 minutes).

**Recommandation : B, avec les deux précisions** (PED ; QA pour le mode attente ; PO). **Décision : B.**
**Impact.** EDB 06 §5 modifiée (MOD-01 à MOD-03) ; US-RES-06 ; scores vérifiés sur 7 séances types. Les formules restent des paramètres.
**Échéance.** Avant I2.

### QO-40 — Points d'orgue et ralentis écrits

**Statut : décidée le 2026-11-07 — option A.**

**Question.** Accepte-t-on qu'au MVP le rythme **ne soit pas évalué** autour des points d'orgue et pendant les ralentis ou accélérations écrits (rit., rall., accel.), plutôt que d'essayer de prévoir leur durée ?

**Pourquoi.** Sinon, un élève qui tient correctement un point d'orgue serait déclaré « en retard » sur toutes les notes suivantes : c'est une source systématique de fausses erreurs.

Options : **A** ne pas évaluer le rythme à ces endroits (les notes restent évaluées) ; **B** modéliser une durée type (plus complexe, risque d'erreurs).
**Recommandation : A** (PED, PO). **Décision : A.** **Impact.** US-ANA-07 CA2 ; EDB 06 §4.3 ; les points d'orgue sont admis dans les morceaux de démonstration (US-BIB-06).
**Échéance.** Avant I2.

---

## 6. Urgence 4 — au go / no-go technique (J1), avant I3

### QO-29 — Si le jeu continu au micro fonctionne, comment le présenter ?

**Statut : décidée le 2026-11-07 — option A.**

**Question.** Si la preuve de concept du micro réussit aussi en jeu continu, mais mesurée sur votre seul téléphone et votre seul piano, comment l'intégrer au MVP ?

| Option | Description |
|---|---|
| A | Vous décidez au vu du rapport de POC-01 relu par QA et l'architecte ; la fonction est marquée « expérimental », avec par défaut aucun retour pendant le jeu (résultats à la fin) ; elle est revérifiée en bêta sur d'autres appareils |
| B | Même chose sans mention « expérimental » |
| C | Attendre des mesures sur 2 appareils et 2 pianos (testeurs bénévoles) avant de l'inclure |

**Recommandation : A** (PO, PED, UX). **Décision : A.** **Impact.** US-ECO-08 CA3 et CA5, US-PRA-01 CA5 ; EDB EF-PRA-03 CA3 et 13 §3. **Échéance.** À J1.

### QO-32 — Précision du rythme au micro

**Statut : décidée le 2026-11-07 — recommandation retenue.**

**Question.** Le micro situe le début d'une note avec une incertitude de 20 à 50 ms. Comment éviter de signaler de fausses erreurs de rythme ?

**Recommandation** (PED, PO), **retenue** : au micro, la tolérance rythmique et la fenêtre des accords ne descendent jamais sous **deux fois l'incertitude mesurée** ; le niveau « exigeant » n'est proposé au micro que si cette incertitude est assez faible (2σ ≤ 50 ms ; sinon il est grisé avec une explication) ; un écart de rythme n'est marqué sur la partition qu'à 1,5 fois la tolérance. Autres options écartées : niveau exigeant toujours indisponible au micro ; pas d'adaptation.
**Impact.** Le niveau « exigeant » peut être indisponible au micro (US-CPT-03 CA5, US-ECO-08 CA4 ; EDB 06 §4.1). **Échéance.** À J1 (l'incertitude est mesurée par POC-01).

### QO-41 — Modèle de reconnaissance entraîné sur des données « non commerciales »

**Statut : décidée le 2026-11-07 — option A.**

**Question.** Peut-on intégrer un modèle de reconnaissance des notes publié sous licence libre, mais entraîné en partie sur des données « non commerciales » (cas probable : Basic Pitch, entraîné notamment sur MAESTRO) ?

| Option | Description |
|---|---|
| A | Oui au MVP, risque juridique résiduel documenté ; avis d'avocat avant la V1 si une version payante est envisagée |
| B | Non : n'utiliser que des modèles entraînés sur des données libres (choix beaucoup plus restreint) |

**Recommandation : A** (JUR). **Décision : A.** Dans tous les cas, sont exclus les modèles dont la licence elle-même interdit l'usage commercial ou restreint l'usage (US-CNF-03 CA3 ; EDB 11 §3).
**Échéance.** Au choix du modèle, après POC-01.

### QO-42 — Que faire si le micro échoue même en mode attente ?

**Statut : décidée le 2026-11-07 — A si les morceaux à une main atteignent les objectifs, sinon C.**

**Question.** Si la preuve de concept du micro n'atteint pas ses objectifs, même en mode attente, que fait-on ?

| Option | Description |
|---|---|
| A | Micro limité aux morceaux à une main, marqué « expérimental » (I3 réduit) |
| B | MVP en MIDI seul ; le porteur ne peut alors plus utiliser l'application à son piano |
| C | Retravailler la reconnaissance avant de continuer le MVP |

**Recommandation : A si les morceaux à une main atteignent les objectifs, sinon C** (PO). Dans tous les cas, I4 (profil, historique, import MIDI) démarre après I2 sans attendre (QA). **Décision : la recommandation.** **Impact.** Branches de J1 ([05 §2](05-plan-de-release.md)) ; EDB 13 §3.
**Échéance.** À J1, seulement si le cas se produit.

---

## 7. Urgence 5 — avant I5, I6 ou la bêta

### QO-11 — Garder le détail de toutes les séances ?

**Statut : décidée le 2026-11-07 — tout garder au MVP.**

**Question.** Conserve-t-on le détail complet (chaque note) de toutes les séances, ou seulement des dernières ?
**Recommandation : tout garder au MVP**, mesurer la place réellement occupée pendant la bêta, puis décider (PO). **Décision : la recommandation.** **Impact.** US-HIS-02 ; EDB EF-PRO-02 et 09. **Échéance.** Mesure en bêta ; choix du compactage avant la V1.

### QO-20 — Comment diffuser l'application, et la vérification des développeurs Android

**Statut : décidée le 2026-11-07 — option A pour l'alpha et la bêta ; canal public avant la V1.**

**Question.** Par quel canal diffuser la bêta puis la V1, sachant que Google imposera en 2027 (Europe comprise) que toute application installée sur un téléphone Android certifié soit enregistrée par un développeur vérifié, même hors Google Play ?

| Option | Coût |
|---|---|
| A — Fichiers APK sur la page de publication du dépôt, avec le compte gratuit de « distribution limitée » de Google (quelques dizaines d'appareils) pour la bêta | Gratuit |
| B — F-Droid (magasin d'applications libres) | Gratuit ; règles d'inclusion strictes |
| C — Google Play | Inscription payante unique (25 $) : contraire au budget nul sauf décision |

**Recommandation : A pour l'alpha et la bêta** (JUR, PO) ; choix du canal public (B ou C) avant la V1. **Décision : la recommandation.** **Impact.** US-CNF-04 CA7 ; US-PUB-01 ; EDB 11 §4. **Échéance.** Avant la bêta (A) ; avant la V1 (canal public).

### QO-26 — Répéter un passage en boucle pendant la pratique

**Statut : décidée le 2026-11-07 — oui.**

**Question.** Ajoute-t-on au MVP la possibilité de rejouer un passage plusieurs fois d'affilée en jeu continu (3 fois par défaut), avec un décompte et un score à chaque répétition ?
**Recommandation : oui** (PED, PO). Coût faible une fois le jeu continu prêt ; c'est le cœur du travail d'un passage. Sans tempo qui augmente (prévu en V1). **Décision : oui.** **Impact.** US-PRA-09 devient ferme ; nouvelle exigence EDB **EF-PRA-11** (boucle de pratique), qui définit le mode « boucle » cité par EF-ANA-04 et EF-PRA-05. **Échéance.** Avant I5.

### QO-28 — Comment recueillir les retours des testeurs sans serveur ?

**Statut : décidée le 2026-11-07 — option A.**

**Question.** Comment savoir, pendant la bêta, si les testeurs reçoivent de fausses erreurs et s'ils utilisent l'application, sans serveur ni mesure d'audience ?

| Option | Description |
|---|---|
| A | Bouton « Ce n'était pas une erreur » (et « J'ai joué juste mais l'application n'a pas avancé ») dans l'application, et export volontaire d'un rapport de test anonymisé envoyé par e-mail ; questionnaire court en complément |
| B | Questionnaire en ligne et tickets sur le dépôt seulement |
| C | Mesure d'audience avec consentement (avance une fonction de la V1) |

**Recommandation : A** (PO, PED, JUR sous conditions de protection des données, QA). Conditions JUR : aucun titre de morceau importé, aucune heure, aucun identifiant ; destinataire, durée de conservation et droits affichés ; testeurs majeurs, ou rapport envoyé par un parent. QA : compter comme inactifs les testeurs qui n'envoient rien. **Décision : A.**
**Impact.** US-RES-08 et US-CNF-05 deviennent fermes ; EDB 11 §2.4 et ENF-MAINT-04. **Échéance.** Avant I6.

### QO-43 — Délai du compte-rendu au micro

**Statut : décidée le 2026-11-07 — option C.**

**Question.** Si l'analyse complète au micro ne peut pas afficher le compte-rendu en 2 secondes après un passage de 5 minutes, accepte-t-on un délai plus long au micro (avec une barre de progression) ?

**Contexte.** L'objectif de la POC-01 (10 s d'analyse par minute jouée) contredit l'exigence de 2 s (ENF-PERF-04). La story vise que l'analyse avance pendant le jeu, pour tenir les 2 s.
Options : **A** garder 2 s partout ; **B** au micro, accepter jusqu'à 10 s avec progression affichée ; **C** décider après mesure.
**Recommandation : C** (PO, QA). **Décision : C** : mesurer en I5, puis le porteur choisit entre A et B au vu de la mesure. **Impact.** US-ECO-08 CA2 ; EDB ENF-PERF-04. **Échéance.** Mesure en I5.

---

## 8. Questions closes

| Question | Résolution |
|---|---|
| QO-33 — Critères du MVP après la décision « micro dans le MVP » | Résolue par l'EDB v0.5 (04 §4 mentionne le micro et le MIDI) |
| QO-10, QO-11, QO-20, QO-23 à QO-32, QO-34 à QO-43 | Décidées le 2026-11-07 (ce fichier), appliquées dans l'EDB v0.6 et le PRD v0.3 |
| QO-44 | Décidée le 2026-11-08 (option A, §2.3), appliquée dans le PRD v0.4 |
| QO-45 à QO-53, QO-55 | Décidées le 2026-11-08 ([DAT 14](../DAT/14-decisions-a-prendre.md)), appliquées dans l'EDB v0.7, le PRD v0.4 et le DAT v0.3 |
| QO-54 | **Ouverte** ([DAT 14](../DAT/14-decisions-a-prendre.md)), à trancher avec le canal public de QO-20 avant la V1 |
| QO-12, QO-16 (EDB) | Tranchées par QO-24 g et f |

Les modifications de l'EDB proposées par les relecteurs sont listées dans [10 — Modifications de l'EDB](10-modifications-edb-proposees.md) ; toutes sont appliquées dans l'EDB v0.6. Celles du DAT (MDAT-01 à MDAT-42, [DAT 15](../DAT/15-modifications-edb-prd-proposees.md)) sont appliquées dans l'EDB v0.7 et le PRD v0.4.
