# Revue QA du PRD v0.1

| Élément | Valeur |
|---|---|
| Relecteur | `qa-testeur` |
| Date | 2026-11-07 |
| Objet | PRD v0.1 (`docs/PRD/`, tous les fichiers), au regard de l'EDB v0.4 (`docs/EDB/`) |
| Points demandés | Testabilité de chaque critère d'acceptation (CA) des stories MVP ; définitions de prêt et de fini ; tolérance de non-régression du banc (US-QUA-02) ; protocole de mesure sur appareil (US-QUA-04) ; plan de release (vérifications reportées en bêta, critères de J2) ; cohérence de la matrice ; cas limites oubliés |
| Hors champ | Les 11 incohérences de l'EDB listées dans le [README du PRD](../README.md#incohérences-relevées-dans-ledb) (connues, en cours de correction) ne sont pas répétées. |

Légende des gravités :
- **Bloquant** : rend une porte qualité (banc, jalon, définition de fini) inapplicable ou une cible centrale invérifiable ; à corriger avant de démarrer l'incrément concerné.
- **Majeur** : critère non mesurable, contradictoire, invérifiable avec le matériel disponible, ou trou de couverture qui laisserait passer une anomalie visible par l'utilisateur.
- **Mineur** : imprécision, incohérence documentaire, amélioration de testabilité.

---

## 1. Verdict global

**Prêt avec corrections.**

Le découpage (epics, stories, incréments) est solide, les stories sont presque toutes rédigées de façon vérifiable, la traçabilité est complète (vérifiée par script, voir §2) et le plan tient compte honnêtement du matériel absent. En revanche, **la mesure de la qualité, qui est la promesse centrale du produit (« fiable avant d'être riche »), n'est pas encore opposable** :

1. la règle de non-régression du banc est contradictoire entre US-QUA-02 et la définition de fini, et indéfinie pour le micro (QA-01) ;
2. les cibles de fausses erreurs « sur des séances réelles » sont invérifiables, faute de vérité terrain et parce que le produit interdit de garder l'audio (QA-02) ;
3. les cibles de performance des incréments I1 et I2 dépendent d'un protocole prévu en I3, sans statistique de décision (QA-03) ;
4. le score et les métriques du mode attente — le seul mode micro garanti au MVP — ne sont pas définis (QA-04).

Ces quatre points sont locaux et corrigeables sans remettre en cause la structure. Les corrections QA-01 et QA-03 conditionnent la fin de I0 ; QA-02 et QA-04 conditionnent I2 et I3.

**Bilan : 4 bloquants, 29 majeurs, 33 mineurs (66 remarques), plus 52 cas limites manquants (§4).**

---

## 2. Ce qui a été vérifié, et comment

### 2.1 Vérifié par commandes (grep, awk, comparaison de listes) sur les fichiers du dépôt

Instantané lu le 2026-11-07 vers 15 h 30. L'EDB est modifié en parallèle par d'autres agents (exemple constaté pendant la revue : le titre de EF-CPT-07 a changé entre deux lectures) ; les résultats ci-dessous valent pour cet instantané.

| Vérification | Résultat |
|---|---|
| Stories MVP : titres `### US-…` de 03 vs tableau récapitulatif de 03 | 84 = 84, mêmes identifiants, même ordre |
| Statuts | 76 fermes, 1 conditionnelle, 7 proposées : conforme au README et à 05 §4 |
| Priorités des stories MVP | 77 M, 7 S |
| Stories par premier incrément (03) vs 05 §4 | I0 5, I1 24, I2 24, I3 11, I4 15, I5 2, I6 3 = 84 : conforme ; proposées/conditionnelles : 8, conforme |
| Exigences EF de l'EDB vs matrice | 102 EF à titre `###` + 5 EF-PRF en tableau = 107 : conforme ; aucune exigence de l'EDB absente de la matrice ; aucun identifiant de la matrice absent de l'EDB |
| Exigences ENF | 54 : conforme |
| Priorités MoSCoW EDB vs colonne « Prio » de la matrice | Identiques pour les 161 exigences (seule différence : ENF-COMP-01 « W (now) » vs « W », sans conséquence) |
| Nombre d'exigences M | 47 EF + 29 ENF = 76 : conforme à la synthèse de 06 |
| Identifiants de stories cités dans tout le PRD | Tous existent (03 ou 04) |
| Chaque story MVP apparaît dans la matrice | Oui, les 84 |
| Champ « Exigences » des stories → ligne de la matrice | 4 liens absents de la matrice (QA-60) |
| Lignes de la matrice → champ « Exigences » de la story | 10 liens non réciproques (QA-60) |
| Dépendances et références dans les CA vers une story d'un incrément **plus tardif** | 6 dépendances et 11 références (QA-12) |
| Termes recherchés et **absents** du PRD et de l'EDB | « mode détaillé » (jamais défini comme réglage), règle de « première tentative » en mode attente, définition de « 10 pages », sauvegarde automatique Android / permission INTERNET, routage du micro avec écouteurs, statistique de décision (centile, médiane) |
| Calculs | Intervalles de Wilson à 95 % (QA-07) ; 435 Hz = −19,8 cents et 446 Hz = +23,4 cents par rapport à 440 Hz (QA-48) |

### 2.2 Supposé (connaissance de la plateforme Android, **à confirmer par `architecte`**)

Comportements cités dans QA-28, QA-29, QA-30 et §4 : sauvegarde automatique des données d'application vers le compte Google ; bascule système « Accès au micro » (Android 12 et plus) qui fournit un signal silencieux ; autorisation « uniquement cette fois » ; bascule de la captation vers le micro d'un casque filaire ou Bluetooth ; types MIME génériques des `.mxl` reçus par mail ; simulation des appels sur l'émulateur.

### 2.3 Impossible à vérifier dans l'environnement actuel

Toute affirmation sur le comportement réel de l'application (rien n'est encore construit) ; la faisabilité des cibles [HYP] ; le comportement d'un vrai piano numérique en USB ; la lisibilité sur une vraie tablette.

---

## 3. Tableau des remarques

### 3.1 Mesure de la qualité de l'analyse (banc, corpus, métriques)

| ID | Gravité | Référence | Constat | Correction proposée |
|---|---|---|---|---|
| QA-01 | **Bloquant** | 03 US-QUA-02 CA3 ; 07 D3 ; 05 §1 principe 5 | Règle de non-régression **contradictoire et incomplète**. D3 exige « le taux de fausses erreurs n'augmente pas, en MIDI comme au micro » (tolérance nulle partout) ; US-QUA-02 CA3 prévoit une « tolérance statistique pour le micro fixée avec `expert-audio-mir` » (non définie, donc la story n'est pas prête au sens de R2). Rien ne dit quoi faire quand le **corpus change de version** (le taux bouge sans que l'analyse change), ni si la règle s'applique au total ou par type d'erreur. Un taux en pourcentage masque des régressions locales (une nouvelle fausse erreur compensée par un fichier ajouté). Rien n'oblige à vérifier que l'exécution est déterministe, ni que le résultat du banc sur ordinateur correspond à l'appareil. | Réécrire US-QUA-02 CA3 : « Chaque exécution compare ses résultats à la référence (dernière version acceptée de l'analyse) **sur la même version du corpus** ; quand le corpus change, la référence est d'abord recalculée sur le nouveau corpus. Blocage : (a) MIDI : le **nombre** de fausses erreurs, au total et **par fichier**, n'augmente pas (tolérance 0) ; (b) micro : deux exécutions successives donnent des résultats identiques (déterminisme vérifié à chaque exécution) ; une hausse du taux de plus de **0,1 point** (1 pour 1 000 notes jouées) [PO, à valider avec `expert-audio-mir`] bloque ; (c) dans les deux cas le taux reste ≤ la cible d'ENF-QA-01 ; (d) toute nouvelle fausse erreur, même sous la tolérance, est listée (fichier, mesure, type) ; (e) une hausse acceptée en échange d'un autre gain n'est possible que par décision consignée du porteur. Les taux sont contrôlés par type (notes, rythme, accords, continuité) et au total. » Aligner D3 : « Non-régression selon la règle de US-QUA-02 CA3. » Ajouter : « À chaque incrément, le corpus micro est aussi rejoué **sur le téléphone de référence** (injection de fichiers audio à la place du micro) ; tout écart avec le résultat de l'intégration continue est une anomalie. » |
| QA-02 | **Bloquant** | 03 US-ECO-07 CA2, US-ECO-08 CA1 ; 07 DM3 ; 05 I3 (« vérifier l'absence de fausses erreurs ») ; en tension avec US-ANA-02 CA4, US-ECO-04 CA4, 07 D8 | Les cibles de fausses erreurs « sur des séances réelles du porteur » sont **invérifiables** : le banc (US-QUA-02) travaille sur des fichiers, or une séance réelle ne laisse aucun fichier (l'audio ne doit jamais être conservé). Sans vérité terrain, « vérifier l'absence de fausses erreurs » au piano repose sur l'impression du porteur, qui ne sait pas toujours s'il a fait l'erreur signalée. | Ajouter à US-QUA-03 (ou créer US-QUA-05) un **enregistreur de séance réservé aux versions de test** : audio, événements détectés, résultat de l'analyse, version, réglages ; absent des versions destinées aux utilisateurs (contrôle automatique sur l'APK). Réécrire US-ECO-07 CA2 : « Taux de fausses erreurs ≤ 5 % [HYP] en mode attente : (a) sur la partie micro du corpus (banc) ; (b) sur au moins **20 séances réelles** du porteur à son piano (≥ **1 000 notes jouées** au total), capturées avec la version de test puis annotées par le porteur à la réécoute avec la partition ; résultat déclaré valable pour cet appareil et ce piano seulement (R-15). Les séances capturées enrichissent le corpus. » Préciser US-ECO-04 CA4 et D8 : « dans les versions destinées aux utilisateurs ». |
| QA-03 | **Bloquant** | 03 US-QUA-04 (incrément I3) ; CA de performance de I1 (US-VIS-04 CA2, US-VIS-08, US-LEC-01 CA2, US-IMP-01 CA5) et de I2 (US-PRA-02, US-RES-01 CA2, US-RES-03 CA1, US-ANA-09 CA4) ; 07 D4 | (1) Le protocole de mesure arrive en **I3**, alors que D4 l'exige pour finir des stories de I1 et I2 : ces incréments ne peuvent pas être finis. (2) Aucune **statistique de décision** : « ≤ 100 ms » s'entend-il en moyenne, en médiane, au 95e centile, au maximum ? (3) US-QUA-04 CA1 n'exige que l'existence d'un protocole, sans contenu minimal. (4) Le porteur n'a qu'un téléphone : il ne peut pas filmer l'écran avec l'appareil mesuré ; une latence « de l'appui à l'affichage » doit donc combiner des horodatages internes et, si possible, une vérification externe. | Avancer **US-QUA-04 CA1 en I0** (protocoles), garder les campagnes en I3 et I6. Réécrire CA1 : « Pour chaque cible ENF-PERF et ENF-RES du MVP, un protocole documenté précise : début et fin de la mesure ; instrumentation (horodatages internes — événement tactile, horodatage des tampons audio d'entrée et de sortie, affichage de l'image ; vérification externe par une caméra ≥ 120 images/s d'un second appareil quand un testeur peut en prêter un) ; version de type « release » ; conditions (batterie > 50 %, économie d'énergie désactivée, mode avion, appareil au repos thermique, une minute de préchauffage) ; nombre de mesures (≥ 50 pour une latence, ≥ 10 pour une durée) ; règle de décision : **95e centile ≤ cible** pour les latences (ENF-PERF-01, 02, 03, 10), **médiane ≤ cible et maximum ≤ 1,5 × cible** pour les durées (ENF-PERF-04, 05, 06) [PO, à valider]. Le protocole indique ce que l'instrumentation interne ne mesure pas (latence du capteur tactile, de l'écran). » |
| QA-04 | **Bloquant** | 03 US-RES-06 CA1, US-PRA-02 CA4, US-ECO-07 ; EDB 06 §5.1 et §5.4 ; 01 §5.1 | **Score du mode attente indéfini.** En mode attente le curseur attend la bonne note : toute note attendue finit par être jouée juste, donc avec S_notes = 100 × N_justes / (N_attendues + 0,5 × N_en_trop), le score vaut presque toujours 100 ; aucune règle ne dit si une note réussie au 3e essai est « juste ». **Métriques manquantes** pour ce mode : le banc mesure les fausses erreurs, mais ni les **validations indues** (le curseur avance sur une note fausse ou sur une résonance : l'élève apprend une erreur), ni les **non-validations** (note juste non reconnue, l'élève rejoue sans raison). Enfin, un enregistrement ne réagit pas au curseur : la façon d'évaluer le mode attente à partir de fichiers n'est pas définie. | Ajouter à US-RES-06 : « En mode attente, une note attendue est **juste si la première note jouée pour cet événement est la bonne**, sinon elle est fausse (une seule erreur par événement, quel que soit le nombre d'essais) ; le nombre d'essais est conservé et affiché au compte-rendu [à valider par `pedagogue-piano`]. » Ajouter à 01 §5.1 et à US-ECO-07 : « **Taux de validations indues** (avances du curseur sans que l'événement attendu ait été joué) ≤ 1 % [PO] ; **taux de non-validation** (événement joué juste non validé en ≤ 150 ms) ≤ 5 % [HYP] ; mesurés par le banc **événement par événement** (pour chaque événement attendu, la tentative enregistrée est soumise seule au détecteur). » |
| QA-05 | Majeur | 03 US-QUA-01 CA1 ; 01 §5.1 ; EDB 06 §9.1 | Le corpus v0 n'a **aucune taille minimale** (l'EDB parle de 30 à 50 pièces). Sans taille, les cibles sont statistiquement creuses. Calcul (intervalle de Wilson à 95 %) : avec 1 000 notes et 4 % observés, la borne haute est 5,4 % — on ne peut pas conclure « ≤ 5 % » ; avec 3 000 notes MIDI et 0,5 % observé, la borne haute est 0,82 % — « ≤ 1 % » est démontré. | Réécrire CA1 avec des minimums : « jeux MIDI corrects : ≥ 3 000 notes jouées sur ≥ 20 pièces ; jeux micro corrects : ≥ 1 000 notes en mode attente (et ≥ 1 000 en jeu continu si I5) ; chaque sous-ensemble ciblé par un CA : ≥ 200 notes. » Ajouter à US-QUA-02 CA2 : « chaque taux est publié avec son intervalle de confiance à 95 % et l'effectif. » |
| QA-06 | Majeur | 03 US-QUA-01 CA1 (liste « analyse micro ») vs CA d'autres stories | Plusieurs CA s'appuient sur des sous-corpus que US-QUA-01 ne prévoit pas : métronome par le haut-parleur (US-ECO-11 CA3) ; autre main jouée par l'application (US-PRA-03) ; position sur le pupitre et à 1,5 m (US-ECO-07 CA6, EF-ECO-04 CA2) ; registres extrêmes (US-ECO-07 CA5) ; notes répétées rapides (US-ANA-07) ; enregistrements transposés (US-ECO-09 CA1) ; conditions étiquetées « bruit », « trop faible », « saturé », « trop loin » (US-ECO-10, US-RES-07) ; jeux hésitants (US-ANA-03 CA4) ; attaques annotées (QA-24). | Compléter la liste de CA1 avec ces sous-corpus, chacun relié au CA qu'il sert. |
| QA-07 | Majeur | 03 US-QUA-02 CA1 ; US-QUA-01 CA2 | Le banc doit tourner « dans l'intégration continue gratuite » d'un dépôt public. Or des éléments du corpus ne peuvent pas y être publiés : jeux de données à licence non commerciale (« limités à l'évaluation interne »), enregistrements maison où l'on entend des voix (bruit ambiant « conversation »). Le « banc au vert » de l'intégration continue ne couvrirait alors pas le corpus micro. | Définir deux niveaux : **corpus public** (rejoué à chaque modification dans l'intégration continue) et **corpus privé** (rejoué par le porteur avant chaque acceptation d'incrément, rapport archivé). DI4 et J2 exigent les deux. Avis de `juriste-conformite` sur les voix enregistrées. |
| QA-08 | Majeur | 03 US-ANA-04 CA4 ; 01 §5.1 | La précision et le rappel **des erreurs** sont « mesurés » sans seuil : seules les fausses erreurs sur des interprétations **correctes** bloquent. Une analyse qui se trompe de type ou signale la voisine d'une vraie erreur sur un jeu fautif passerait toutes les portes. | Ajouter à 01 §5.1 et US-ANA-04 : « Précision des erreurs (erreur signalée = erreur annotée, même type, même note attendue) ≥ 95 % MIDI, ≥ 85 % micro [PO] ; rappel des erreurs ≥ 90 % MIDI [PO], mesuré sans seuil au micro au MVP ; les deux entrent dans la non-régression. » |
| QA-09 | Majeur | 03 US-ECO-08 CA1 ; EDB 06 §9.1 | Au micro sur le piano droit, il n'existe **pas de vérité terrain des instants d'attaque** (pas de piano numérique). « Vérifier à l'oreille et sur la forme d'onde » n'a pas la précision requise face à des tolérances de 50 à 150 ms. Les fausses erreurs de rythme au micro ne sont donc pas mesurables telles quelles. | Ajouter à US-QUA-01 : « Sous-corpus d'attaques annotées à la main (≥ 300 notes) avec un outil libre d'annotation audio, annoté deux fois ; écart entre les deux annotations ≤ 10 ms au 95e centile. » Scinder US-ECO-08 CA1 : fausses erreurs de notes ≤ 5 % [HYP] sur le corpus micro ; fausses erreurs de rythme ≤ 5 % [HYP] **sur le sous-corpus d'attaques annotées**. |
| QA-10 | Majeur | 03 US-ECO-08 CA2 vs US-ANA-09 CA4 et US-RES-03 CA1 (ENF-PERF-04) | **Contradiction de cibles.** US-ECO-08 CA2 accepte 10 s d'analyse différée par minute de jeu ; pour un passage de 5 minutes, cela fait jusqu'à 50 s, alors que le compte-rendu doit s'afficher en ≤ 2 s (ENF-PERF-04, cité dans le même CA). | Réécrire US-ECO-08 CA2 : « L'analyse différée avance **pendant le jeu**, au moins aussi vite que le jeu sur l'appareil de référence ; le compte-rendu d'un passage de 5 minutes s'affiche en ≤ 2 s après la fin du jeu (ENF-PERF-04). » Si c'est irréaliste au micro, le porteur doit assouplir ENF-PERF-04 pour le micro (nouvelle question ouverte). |
| QA-11 | Majeur | 03 US-LEC-01 CA4 ; US-QUA-01 CA3 ; US-RES-06 CA3 ; 07 D9 | Des validations **perceptives** sont confiées à des agents qui ne peuvent pas écouter : `pedagogue-piano` « juge le son acceptable » ; il « relit chaque annotation d'erreur volontaire » d'un enregistrement. Une relecture par agent ne peut vérifier que la cohérence d'un texte avec la partition. | US-LEC-01 CA4 : « [automatique] les 88 notes de la banque de sons produisent un son au-dessus d'un seuil, sans écart de niveau > 6 dB [PO] entre deux notes voisines ; [porteur] écoute de 3 morceaux de démonstration jugée acceptable. » US-QUA-01 CA3 : « chaque annotation est vérifiée à la réécoute par le porteur ; `pedagogue-piano` vérifie la cohérence de l'annotation avec la partition. » Règle générale dans 07 : un critère perceptif (écoute, lisibilité à distance) est vérifié par un humain. |

### 3.2 Définitions de prêt et de fini

| ID | Gravité | Référence | Constat | Correction proposée |
|---|---|---|---|---|
| QA-12 | Majeur | 07 DI1 ; 03 (dépendances) ; 05 §3 | DI1 exige que « toutes les stories fermes de l'incrément soient finies », mais 6 dépendances et 11 références de CA visent une story d'un **incrément plus tardif** (vérifié par script) : US-VIS-04 CA2 et US-VIS-08 (I1) → US-QUA-04 (I3) ; US-ECO-01 (I2) → US-CPT-02 (I4) ; US-ECO-02 CA3 (I2) → US-PRA-06 (I3) ; US-IMP-05 CA1 et CA4 (I1) → US-IMP-02 et US-BIB-07 (I4) ; US-RES-01 CA5 (I2) → US-CPT-04 (I4) ; US-CNF-04 CA3 (I0) → US-CNF-02 (I6) ; US-ANA-08 CA4 (I2) → US-RES-07 (I3) ; US-PRA-01 CA4 (I2) → US-ECO-08 (I5) ; US-ANA-02 (I2) → US-ECO-05 (I3) ; US-BIB-01 (I1) → US-HIS-01 (I4). Seules certaines de ces stories sont déclarées « en deux temps ». | Pour chaque story à cheval, rattacher **chaque CA à un incrément** (ex. « CA4 : I4 »). Réécrire DI1 : « Tous les CA rattachés à l'incrément sont vérifiés ; les CA rattachés à un incrément ultérieur sont suivis dans la story. » Corriger 05 §3 I3 : ajouter « US-PRA-02 (partie micro) », présente dans la fiche mais absente de la liste. |
| QA-13 | Majeur | 07 R2 ; 03 (toutes les stories) | R2 exige que « chaque critère dise comment il sera vérifié ». Aucune des 84 stories ne l'indique de façon systématique : selon sa propre définition de prêt, **aucune story n'est prête**. | Ajouter une étiquette à chaque CA : [AUTO] test automatisé ; [BANC] banc d'évaluation ; [MESURE] mesure sur le téléphone selon US-QUA-04 ; [TÉL] test manuel sur le téléphone ; [ÉMU] test manuel sur l'émulateur ; [PIANO] test au piano du porteur ; [REVUE agent] ; [PORTEUR] jugement humain ; [BÊTA] vérification reportée (voir QA-31). `qa-testeur` peut produire cet étiquetage. |
| QA-14 | Majeur | 07 §1 ; 03 US-IMP-06 CA2, US-VIS-08 CA2, US-ECO-09 CA3, US-ECO-10 CA1, US-ECO-06 CA3, US-ANA-06 CA4, US-RES-03 CA3, US-LEC-03 CA5, US-CPT-02 CA3 | Des CA contiennent des valeurs « à fixer », « à proposer » ou non chiffrées (taille maximale de fichier, mémoire minimale, seuil de désaccord, seuils de signal, dispersion, fenêtre d'arpège, pondérations, table de tempos, correspondance des niveaux). La définition de prêt ne l'interdit pas explicitement. | Ajouter R14 : « Aucun CA ne contient de valeur à fixer ; chaque valeur [HYP] ou [PO] est figée, avec sa source, à l'entrée de la story ; changer une valeur ensuite est tracé. » |
| QA-15 | Majeur | 07 §2.1 (exception « matériel absent ») | L'exception permet de déclarer une story **finie** sans vérification réelle, sans dire ce que la simulation doit prouver ni ce qui se passe si la bêta échoue. Combinée à J2 (QA-30), le MVP peut passer J2 avec le MIDI USB jamais essayé sur un vrai piano, sans que cela soit écrit nulle part pour les testeurs. | Réécrire : « Un CA qui exige un matériel absent est vérifié par simulation (preuve jointe) et inscrit au **registre des vérifications reportées** (QA-31) avec son critère chiffré de bêta. Un échec en bêta ouvre une anomalie bloquante pour J3. Les notes de version listent ces CA. » |
| QA-16 | Majeur | 07 §2.3 (DM1 à DM5) ; 07 §2.2 | La définition de fini du MVP n'a **ni critère d'anomalies** (DI5 n'existe que par incrément), **ni critère de performance** (aucun DM ne reprend les résultats de US-QUA-04). Aucune échelle de gravité des anomalies n'est définie. Aucune **non-régression manuelle** des incréments précédents n'est prévue : DI3 ne montre que l'incrément courant. | Ajouter DM6 : « Aucune anomalie bloquante ouverte ; anomalies majeures ouvertes acceptées une à une par le porteur. » DM7 : « Campagne US-QUA-04 complète, sans écart non accepté. » Ajouter en 07 l'échelle de gravité (bloquant, majeur, mineur, avec définitions). Ajouter DI6 : « La liste courte de recette des incréments précédents est rejouée sur l'APK de l'incrément (≤ 30 min) ; résultat archivé. » |
| QA-17 | Majeur | 07 D3, D5, D8 | D5 impose à **toute** story une vérification sur téléphone et émulateur, profils téléphone et tablette, portrait et paysage, mode avion — y compris pour une story de moteur sans écran (US-ANA-01) ou d'outillage. Une règle inapplicable sera contournée en silence. D8 (« aucun son écrit dans le stockage ») contredit l'enregistreur de test proposé en QA-02 s'il n'est pas restreint. | D5 : « selon la nature de la story (écran, captation, données) ; la fiche de test indique les vérifications non applicables. » D8 : « dans les versions destinées aux utilisateurs ». |

### 3.3 Plan de release, bêta et jalon J2

| ID | Gravité | Référence | Constat | Correction proposée |
|---|---|---|---|---|
| QA-30 | Majeur | 05 I6 « Critère de fin = J2 » ; 07 DM4 ; 03 US-CPT-06 CA3 | Critère J2 **non mesurable et incomplet** : un seul testeur ; « comprend son compte-rendu » sans mesure ; source de captation non précisée (sans piano numérique, ce sera le micro) ; « toutes les stories **M** fermes » exclut les stories S acceptées par le porteur (US-ECO-09, 10, 11, US-PRA-08) et la story conditionnelle si I5 existe ; pas de critère d'anomalies ni de performance ; incohérent avec le panel de 3 personnes de US-CPT-06 ; installer un APK hors store demande souvent de l'aide (sources inconnues, alertes de sécurité). | Réécrire : « J2 est atteint quand : (1) toutes les stories du périmètre MVP arrêté par le porteur (fermes, proposées acceptées, conditionnelle si I5) sont finies ; (2) **au moins 3 testeurs** qui ne connaissent pas l'application réalisent **sans intervention**, avec pour seule aide le guide d'installation écrit, le scénario de recette J2 : installer l'APK, importer un MusicXML fourni, l'écouter à 60 %, pratiquer en mode attente au micro sur un piano acoustique, ouvrir le compte-rendu ; (3) chacun répond juste à au moins 2 des 3 questions sur son compte-rendu (score, passage à retravailler, type d'erreur dominant) ; (4) banc au vert sur les corpus public et privé, cibles ENF-QA-01 atteintes ; (5) DM6 et DM7 (QA-16) satisfaits ; (6) les notes de version listent ce qui n'est vérifié que par simulation (MIDI USB) ou sur un seul appareil et un seul piano (micro). » |
| QA-31 | Majeur | 05 §5 (bêta) | Les vérifications reportées en bêta n'ont **ni critère chiffré, ni nombre de testeurs, ni conséquence en cas d'échec**. Liste incomplète : US-VIS-07 sur vraies tablettes (7 et 13 pouces) ; US-VIS-08 CA2 / ENF-RES-04 sur un appareil d'entrée de gamme ; latences de US-QUA-04 sur d'autres appareils ; EF-ECO-04 CA1 au micro sur un piano à queue et un piano numérique joué sur ses haut-parleurs (cas proposé par US-CPT-02 pour « numérique sans connexion » et « Bluetooth ») ; US-ECO-11 avec de vrais écouteurs ; US-ECO-02 CA2 et CA4 (branchement à chaud, plusieurs appareils) ; US-PRA-03 en MIDI avec un vrai piano ; US-HIS-03 (mises à jour sur les téléphones des testeurs). | Transformer 05 §5 en **registre des vérifications reportées** avec les colonnes : story et CA ; preuve de la simulation faite au MVP ; critère de bêta chiffré ; nombre minimal de testeurs ou d'appareils ; conséquence si échec (anomalie bloquante pour J3). Exemple pour le MIDI réel : « ≥ 3 modèles de pianos numériques de marques différentes ; 0 note perdue sur 500 notes jouées ; branchement et débranchement détectés en < 2 s ; latence mesurée par US-ECO-06 ≤ 30 ms [PO]. » |
| QA-32 | Majeur | 05 §2 et §3 ; EDB 13 §3 (J1) | Le plan ne traite pas le cas « **POC-01 non concluant même en mode attente** », prévu par l'EDB (arrêt, ou micro limité aux pièces simples à une main). Or I3 a J1 pour prérequis et I4 dépend de I3 : tout le reste du MVP serait bloqué, alors que I4 (profil, historique, import MIDI) n'a pas besoin du micro. Dans ce cas, le porteur ne peut même plus utiliser l'application à son piano. | Ajouter la branche : « Si POC-01 n'est pas concluant en mode attente : décision du porteur entre (a) micro limité aux pièces à une main, avec un I3 réduit et un statut « expérimental », ou (b) MVP MIDI seul ; I4 démarre après I2 ; J2 et DM sont adaptés en conséquence. » |
| QA-33 | Mineur | 05 §3 I3 et I5 (« Le porteur peut tester ») | Le texte « vérifier que les erreurs de rythme signalées correspondent à son ressenti et à l'enregistrement » suppose un enregistrement qui n'existe pas dans le produit. | Renvoyer à l'enregistreur de test (QA-02). |

### 3.4 Stories MVP : testabilité critère par critère

Les CA qui ne figurent pas ci-dessous sont jugés vérifiables en l'état, sous réserve d'être étiquetés (QA-13).

| ID | Gravité | Référence | Constat | Correction proposée |
|---|---|---|---|---|
| QA-18 | Majeur | US-CPT-06 CA3 | Le panel inclut le porteur, qui connaît l'application (« sans aide » n'a pas de sens pour lui) ; la règle de décision n'est pas dite (tous ? la médiane ?) ; au micro, chaque personne a besoin d'un piano. | « Sur 3 personnes qui ne connaissent pas l'application, à leur piano ou à celui du porteur, **chacune** atteint le premier compte-rendu en ≤ 5 min à partir de la première ouverture (installation non comprise), sans intervention ; chronométrage par un observateur muet. Le passage du porteur sert seulement de contrôle préalable. » |
| QA-19 | Majeur | US-IMP-01 CA2 vs CA6 | **Contradiction** : CA2 exige que tout fichier du corpus d'import s'importe sans erreur bloquante (100 %), CA6 se contente de 95 % sur le même corpus. Rien ne garantit que le corpus contient des fichiers de chacun des quatre logiciels (budget nul : Finale, Sibelius, Dorico sont payants). | CA2 : « Sur le sous-corpus "éditeurs" (≥ 5 fichiers valides par logiciel disponible parmi MuseScore, Finale, Sibelius, Dorico, provenance et licence documentées ; un logiciel sans fichier disponible est signalé), 100 % des fichiers s'importent sans erreur bloquante. » CA6 : « Sur le sous-corpus "tout-venant" (≥ 40 fichiers de sources publiques, retenus sans tri), taux de réussite ≥ 95 %. » |
| QA-20 | Majeur | US-IMP-01 CA5 ; US-VIS-08 CA1 et CA2 | « 10 pages » et « 30 pages » ne sont pas définis : un MusicXML n'a pas de pagination fixe (elle dépend de l'écran et du zoom). Trois cibles de performance en dépendent. | Désigner des fichiers de référence dans le corpus : « P10 : environ 250 mesures à deux portées, environ 4 000 notes ; P30 : environ 750 mesures » [à ajuster par `architecte`], cités par leur nom dans les CA. |
| QA-21 | Mineur | US-IMP-01 CA3 | Le nombre d'éléments attendu par catégorie n'a pas de source : établi à la main, c'est très long ; produit par la même bibliothèque, le test est circulaire. | « Valeurs de référence produites par un analyseur indépendant de celui de l'application, écarts examinés à la main et consignés. » |
| QA-22 | Mineur | US-IMP-03 CA4 | L'EDB (EF-IMP-05 CA2) dit « en **moins de** trois interactions » (2 au plus), le PRD « 3 interactions au plus ». « Interaction » et point de départ ne sont pas définis. | « Depuis la notification "téléchargement terminé", l'aperçu d'import est atteint en 2 appuis au plus », ou faire valider l'écart par le porteur. |
| QA-23 | Mineur | US-IMP-03 CA1 | « Limité aux formats pris en charge » : si le filtre se fait par type MIME, des `.mxl` déclarés avec un type générique (fréquent pour les pièces jointes) deviennent invisibles [supposé]. | « Le sélecteur montre les fichiers des formats pris en charge, y compris quand leur type déclaré est générique ; le contrôle du format se fait à l'import. » |
| QA-24 | Mineur | US-IMP-06 CA3 | « Sans saturer la mémoire » n'est pas mesurable. | « Sur les fichiers piégés du corpus (expansion d'entités XML, archive dont le contenu décompressé dépasse 1 Go), l'import est refusé en ≤ 5 s, la mémoire du processus reste sous la limite fixée en CA2 et l'application reste utilisable. » |
| QA-25 | Mineur | US-BIB-01 CA5 | « Fluide » n'est pas mesurable. | « Défilement automatisé de bout en bout d'une liste de 200 morceaux, version release, téléphone de référence : ≤ 5 % d'images en retard (statistiques d'affichage d'Android). » |
| QA-26 | Majeur | US-VIS-03 CA3 et CA4 | La lisibilité à 60 et 70 cm repose sur un test humain (le protocole reste à écrire) ; le CA tablette est reporté en bêta faute de tablette. Le porteur connaît les morceaux de démonstration : il ne sert pas de lecteur valable. | Ajouter un critère **objectif vérifiable dès maintenant, émulateur compris** : « Au réglage par défaut, l'interligne de la portée mesure à l'écran ≥ 2,0 mm [à valider par `designer-ux`] (calcul à partir de la densité physique de l'écran), sur le téléphone de référence et sur le profil tablette 10 pouces. » Garder le test humain comme confirmation, sur un extrait inconnu du lecteur, avec un lecteur qui n'est pas le porteur. |
| QA-27 | Mineur | US-VIS-01 CA3 | « Toute évolution du rendu est comparée aux captures de référence » : comparer à la main 20 partitions à chaque modification ne sera pas fait. | « Test automatisé de comparaison des captures (seuil de différence) ; toute différence est examinée et la capture de référence mise à jour sur décision. » |
| QA-28 | Majeur | US-CNF-01 CA2 ; US-CPT-05 CA3 | L'écoute du trafic réseau de l'émulateur ne voit pas les sorties de données faites **par le système** pour l'application : sauvegarde automatique Android des données d'application vers le compte Google [supposé]. Après « Supprimer toutes mes données », une réinstallation pourrait restaurer une ancienne sauvegarde. Le contrôle le plus fort n'est pas demandé : l'absence de permission réseau. | US-CNF-01 : « Le manifeste final de l'APK ne déclare pas la permission INTERNET (contrôle automatique à chaque construction). » « La sauvegarde automatique Android est désactivée ou exclut toutes les données, vérifié en forçant une sauvegarde puis en inspectant son contenu. » US-CPT-05 : « Après suppression puis réinstallation, aucune donnée n'est restaurée. » |
| QA-29 | Majeur | US-PRA-08 CA2 vs US-ECO-04 CA2 | **Contradiction.** La story promet d'« arrêter et reprendre sans toucher l'écran » ; or la suspension ferme le micro (US-ECO-04 CA2 : micro fermé quand la séance est en pause), donc l'application ne peut pas entendre la reprise, et CA2 propose un choix à l'écran. | Trancher : (a) pendant une suspension automatique, le micro reste ouvert au plus 60 s [PO], indicateur allumé, et reprendre à jouer relance la séance ; ou (b) renommer la story « Arrêt automatique » et accepter un appui pour reprendre. Aligner US-ECO-04 CA2 sur le choix. |
| QA-34 | Majeur | US-RES-07 CA1 | La « cause probable » (bruit, niveau trop faible, appareil trop loin) suppose l'analyse du signal de US-ECO-10, story **proposée** : dépendance cachée d'une story ferme (M) vers une story qui peut être refusée. L'exactitude de la cause affichée n'a pas de critère. | Ajouter la dépendance et un repli : « sans US-ECO-10, message générique sans cause ». CA de mesure : « Sur les enregistrements du corpus étiquetés par condition, la cause affichée correspond à l'étiquette dans ≥ 80 % des cas [PO]. » |
| QA-35 | Majeur | US-ANA-05 CA2 | Un décalage de 30 ms est **inférieur à toutes les tolérances** (T = 50, 90 ou 150 ms) : le test passe même sans compensation et ne prouve rien. | « Étant donné un jeu MIDI simulé juste, décalé de +80 ms constants, au niveau exigeant (T = 50 ms), alors aucune note n'est "en retard". Étant donné le même jeu avec un décalage qui croît de 0 à +200 ms, alors des notes "en retard" apparaissent (la compensation n'efface pas une vraie dérive). » |
| QA-36 | Mineur | US-ANA-03 CA3 et CA4 | CA4 (« ne cassent pas l'alignement ») n'est pas mesurable ; CA3 ne dit pas d'où se comptent les « 2 mesures ». | CA3 : « Toutes les notes jouées à partir de la 3e mesure qui suit le point de reprise sont alignées sur la bonne note attendue. » CA4 : « Sur le sous-corpus "hésitant" (pauses jusqu'au seuil d'arrêt, tempo à ±20 %), exactitude de l'alignement ≥ 98 % en MIDI. » |
| QA-37 | Majeur | US-PRA-02, 2e scénario Gherkin ; US-ECO-07 CA4 | « Signalée **immédiatement** » n'est pas mesurable ; et au micro, ce scénario contredit US-ECO-07 CA4 (en cas de doute, l'application attend au lieu de signaler). | « Alors, en MIDI, la fausse note est signalée en ≤ 100 ms (visuel, et son si activé) ; au micro, elle est signalée en ≤ 150 ms si sa confiance atteint le seuil, sinon rien n'est signalé ; et le curseur ne bouge pas. » CA3 : remplacer « en même temps » par « dans la fenêtre de simultanéité du niveau ». |
| QA-38 | Mineur | US-PRA-02 CA4 vs US-RES-03 CA2 | « Les notes le plus souvent ratées » n'apparaissent pas dans le contenu du compte-rendu défini par US-RES-03 : on ne sait pas lequel des deux fait foi. | Ajouter l'élément à US-RES-03 CA2 (pour le mode attente) ou le retirer de US-PRA-02. |
| QA-39 | Majeur | US-RES-04 CA1 et CA3 ; EDB 10 §5 | Les résultats « rejouée », « accord non synchronisé », « hésitation » et « arrêt » sont calculés (US-ANA-04 à 06) et comptent dans les scores, mais **n'ont aucune représentation** définie : CA3 (« aucun résultat par la seule couleur ») est invérifiable pour eux. | Compléter la liste de CA1 et la légende, symboles à proposer par `designer-ux`. |
| QA-40 | Majeur | US-ANA-08 CA2 ; US-RES-04 CA5 ; US-RES-05 CA2 | Le « **mode détaillé** » est cité trois fois mais aucune story ne dit comment l'activer, où, ni sa valeur par défaut (vérifié : aucune définition dans le PRD ; l'EDB ne fait que le citer). | Ajouter à US-RES-04 : « Un interrupteur "Afficher les détections incertaines", désactivé par défaut, est disponible dans la partition annotée ; son état est mémorisé. » |
| QA-41 | Mineur | US-ANA-06 CA3 et CA4 | CA4 : fenêtre élargie de l'arpège non chiffrée. CA3 distingue les mains alors que l'attribution des mains (EF-ANA-07) est en V1, et que US-RES-06 renvoie les scores par main à la V1 pour ce motif. | CA4 : « fenêtre d'arpège = fenêtre du niveau × nombre de notes de l'accord [à valider] ». CA3 : « main déduite de la note attendue associée » et le dire explicitement dans US-ANA-07. |
| QA-42 | Mineur | US-ANA-07 CA3 | « L'absence d'une petite note est une erreur **mineure** » : la formule de score ne connaît pas de gravité. Pour les trilles « non évalués », on ne sait pas si la note principale est attendue. | « Petite note absente : classée "manquée (mineure)", hors du score de notes, citée au compte-rendu. Trille, mordant, gruppetto : seule la note principale est attendue, au début de l'ornement. » [à valider par `pedagogue-piano`] |
| QA-43 | Mineur | US-RES-03 CA3 et CA5 | Pondérations de gravité non chiffrées, donc classement des passages non testable ; CA5 (« ne jugent jamais la personne ») sans grille de relecture. | CA3 : « poids manquée = fausse = 3, rythme = 2, en trop = 1, rejouée = 0,5 [PO] ; à égalité, le passage le plus tôt dans la partition d'abord. » CA5 : « la liste exhaustive des textes du compte-rendu est relue selon une grille (aucun qualificatif de la personne, aucune formule négative sans conseil). » |
| QA-44 | Mineur | US-RES-06 CA1 | Règle d'arrondi non précisée (« entiers ») : les tests aux limites seront ambigus. | « Arrondi à l'entier le plus proche, ,5 vers le haut, calculé une seule fois sur le score final. » |
| QA-45 | Mineur | US-RES-04 CA3 | Les simulateurs de couleur intégrés à Android proposent des anomalies (deutéranomalie, etc.) et non les « -anopies » citées [supposé] ; l'outil n'est pas désigné. | Désigner l'outil et ajouter la vérification avec l'inversion des couleurs et le mode sombre forcé du système (§4). |
| QA-46 | Mineur | US-ECO-06 CA1 et CA3 | « Par exemple 8 » et « dispersion trop forte » ne sont pas des valeurs. | « 8 clics ; résultat refusé si l'écart-type dépasse 25 ms [PO] ou si moins de 6 notes sont reçues. » |
| QA-47 | Mineur | US-ECO-05 | Aucune exactitude attendue pour la mesure du diapason ni de la latence ; « moins d'une minute » sans nombre d'essais. | « Diapason estimé à ± 3 cents [HYP] sur des sons de référence à 435, 440 et 446 Hz diffusés par un haut-parleur ; latence estimée à ± 10 ms ; durée < 60 s sur 5 calibrages successifs au piano. » |
| QA-48 | Mineur | US-ECO-09 CA1 | « Pas plus de fausses erreurs qu'à 440 Hz » est une égalité stricte sur une mesure bruitée. Calcul : 435 Hz correspond à −19,8 cents et 446 Hz à +23,4 cents. | « Sur le sous-corpus micro transposé de −20 et de +24 cents, le taux de fausses erreurs dépasse d'au plus 0,5 point [PO] celui du corpus d'origine. » |
| QA-49 | Mineur | US-ECO-07 CA5 et CA6 | « Très graves ou très aiguës » non borné ; « fonctionne » posé sur le pupitre n'est pas un résultat. | CA5 : « en dessous du la 1 et au-dessus du do 7 [à valider par `expert-audio-mir`] ». CA6 : « CA1 à CA3 sont mesurés avec l'appareil sur le pupitre et à 1,5 m. » |
| QA-50 | Mineur | US-ECO-10 CA1 | Seuils de signal non définis, pas de critère de justesse de l'indicateur. | « Sur les enregistrements du corpus étiquetés "bruit", "saturé", "trop faible", l'indicateur s'allume dans ≥ 90 % des cas ; sur les enregistrements normaux, dans ≤ 5 % des cas [PO]. » |
| QA-51 | Mineur | US-HIS-02 CA3 | « Sans délai perceptible » n'est pas mesurable. | « ≤ 500 ms [PO] jusqu'à l'affichage des 20 premières séances, base de 1 000 séances générée par un outil de test. » |
| QA-52 | Majeur | US-HIS-01 CA2 | (1) Arrêter le processus ne reproduit pas l'« arrêt brutal de l'**appareil** » d'ENF-FIA-01 (écritures non synchronisées). (2) Nombre et instants des arrêts non définis. (3) CA1 dit qu'une séance interrompue est enregistrée, CA2 ne garantit que les séances terminées : le sort d'une séance en cours lors d'un arrêt brutal n'est pas fixé. | « Test automatisé : 50 arrêts forcés du processus à des instants aléatoires pendant une séance et dans les 2 s qui suivent sa fin, et 10 arrêts brutaux de l'émulateur (équivalent d'une coupure d'alimentation) : aucune base corrompue ; toute séance terminée avant l'arrêt est présente ; la séance en cours est enregistrée "interrompue" avec ce qui a été joué jusqu'à la dernière mesure complète [règle à confirmer]. » |
| QA-53 | Majeur | US-HIS-03 CA2 | Le test ne couvre que N → N+1. Les testeurs sauteront des versions d'APK. | « Migration testée depuis **chacune** des versions diffusées (N−k → N), avec un jeu de données de référence archivé par version. » CA3 : « échec de migration provoqué par injection de défaut ». |
| QA-54 | Mineur | US-PRA-06 CA1 et CA3 | Recevoir un appel sur le téléphone de référence demande un second téléphone ; sur l'émulateur, l'appel est simulable [supposé]. CA3 : le moment où une séance est « jamais reprise » n'est pas défini. | CA1 : « vérifié sur l'émulateur (appel simulé) et sur le téléphone (appel réel ou mise en arrière-plan) ». CA3 : « … quand l'utilisateur quitte l'écran de séance ou quand le processus est arrêté ». |
| QA-55 | Mineur | US-LEC-04 CA3 ; US-ANA-07 CA3 (notes croisées) | MusicXML n'a pas d'élément standard « main » : « les indications de main explicites du fichier » ne sont pas définies (portée de la note ? texte « m.g. / m.d. » ?). | « La main est déduite de la portée de la note ; les textes "m.g." et "m.d." sont ignorés au MVP » [à valider par `pedagogue-piano`]. |
| QA-56 | Mineur | US-LEC-06 CA1 | « Affinée au temps près » : aucune story ne décrit comment sélectionner au temps près (US-VIS-06 sélectionne des mesures). | Ajouter le geste à US-VIS-06 ou retirer « au temps près ». |
| QA-57 | Mineur | US-QUA-03 CA4 | « Versions de test » et « versions destinées aux utilisateurs » ne sont pas définies : l'APK de bêta est-il une version de test ? | Définir les variantes de construction (interne, bêta, publique) ; « contrôle automatique : l'APK de bêta ne contient ni le simulateur MIDI, ni le clavier visuel de test, ni l'enregistreur de séance ». |
| QA-58 | Mineur | US-CNF-03 CA3 | Le contrôle automatique ne porte que sur les dépendances de construction ; sons, polices, morceaux de démonstration et poids de modèles sont des fichiers embarqués. | « Le contrôle échoue aussi si un fichier embarqué (sons, polices, partitions, modèles) n'est pas inscrit à l'inventaire. » |
| QA-59 | Mineur | US-CNF-04 CA3 | Renvoie à US-CNF-02 (I6) pour un critère de I0. | « Le numéro de version est visible sur l'écran principal dès I0, puis dans "À propos" (US-CNF-02). » |
| QA-62 | Mineur | US-CPT-05 CA3 | Le périmètre de l'inspection n'est pas précisé ; les fichiers exportés par l'utilisateur (US-BIB-05) hors de l'application ne sont pas couverts et ne doivent pas l'être, mais il faut le dire. | « Inspection : base de données, préférences, caches, dossiers propres à l'application en stockage interne et externe ; les fichiers exportés par l'utilisateur ne sont pas supprimés, et l'écran de confirmation le précise. » |
| QA-63 | Mineur | US-VIS-07 CA1 | « Utilisables » n'est pas mesurable. | « Sur chaque profil et chaque orientation : aucun texte tronqué, toutes les commandes accessibles sans défilement horizontal, cibles ≥ 48 dp ; vérifié par captures archivées. » |
| QA-64 | Mineur | US-VIS-08 CA2 | Mémoire minimale « à fixer » ; un émulateur à mémoire réduite ne reproduit pas un processeur d'entrée de gamme. | Fixer la valeur (QA-14) et dire la limite ; ajouter l'appareil d'entrée de gamme au registre de bêta (QA-31). |

### 3.5 Matrice de traçabilité et objectifs

| ID | Gravité | Référence | Constat | Correction proposée |
|---|---|---|---|---|
| QA-60 | Mineur | 06 §2, §3 ; champs « Exigences » de 03 | Liens non réciproques (vérifiés par script). Story → matrice : US-ECO-11 cite EF-PRA-03 et EF-PRA-05, absents de ces lignes ; US-QUA-04 cite « ENF-PERF-01 à ENF-PERF-06 » et ENF-PERF-10, mais les lignes ENF-PERF-02, 04, 05, 06 et 10 ne la mentionnent pas. Matrice → story (exigence absente du champ de la story) : EF-IMP-11 / US-IMP-01, EF-LEC-04 / US-PRA-03, EF-PRA-03 / US-ECO-08, EF-ANA-09 / US-ANA-07, EF-RES-06 / US-RES-03, ENF-FIA-04 / US-ECO-02, ENF-QA-01 / US-ANA-04, ENF-ACC-02 / US-RES-02, ENF-CONF-01 / US-ANA-02 (et ENF-PERF-03 / US-QUA-04, couvert seulement par la plage « 01 à 06 »). | Rendre les deux sens identiques ; à terme, **générer** la matrice à partir des champs des stories par un script, contrôlé à chaque modification (R3, D10). |
| QA-61 | Mineur | 06 §1 et §2 | Couvertures déclarées « Complète » alors que la définition exige des stories **fermes** : EF-ANA-04 et EF-PRA-05 reposent pour le mode « boucle » sur US-PRA-09, proposée ; ENF-I18N-01 est « Complète si l'anglais est confirmé », alors qu'EF-CPT-05, pour la même raison, est « Partielle » ; EF-PRA-03 est « Complète en MIDI ; au micro selon POC-01 », catégorie qui n'existe pas (même situation qu'EF-ECO-04, classée « Partielle »). Le décompte « 6 exigences M partielles » passerait à 9 ou 10. | Reclasser en « Partielle » ou « Proposée » et mettre à jour §1 et §4. |
| QA-65 | Mineur | 01 §5.1 et §5.3 | « Précision » et « rappel » des notes à 99 % en MIDI sont acquis d'office (événements exacts) : ces lignes mesurent la chaîne de réception, pas une transcription. Le « taux de signalements » dépend de US-RES-08, proposée. La rétention calculée à partir de rapports exportés **volontairement** surestime l'usage (seuls les testeurs assidus exportent). | Renommer la métrique MIDI (« notes reçues / notes émises par le simulateur ») ; indiquer le repli si QO-28 écarte US-RES-08 ; définir le dénominateur de rétention = testeurs inscrits, les non-répondants comptés comme inactifs. |
| QA-66 | Mineur | 03 US-ECO-02 CA5 ; 05 §5 | La simulation (périphérique MIDI virtuel, rejeu) ne traverse pas la couche USB : adaptateur, mode USB du téléphone, flux de messages de maintien envoyé par les vrais claviers, canaux multiples, transposition réglée sur le piano, claviers de moins de 88 touches. | Le dire dans la note de US-ECO-02 et inscrire ces points au registre de bêta (QA-31) ; voir les cas limites MIDI en §4. |

---

## 4. Cas limites manquants

Rattachés à la story où les ajouter. Les cas marqués [supposé] reposent sur un comportement d'Android à confirmer par `architecte`.

### 4.1 Partitions mal formées ou exotiques

| # | Cas limite | Story | Critère à ajouter (proposition) |
|---|---|---|---|
| CL-01 | MusicXML « timewise » (et non « partwise ») | US-IMP-01, US-IMP-06 | Importé, ou refusé avec un message clair. |
| CL-02 | `.mxl` dont le manifeste désigne plusieurs fichiers, ou sans manifeste | US-IMP-06 | Le fichier principal désigné par le manifeste est lu ; à défaut, refus clair. |
| CL-03 | Encodage UTF-16, marque d'ordre des octets, caractères non latins dans le titre | US-IMP-01, US-BIB-02 | Import correct, titre affiché et recherchable. |
| CL-04 | Indication d'octave (8va, 8vb, 15ma) | US-ANA-01, US-VIS-01 | Les hauteurs attendues tiennent compte de l'octave ; test au corpus (sinon fausses erreurs d'octave en série). |
| CL-05 | Trémolo écrit (abréviation de notes répétées) | US-ANA-01, US-ANA-07 | Règle explicite : non évalué au MVP, ou notes développées. |
| CL-06 | Notes cachées, notes « cue » (petites notes d'indication non jouées) | US-ANA-01 | Non attendues. |
| CL-07 | Mesures à plusieurs pauses (multi-mesures), mesure vide, partition sans note | US-ANA-01, US-PRA-01 | Pas de blocage ; une séance sur un passage sans note n'est pas proposée. |
| CL-08 | Changement de clé ou de chiffrage au milieu d'une mesure ; chiffrage sans valeur (musique libre) | US-VIS-01, US-LEC-05 | Rendu correct ; métronome sans accentuation si chiffrage absent. |
| CL-09 | Voix multiples dans une portée, retour en arrière dans la mesure (`backup`) | US-ANA-01 | Nombre d'événements conforme à la référence manuelle. |
| CL-10 | MIDI de type 2, division en temps absolu (SMPTE), note désactivée codée par une vélocité 0, notes de même hauteur qui se chevauchent, plus de deux pistes, changements de tempo | US-IMP-02, US-IMP-06 | Chaque cas présent au corpus, avec résultat attendu. |
| CL-11 | Fichier `.xml` qui n'est pas du MusicXML, reçu par « Ouvrir avec » | US-IMP-03, US-IMP-06 | Refus clair, rien dans la bibliothèque. |
| CL-12 | Réimport d'un morceau de démonstration ; fichier identique à une démonstration | US-IMP-07 | Doublon signalé comme pour un morceau importé. |

### 4.2 Reprises et sauts

| # | Cas limite | Story | Critère à ajouter |
|---|---|---|---|
| CL-13 | Reprises imbriquées ; reprise jouée trois fois ; fins 1, 2 et 3 | US-LEC-07, US-ANA-01 | Ordre de jeu conforme à une référence manuelle, ou signalé « non pris en charge » au rapport d'import. |
| CL-14 | Barre de reprise de fin sans barre de début (retour au début) | US-LEC-07 | Retour à la mesure 1 (ou après l'anacrouse). |
| CL-15 | Plage sélectionnée qui commence dans une deuxième fin, ou qui contient la moitié d'une reprise | US-VIS-06, US-ANA-01 | Règle explicite ; le numéro de mesure affiché indique quel passage (1re ou 2e fois). |
| CL-16 | Passage choisi en mesures alors que la mesure 8 est jouée deux fois (reprise) : localisation d'une erreur | US-RES-04, US-RES-03 | L'erreur est rattachée au bon passage (1er ou 2e). |

### 4.3 Notes liées, ornements, accords arpégés, croisements de mains

| # | Cas limite | Story | Critère à ajouter |
|---|---|---|---|
| CL-17 | Liaison de prolongation à travers une barre de mesure, un saut de ligne ou de page, ou vers une fin de reprise | US-ANA-01, US-VIS-05 | Note non attendue comme nouvelle attaque, dans tous ces cas. |
| CL-18 | Accord dont une note est liée et les autres rejouées, en mode attente | US-PRA-02, US-ANA-06 | Validé quand les notes non liées sont jouées, la note liée étant tenue ou non. |
| CL-19 | Petites notes au tout début du morceau, avant le premier temps (et décompte) | US-PRA-05, US-ANA-07 | Décompte terminé avant la petite note ; pas d'erreur de rythme sur elle. |
| CL-20 | Arpège qui s'étend sur les deux portées | US-ANA-06 | Fenêtre élargie appliquée à l'ensemble de l'accord. |
| CL-21 | Accord non marqué arpégé mais roulé naturellement par un débutant | US-ANA-06 | En indulgent, pas d'« accord non synchronisé » jusqu'à la fenêtre du niveau ; chiffre au corpus. |
| CL-22 | Main droite écrite sur la portée du bas (croisement), en travail main seule | US-ANA-01, US-LEC-04 | En « main droite seule », les notes attendues sont celles de la main droite selon la règle retenue (QA-55), test au corpus. |
| CL-23 | L'utilisateur joue les deux mains alors que « main droite seule » est choisie | US-PRA-02, US-ANA-04 | Les notes de la main gauche ne sont pas comptées « en trop » (règle à décider), sinon fausses erreurs garanties au micro. |

### 4.4 Jeu hésitant et retours en arrière

| # | Cas limite | Story | Critère à ajouter |
|---|---|---|---|
| CL-24 | Retour au tout début du passage ; saut en avant d'une mesure (mesure oubliée) | US-ANA-03 | Recalage en ≤ 2 mesures ; mesure sautée = notes « manquées », sans décalage de la suite. |
| CL-25 | Même fausse note répétée plusieurs fois en mode attente | US-PRA-02, US-RES-06 | Une seule erreur par événement (QA-04) ; nombre d'essais conservé. |
| CL-26 | Jeu au tempo 25 % (notes très longues) puis 150 % | US-ANA-05 | Seuils d'hésitation et d'arrêt proportionnels au tempo ; pas d'arrêt détecté sur une ronde lente. |
| CL-27 | L'utilisateur continue de jouer après la fin du passage | US-PRA-07 | Notes au-delà de la fin ignorées, sans « en trop ». |

### 4.5 Bruit ambiant et piano désaccordé

| # | Cas limite | Story | Critère à ajouter |
|---|---|---|---|
| CL-28 | Son de notification, sonnerie, vibration du téléphone posé sur le pupitre pendant une séance au micro | US-ECO-11, US-PRA-06 | Aucune note « en trop » créée ; conseil « Ne pas déranger » proposé au début de séance. |
| CL-29 | Bruits mécaniques du piano (pédale, couvercle), page tournée, voix | US-ECO-07, US-QUA-01 | Sous-corpus dédié ; aucune note détectée sur ces bruits. |
| CL-30 | Accord « étiré » normal des pianos dans les extrêmes (aigus plus hauts, graves plus bas), unissons désaccordés, touche muette ou bloquée | US-ECO-09, US-ECO-07 | Pas de fausse erreur due à l'étirement ; une touche qui ne sonne pas est signalée comme problème de captation, pas comme note manquée répétée. |
| CL-31 | Environnement qui change après le calibrage (fenêtre ouverte, appareil déplacé, autre pièce) | US-ECO-05, US-PRA-01 | La préparation de séance signale un calibrage ancien ou fait sur un autre appareil ; recalibrage proposé si la captation se dégrade (US-RES-07). |
| CL-32 | Traitements audio du système (réduction de bruit, contrôle automatique du gain) qui coupent les sons tenus [supposé] | US-ECO-07, US-ANA-02 | La source audio utilisée est documentée et testée ; résultat au corpus avec et sans traitement. |

### 4.6 MIDI : déconnexion et matériel réel

| # | Cas limite | Story | Critère à ajouter |
|---|---|---|---|
| CL-33 | Débranchement alors que des notes sont enfoncées (aucune note désactivée reçue) | US-ECO-02, US-PRA-02 | Les notes restées « tenues » sont relâchées à la déconnexion ; elles ne valident pas l'accord suivant. |
| CL-34 | Débranchement pendant le calibrage de latence | US-ECO-06 | Calibrage annulé, ancienne valeur conservée. |
| CL-35 | Rebranchement d'un autre appareil que celui de la séance | US-ECO-02 | Reprise sur le nouvel appareil seulement après confirmation. |
| CL-36 | Transposition activée sur le piano numérique (toutes les notes décalées de n demi-tons) | US-ECO-02, US-ANA-04 | Décalage systématique détecté et signalé au lieu d'une série de notes fausses. |
| CL-37 | Clavier de 61 ou 76 touches : notes de la partition hors de l'étendue | US-ECO-02, US-PRA-01 | Avertissement en préparation de séance. |
| CL-38 | Clavier qui émet sur plusieurs canaux (clavier partagé), messages de maintien en continu, pédale de sustain à mi-course | US-ECO-02, US-QUA-03 | Le simulateur sait produire ces cas ; tous les canaux sont écoutés ; pédale à mi-course traitée selon une règle écrite. |

### 4.7 Interruptions et cycle de vie

| # | Cas limite | Story | Critère à ajouter |
|---|---|---|---|
| CL-39 | Rotation de l'écran pendant une séance au micro | US-VIS-07, US-ECO-04 | Aucune note jouée perdue pendant la rotation, ou orientation verrouillée pendant la séance (à décider). |
| CL-40 | Application en arrière-plan avec une séance suspendue, puis processus arrêté par le système | US-PRA-06 | Au retour, la séance est proposée à la reprise ou enregistrée « interrompue » ; rien n'est perdu. |
| CL-41 | Écran partagé, fenêtre flottante, perte du focus audio (autre application qui joue de la musique, alarme) | US-PRA-06, US-LEC-02 | Séance suspendue proprement ; lecture mise en pause à la perte du focus audio. |
| CL-42 | Mode économie d'énergie ou bridage thermique pendant une longue séance | US-QUA-04, US-ECO-07 | Latences mesurées aussi en économie d'énergie ; avertissement si la cible n'est plus tenue. |
| CL-43 | Changement d'heure, de fuseau, de langue pendant ou entre des séances | US-HIS-01, US-BIB-01, US-CPT-04 | Dates et tri « dernière pratique » cohérents ; libellés à jour. |

### 4.8 Écouteurs et sortie audio

| # | Cas limite | Story | Critère à ajouter |
|---|---|---|---|
| CL-44 | Écouteurs filaires avec micro ou casque Bluetooth branchés : la captation peut basculer sur leur micro [supposé] | US-ECO-11, US-ECO-07 | La captation reste sur le micro de l'appareil, ou l'application l'indique. |
| CL-45 | Sortie Bluetooth : 100 à 300 ms de retard sur le métronome, le décompte et l'autre main [supposé] | US-ECO-11, US-PRA-04, US-PRA-05 | Retard mesuré et compensé, ou métronome sonore désactivé en jeu continu avec une sortie Bluetooth. |
| CL-46 | Volume du téléphone à zéro ou mode silencieux | US-LEC-01, US-PRA-05 | Message « son coupé » quand la lecture est lancée. |

### 4.9 Stockage plein

| # | Cas limite | Story | Critère à ajouter |
|---|---|---|---|
| CL-47 | Stockage plein pendant un import (copie du fichier d'origine ou de la partition convertie) | US-IMP-07, US-IMP-06 | Import refusé avec un message ; aucune entrée à moitié créée, aucun fichier orphelin. |
| CL-48 | Stockage plein à l'enregistrement d'une séance, ou pendant une migration | US-HIS-01, US-HIS-03 | Message clair ; séances existantes intactes ; migration non lancée sans l'espace nécessaire. |

### 4.10 Permissions et micro indisponible

| # | Cas limite | Story | Critère à ajouter |
|---|---|---|---|
| CL-49 | Micro autorisé mais muet : bascule système « Accès au micro » coupée, micro occupé par une autre application (appel, assistant vocal) [supposé] | US-ECO-03, US-ECO-10, US-PRA-02 | Silence anormal détecté en ≤ 3 s [PO] au début de séance et expliqué ; l'application n'attend pas indéfiniment. |
| CL-50 | Autorisation « uniquement cette fois », autorisation retirée dans les réglages pendant une séance, réinitialisation automatique des autorisations des applications peu utilisées [supposé] | US-ECO-03, US-PRA-06 | Chaque cas mène à l'explication de US-ECO-03 CA3, sans plantage ni perte de séance. |

### 4.11 Appareil et réglages d'affichage

| # | Cas limite | Story | Critère à ajouter |
|---|---|---|---|
| CL-51 | Taille de police et taille d'affichage du système au maximum | US-VIS-07, US-CPT-04 | Aucun écran inutilisable (même si ENF-ACC-04 complet est en V1). |
| CL-52 | Mode sombre forcé par le système, inversion ou correction des couleurs | US-RES-04, US-RES-02 | Couleurs et symboles d'erreur toujours distincts ; partition lisible. |

L'appareil d'entrée de gamme ne peut pas être testé au MVP (un seul téléphone) : il est à inscrire au registre de bêta (QA-31). Seule la mémoire réduite est simulable sur l'émulateur (US-VIS-08 CA2), pas un processeur lent.

---

## 5. Synthèse

### 5.1 Anomalies du document par gravité

| Gravité | Nombre | Identifiants |
|---|:-:|---|
| Bloquant | 4 | QA-01, QA-02, QA-03, QA-04 |
| Majeur | 29 | QA-05 à QA-20, QA-26, QA-28 à QA-32, QA-34, QA-35, QA-37, QA-39, QA-40, QA-52, QA-53 |
| Mineur | 33 | QA-21 à QA-25, QA-27, QA-33, QA-36, QA-38, QA-41 à QA-51, QA-54 à QA-66 |
| Cas limites manquants | 52 | CL-01 à CL-52 (§4) |

Les identifiants ne suivent pas tout à fait l'ordre du document (regroupement par thème) ; le décompte ci-dessus a été recompté par script sur la colonne « Gravité » de ce fichier.

### 5.2 Risques résiduels, même après corrections

- **Un appareil, un piano** (R-15) : toutes les mesures micro du MVP vaudront pour le téléphone et le piano du porteur. Les corrections proposées rendent les cibles vérifiables, pas généralisables.
- **MIDI réel jamais essayé avant la bêta** (R-16) : la simulation ne traverse pas la couche USB (QA-66). Recommandation : afficher le MIDI USB comme « non vérifié sur matériel réel » dans les notes de version de J2.
- **Vérité terrain micro produite par le porteur seul** : annotations faites à la réécoute par la même personne qui a joué ; risque de biais. Un second annotateur humain, même ponctuel, réduirait ce risque.
- **Revue par des agents** : les validations perceptives (son, lisibilité, justesse d'un enregistrement) restent du ressort d'un humain (QA-11).

## Suite donnée (PRD v0.2, `product-owner`)

Bilan : **66 remarques intégrées (dont 2 en partie), 0 rejetée, 0 en attente (les 2 points en attente en v0.2 sont tranchés par les décisions du porteur du 2026-11-07, PRD v0.3)** ; **52 cas limites intégrés** dans les stories. Modifications par arbitrage ([08 §3](../08-questions-ouvertes.md)) : fenêtre d'arpège selon la formule de PED plutôt que « fenêtre × n » (A9) ; cible de lisibilité de 7,6 mm de portée (interligne 1,9 mm) proposée par UX plutôt que 2,0 mm (A7). La tolérance micro de 0,1 point [PO] reste à valider avec `expert-audio-mir`.

| Remarque | Statut | Suite donnée |
|---|---|---|
| QA-01 | Intégrée | US-QUA-02 CA3, D3 ; MOD-15 |
| QA-02 | Intégrée | Nouvelle US-QUA-05 ; US-ECO-07 CA2 ; US-ECO-04 CA4 et D8 limités aux variantes bêta et publique |
| QA-03 | Intégrée | US-QUA-04 CA1 en I0 ; D4 ; MOD-33 |
| QA-04 | Intégrée | US-PRA-02 CA4, US-ECO-07 CA2, US-QUA-02 CA2, 01 §5.1 |
| QA-05 | Intégrée | US-QUA-01 CA1 et CA2, 01 §5.1 (intervalles de confiance) |
| QA-06 | Intégrée | US-QUA-01 CA1 |
| QA-07 | Intégrée | US-QUA-01 CA4, US-QUA-02 CA1 et CA4, DI4 (arbitrage A12) |
| QA-08 | Intégrée | US-ANA-04 CA4, 01 §5.1 |
| QA-09 | Intégrée | US-QUA-01 CA1 (attaques annotées), US-ECO-08 CA1 |
| QA-10 | Intégrée (décision QO-43, 2026-11-07) | US-ECO-08 CA2 réécrit |
| QA-11 | Intégrée | US-LEC-01 CA4 et CA5, US-QUA-01 CA3, 07 (introduction et §4) |
| QA-12 | Intégrée | Incrément par CA (03 §1.1), R7, DI1, liste de I3 corrigée |
| QA-13 | Intégrée | Étiquettes de vérification sur tous les CA (03 §1.2) |
| QA-14 | Intégrée | R14 |
| QA-15 | Intégrée | 07 §2.1 (vérifications reportées), registre 05 §5 |
| QA-16 | Intégrée | DM6, DM7, DI6, échelle de gravité 07 §3 |
| QA-17 | Intégrée | D5, D8 |
| QA-18 | Intégrée | US-CPT-06 CA6 (arbitrage A11) |
| QA-19 | Intégrée | US-IMP-01 CA2 et CA3 |
| QA-20 | Intégrée | Fichiers P10 et P30 (US-IMP-01 CA6, US-VIS-08, US-QUA-01) |
| QA-21 | Intégrée | US-IMP-01 CA4 |
| QA-22 | Intégrée | US-IMP-03 CA4 (2 appuis au plus) |
| QA-23 | Intégrée | US-IMP-03 CA1 |
| QA-24 | Intégrée | US-IMP-06 CA3 |
| QA-25 | Intégrée | US-BIB-01 CA6 |
| QA-26 | Intégrée en partie | US-VIS-03 CA1 : critère objectif retenu, avec la valeur de UX (7,6 mm, A7) ; lecteurs autres que le porteur (CA3) |
| QA-27 | Intégrée | US-VIS-01 CA4 |
| QA-28 | Intégrée | US-CNF-01 CA2 et CA3, US-CPT-05 CA4 |
| QA-29 | Intégrée | US-PRA-08 CA2, US-ECO-04 CA2 (option a, arbitrage A6) |
| QA-30 | Intégrée | Critères de J2 (05 §3), DM4 |
| QA-31 | Intégrée | Registre des vérifications reportées (05 §5) |
| QA-32 | Intégrée (décision QO-42, 2026-11-07) | Branche ajoutée au plan (05 §2) |
| QA-33 | Intégrée | 05, I5 (capture des séances, US-QUA-05) |
| QA-34 | Intégrée | US-RES-07 CA2 et dépendance |
| QA-35 | Intégrée | US-ANA-05 CA2 |
| QA-36 | Intégrée | US-ANA-03 CA3 et CA4 |
| QA-37 | Intégrée | US-PRA-02 (scénarios Gherkin) |
| QA-38 | Intégrée | US-RES-03 CA6 |
| QA-39 | Intégrée | US-RES-04 CA2 |
| QA-40 | Intégrée | US-RES-04 CA5 |
| QA-41 | Intégrée en partie | US-ANA-06 CA3 (main déduite de la note attendue) ; formule de fenêtre de PED retenue (A9) |
| QA-42 | Intégrée | US-ANA-07 CA3 et CA4 |
| QA-43 | Intégrée | US-RES-03 CA3 et CA8 |
| QA-44 | Intégrée | US-RES-06 CA2 |
| QA-45 | Intégrée | US-RES-04 CA3 |
| QA-46 | Intégrée | US-ECO-06 CA2 |
| QA-47 | Intégrée | US-ECO-05 CA3 et CA4 |
| QA-48 | Intégrée | US-ECO-09 CA1 |
| QA-49 | Intégrée | US-ECO-07 CA6 et CA7 |
| QA-50 | Intégrée | US-ECO-10 CA1 |
| QA-51 | Intégrée | US-HIS-02 CA3 |
| QA-52 | Intégrée | US-HIS-01 CA2 |
| QA-53 | Intégrée | US-HIS-03 CA2 et CA3 |
| QA-54 | Intégrée | US-PRA-06 CA1 et CA3 |
| QA-55 | Intégrée | US-LEC-04 CA3 (à valider par PED) |
| QA-56 | Intégrée | US-VIS-06 CA3, US-LEC-06 CA1 |
| QA-57 | Intégrée | Variantes de construction (03 §1.3), US-QUA-03 CA4, US-QUA-05 CA2 |
| QA-58 | Intégrée | US-CNF-03 CA5 |
| QA-59 | Intégrée | US-CNF-02 CA5 (US-CNF-02 en I0) |
| QA-60 | Intégrée | Matrice générée depuis les stories, réciprocité contrôlée par script |
| QA-61 | Intégrée | Couvertures reclassées (06 §1 et §4 : 10 exigences M partielles) |
| QA-62 | Intégrée | US-CPT-05 CA1 et CA3 |
| QA-63 | Intégrée | US-VIS-07 CA1 |
| QA-64 | Intégrée | US-VIS-08 CA2 et CA3 |
| QA-65 | Intégrée | 01 §5.1 et §5.3 |
| QA-66 | Intégrée | US-ECO-02 CA7, registre 05 §5 |

### Cas limites

| Cas | Statut | Story et critère |
|---|---|---|
| CL-01 | Intégré | US-IMP-01 CA1 |
| CL-02 | Intégré | US-IMP-06 CA4 |
| CL-03 | Intégré | US-IMP-01 CA7, US-BIB-02 CA1 |
| CL-04 | Intégré | US-ANA-01 CA5, US-VIS-01 CA1 |
| CL-05 | Intégré | US-ANA-01 CA5 |
| CL-06 | Intégré | US-ANA-01 CA5 |
| CL-07 | Intégré | US-ANA-01 CA7, US-PRA-01 CA6 |
| CL-08 | Intégré | US-VIS-01 CA1, US-LEC-05 CA2 |
| CL-09 | Intégré | US-ANA-01 CA6 |
| CL-10 | Intégré | US-IMP-02 CA1 |
| CL-11 | Intégré | US-IMP-03 CA5 |
| CL-12 | Intégré | US-IMP-07 CA4 |
| CL-13 | Intégré | US-LEC-07 CA5 |
| CL-14 | Intégré | US-LEC-07 CA1 |
| CL-15 | Intégré | US-VIS-06 CA5 |
| CL-16 | Intégré | US-RES-03 CA5, US-RES-04 CA6 |
| CL-17 | Intégré | US-ANA-01 CA3, US-VIS-05 CA5 |
| CL-18 | Intégré | US-ANA-06 CA2 |
| CL-19 | Intégré | US-ANA-07 CA4, US-PRA-05 CA5 |
| CL-20 | Intégré | US-ANA-06 CA4 |
| CL-21 | Intégré | US-ANA-06 CA5 |
| CL-22 | Intégré | US-LEC-04 CA3 |
| CL-23 | Intégré | US-ANA-04 CA3 (arbitrage A15, à valider par PED) |
| CL-24 | Intégré | US-ANA-03 CA2 et CA3 |
| CL-25 | Intégré | US-PRA-02 CA4 |
| CL-26 | Intégré | US-ANA-05 CA3 |
| CL-27 | Intégré | US-ANA-03 CA6, US-PRA-07 CA4 |
| CL-28 | Intégré | US-ECO-11 CA5 |
| CL-29 | Intégré | US-ECO-07 CA8 |
| CL-30 | Intégré | US-ECO-09 CA2 et CA4 |
| CL-31 | Intégré | US-ECO-05 CA5, US-PRA-01 CA2 |
| CL-32 | Intégré | US-ECO-07 CA10 |
| CL-33 | Intégré | US-ECO-02 CA3 |
| CL-34 | Intégré | US-ECO-06 CA5 |
| CL-35 | Intégré | US-ECO-02 CA3 |
| CL-36 | Intégré | US-ECO-02 CA5 |
| CL-37 | Intégré | US-ECO-02 CA6 |
| CL-38 | Intégré | US-ECO-02 CA1, US-QUA-03 CA2 |
| CL-39 | Intégré | US-VIS-07 CA3 (orientation verrouillée, arbitrage A14) |
| CL-40 | Intégré | US-PRA-06 CA3 |
| CL-41 | Intégré | US-PRA-06 CA1, US-LEC-02 CA4 |
| CL-42 | Intégré | US-QUA-04 CA3 |
| CL-43 | Intégré | US-HIS-01 CA4, US-BIB-01 CA7, US-CPT-04 CA9 |
| CL-44 | Intégré | US-ECO-07 CA9 |
| CL-45 | Intégré | US-ECO-11 CA3 |
| CL-46 | Intégré | US-LEC-01 CA6 |
| CL-47 | Intégré | US-IMP-06 CA5 |
| CL-48 | Intégré | US-HIS-01 CA3, US-HIS-03 CA4 |
| CL-49 | Intégré | US-ECO-04 CA5 |
| CL-50 | Intégré | US-ECO-03 CA5 et CA6 |
| CL-51 | Intégré | US-VIS-07 CA4 |
| CL-52 | Intégré | US-RES-04 CA3 |
