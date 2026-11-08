# Revue du PRD v0.1 — `designer-ux`

| Élément | Valeur |
|---|---|
| Relecteur | `designer-ux` |
| Date | 2026-11-07 |
| Documents relus | PRD v0.1 (README, 01 à 08) ; EDB v0.4, surtout 02, 03, 05 (EF citées), 06 §4.1 et §7, 08 §7, 10 |
| Angle | Ergonomie, parcours, lisibilité au pupitre, accessibilité, textes |
| Contexte matériel retenu | Un téléphone Android 14 (pas de tablette), l'émulateur, un piano droit ; Android 13 minimum ; Jetpack Compose et Material 3 probables |

Les incohérences de l'EDB déjà listées dans le [README du PRD](../README.md#incohérences-relevées-dans-ledb) ne sont pas reprises. Quand une remarque s'appuie sur l'une d'elles (E12, E14, détection d'arrêt), elle porte seulement sur ce qu'il faut spécifier pour le MVP.

---

## 1. Verdict global

**Prêt avec corrections.**

Le PRD est solide du point de vue produit : périmètre MVP clair, stories tracées, règle « fiabilité d'abord » appliquée partout, cas d'erreur de l'import bien traités, confidentialité du micro prise au sérieux. Les choix « un seul style », « thème clair seul » et « pas de "travailler ce passage" » sont cohérents avec le budget.

Trois points demandent des corrections avant de démarrer les incréments concernés.

1. **L'affichage sur téléphone n'est pas démontré** (2 remarques bloquantes, avant I1). Sur le téléphone de référence en paysage, une grande portée lisible à 60 cm laisse environ **un seul système** à l'écran. Le mode page recommandé pour QO-27 ne peut alors pas montrer la ligne suivante avant la fin de la ligne en cours (US-VIS-05 CA1). Par ailleurs, « lisible à 60 cm » n'est pas mesurable. Je propose une cible physique, un protocole de test, et un test comparatif qui tranche QO-27. Ma recommandation est une **bande horizontale qui défile par paliers, en paysage** (§3.1).
2. **Le principe « mains sur le clavier » n'est tenu qu'à moitié** (avant I2 et I3). Il porte sur le démarrage du jeu continu, la reprise après un arrêt, la taille et le rôle des commandes de séance, les sons de l'application captés par le micro, la latence des écouteurs Bluetooth et le métronome visuel.
3. **Plusieurs écrans du MVP ne sont pas encore spécifiables**. Il manque une correspondance entre stories et écrans, des écrans absents de l'inventaire, les états vides, d'erreur et de chargement, la navigation du MVP, et un code des résultats qui entre en conflit avec la notation musicale.

Aucune remarque ne remet en cause le découpage en epics, les priorités ou le plan de release. Toutes les corrections portent sur des critères d'acceptation, des notes ou des spécifications d'écran.

| Gravité | Nombre |
|---|:-:|
| Bloquant | 2 |
| Majeur | 22 |
| Mineur | 10 |
| **Total** | **34** |

---

## 2. Remarques

Gravité :
- **bloquant** : à corriger avant de valider le PRD ou de démarrer l'incrément concerné ;
- **majeur** : à corriger avant que la story concernée soit « prête » (DoR) ;
- **mineur** : amélioration recommandée.

### 2.1 Affichage et lisibilité

| ID | Gravité | Référence | Constat | Correction proposée |
|---|---|---|---|---|
| UX-01 | **Bloquant** | 08 QO-27 ; 03 US-VIS-02, US-VIS-05 CA1, US-VIS-03 CA4 | Sur un téléphone de 6,1 à 6,7 pouces en paysage, une grande portée lisible à 60 cm (portée ≈ 7,6 mm) donne **un système complet par écran**, avec au plus l'amorce du suivant (§3.1). En mode page, la ligne suivante ne peut pas être visible quand le curseur atteint la fin de la ligne, sauf à réduire la portée à environ 5,3 mm, ce qui la rend illisible à 60 cm. En portrait, le mode page affiche 3 systèmes de 1 à 1,5 mesure : retour à la ligne à chaque mesure et clés répétées sur environ 30 % de la largeur. La recommandation du PO n'est donc pas démontrée sur le seul appareil de test. | Trancher QO-27 **avant I1** par le test comparatif du §3.1. Ma recommandation : **défilement horizontal par paliers, en paysage**, comme mode unique du MVP ; le mode page arrive en V1 (US-CNT-03), pour la tablette et le portrait. Reformuler US-VIS-05 CA1 indépendamment du mode : « à tout instant, au moins une mesure entière, et au moins 4 temps, au-delà du curseur sont visibles ». CA2 (tourne en deux temps) ne s'applique que si le mode page est retenu. Ajouter à POC-04 le critère « rendu sur un seul système de longueur illimitée ». |
| UX-02 | **Bloquant** | 03 US-VIS-03 CA3, CA4 et Notes ; EDB 08 ENF-ACC-07 | « Lisible » n'est pas mesurable : ni taille physique, ni matériel de test, ni seuil de réussite. Le zoom par défaut ne peut donc être ni implémenté ni vérifié automatiquement. De plus, le dp n'est pas une unité physique exacte : selon les appareils, l'écart peut atteindre environ 10 %. CA4 est marqué [HYP] alors que c'est une proposition du PO ([PO]). | Exprimer la cible en **hauteur physique de portée**, déduite de l'angle de vision (§3.2) : ≥ 7,6 mm à 60 cm sur téléphone et ≥ 8,9 mm à 70 cm sur tablette, calculées à partir de la densité réelle de l'écran. Prévoir un contrôle automatique de la taille et le protocole humain du §3.2. Remplacer [HYP] par [PO]. |
| UX-03 | Majeur | 03 US-VIS-01 CA1 (doigtés) ; US-VIS-03 | Les doigtés et les numéros de mesure sont gravés à environ 0,6 à 1 interligne. À 60 cm, cela représente 9 à 11 minutes d'arc, sous le minimum de lecture d'un texte (16 minutes d'arc), même quand la portée est lisible. Or les débutants (Claire, Lucas) s'appuient sur les doigtés. | Règle de rendu : doigtés et numéros de mesure agrandis, environ 1,3 fois la taille de gravure par défaut (à ajuster par le test). Le protocole du §3.2 inclut la lecture des doigtés (hauteur des chiffres ≥ 2,8 mm à 60 cm). Validation par `pedagogue-piano`. |
| UX-04 | Majeur | 03 US-VIS-05, US-LEC-07, US-ANA-01 | La lecture en avance ignore les reprises et les première et deuxième fins. À une barre de reprise, la musique suivante se trouve plus haut dans la partition, en mode page parfois sur une autre page. Rien ne dit ce qui est affiché. | Ajouter un CA : « à l'approche d'une barre de reprise ou d'une 1re fin, la musique jouée ensuite est visible avant la fin de la mesure ». Avec la bande horizontale, dérouler la partition **dans l'ordre de jeu** : numéros de mesure d'origine conservés, mention « 2e fois », seule la fin du passage en cours affichée. Validation par `pedagogue-piano`. |
| UX-05 | Majeur | 03 US-PRA-06 CA4 ; 05 I1 (« utile comme pupitre ») | L'écran ne reste allumé que pendant une séance active. Pendant l'écoute (E11), le jeu sans séance avec la partition au pupitre ou le calibrage (E04), le téléphone se met en veille après le délai du système, souvent 30 s. L'usage « pupitre » d'I1 ne fonctionne donc pas. | Garder l'écran allumé tant qu'une partition est affichée au premier plan (E11, E13, partition annotée) et pendant le calibrage ; rendre la main au système partout ailleurs. |
| UX-06 | Mineur | 03 US-VIS-03 CA1, US-VIS-07 | Le zoom n'est mémorisé que par morceau. Comme la mise en page est recalculée, un zoom réglé en paysage ne convient pas en portrait. Par ailleurs, pincer l'écran est peu commode quand le téléphone est posé sur le pupitre. | Mémoriser le zoom par morceau **et** par orientation. Ajouter une commande rapide « Taille de la partition » (Normale, Grande, Très grande) dans la barre de E11. La taille par défaut ne descend jamais sous la cible de lisibilité ; le pincement reste libre. |
| UX-07 | Mineur | EDB 10 §4 (E11, E13) ; 03 US-VIS-07 | Les maquettes de E11 et E13 sont dessinées pour une tablette en paysage. Sur un téléphone en paysage, les barres système et l'encoche de la caméra prennent 10 à 15 % de la hauteur ou de la largeur, et aucune règle n'en tient compte. | Plein écran (mode immersif) en lecture et en pratique, avec les barres système rappelées par un balayage. Gérer l'affichage bord à bord (obligatoire avec une cible API 35) et laisser l'encoche hors de la partition. Masquer les commandes 3 s après le début de la lecture (principe 1 de [10 §1](../../EDB/10-ecrans-et-ergonomie.md)) ; un appui les fait réapparaître. |

### 2.2 Mains sur le clavier pendant le jeu

| ID | Gravité | Référence | Constat | Correction proposée |
|---|---|---|---|---|
| UX-08 | Majeur | 03 US-PRA-05, US-PRA-01 CA5 ; EDB 10 §1 principe 2 | En jeu continu, le décompte part dès l'appui sur « Commencer ». La main doit revenir au clavier pendant une mesure de décompte, soit 2 à 3 s à 80 %, avec un téléphone posé à 60 cm. | Par défaut : « Jouez une note pour lancer le décompte ». N'importe quelle note convient, en MIDI comme au micro, et elle n'est pas analysée. En option : décompte après l'appui, précédé d'un délai de 3 s. Le mode attente ne change pas : le curseur attend. Faisabilité au micro à confirmer par `expert-audio-mir`. |
| UX-09 | Majeur | 03 US-PRA-08 CA2, US-PRA-06 CA2 | Après un arrêt détecté par le silence, ou au retour après une interruption, les trois choix exigent de toucher l'écran. Or « reprendre » est le choix le plus fréquent. | **Reprendre en jouant** : en mode attente, jouer la note attendue reprend la séance ; en jeu continu, jouer une note relance le décompte au début de la mesure en cours. Les autres choix s'affichent en grand (cibles ≥ 64 dp, texte ≥ 28 sp). Du point de vue UX, j'appuie l'option 1 de QO-23 pour US-PRA-08. |
| UX-10 | Majeur | 03 US-PRA-07 CA2, US-LEC-02 CA2, US-VIS-06 CA3 ; 07 D6 | (a) 48 dp est un minimum pour un téléphone tenu en main. Atteindre le pupitre depuis le clavier est imprécis, d'où des cibles trop petites pour les commandes de séance. (b) En séance, l'effet d'un appui sur la partition n'est pas défini : US-LEC-02 CA2 déplace le point de départ, ce qui serait dangereux sur un appui involontaire. (c) La sélection d'un passage en glissant le doigt entre en conflit avec le défilement et le pincement. | (a) Commandes de séance ≥ 64 dp, placées dans les coins, hors de la partition, espacées d'au moins 16 dp ; mettre à jour 07 D6. (b) En séance, les appuis sur la partition sont ignorés ; seules les commandes réagissent. (c) La sélection commence par un appui long, se règle avec des poignées, et peut aussi se saisir dans E12 (« De la mesure … à … »). |
| UX-11 | Majeur | 03 US-ECO-11 CA1, US-PRA-05 CA2, US-LEC-05 CA1 | Le métronome et le décompte visuels sont le réglage par défaut au micro, mais ils ne sont pas spécifiés. Une zone qui clignote à 180 battements par minute (150 % de 120) ou avec subdivisions dépasse 3 éclairs par seconde, ce qui présente un risque de crise d'épilepsie (WCAG 2.3.1, niveau A). Un décompte affiché par-dessus les premières mesures masque ce qu'il faut lire pendant le décompte. | Une pulsation **sans éclair** : légère variation d'opacité ou de taille d'un petit élément (le curseur et un compteur de temps dans la barre du haut), jamais par-dessus la partition. Pas plus de 3 changements par seconde : les subdivisions ne sont pas affichées au-delà. Respecter le réglage Android « Supprimer les animations ». Chiffres du décompte dans la barre du haut, ≥ 32 sp. |
| UX-12 | Majeur | 03 US-CPT-04 CA4, US-PRA-02 (« son si activé »), US-ECO-11 | US-ECO-11 ne couvre pas le **son d'erreur** du mode attente. Au micro, ce son est capté : il peut être pris pour une note jouée ou masquer la correction. | Son d'erreur désactivé par défaut au micro, et soumis à la même règle que le métronome (avertissement « écouteurs »). Le signal visuel reste toujours présent. |
| UX-13 | Majeur | 03 US-ECO-11 CA2, US-PRA-03, US-LEC-05 ; EDB 08 ENF-PERF-03 | L'application recommande des écouteurs. Avec des écouteurs Bluetooth, la latence de sortie est souvent de 150 à 300 ms : le métronome, le décompte et l'autre main arrivent en retard sur le curseur, et l'utilisateur qui suit le clic est jugé en retard. | Texte : « écouteurs **filaires** de préférence ». Détecter une sortie audio Bluetooth et afficher un avertissement. Demander à `architecte` de compenser la latence de sortie dans la synchronisation du son et du curseur ; si c'est impossible, ouvrir une question. |

### 2.3 Premier lancement (E01 à E04)

| ID | Gravité | Référence | Constat | Correction proposée |
|---|---|---|---|---|
| UX-14 | Majeur | 03 US-CPT-06 CA1 à CA3, US-ECO-05 CA1 et CA2, US-ECO-06 ; écrans E03, E04 | Le budget de 5 minutes est tendu. Le calibrage complet (niveau, latence, diapason) prend près d'une minute. Les « premières mesures » ne sont pas bornées. Rien ne dit quand poser le téléphone sur le pupitre. Un calibrage qui demande des notes précises est difficile pour un débutant. Estimation au §3.5 : 3 min 20 à 4 min 15, à condition d'appliquer les corrections ci-contre. | Pendant le premier lancement, limiter le calibrage à ce dont le mode attente a besoin : niveau d'entrée, et diapason si US-ECO-09 est retenue. Consigne : « Jouez quelques notes au milieu du clavier, comme vous voulez ». Mesurer la latence avant la première séance en jeu continu au micro, et ne pas faire le test de latence MIDI au premier lancement : le mode attente ne juge pas le rythme. Ajouter une étape explicite « Posez le téléphone sur le pupitre », illustrée. E04 avance seul dès que les notes sont détectées et se lit à 60 cm. Premier passage : 4 mesures, 16 notes au plus, main droite, morceau à une main. |
| UX-15 | Majeur | 03 US-CPT-02, US-ECO-01, US-ECO-02 ; EDB E02, E03 | E03 (« configuration de la captation ») fait en partie double emploi avec E02, puisque la source découle désormais du type de piano. Plusieurs embranchements ne sont pas décrits : USB choisi mais aucun appareil branché ou pas d'adaptateur ; micro refusé ; « je ne sais pas » ; premier lancement par « Ouvrir avec » ; écran d'arrivée après le premier compte-rendu. | Enchaînement du §3.5. Branche MIDI : écran « Branchez votre piano » avec détection en direct, mention de l'adaptateur USB OTG et repli « Utiliser le micro pour l'instant ». Branche micro : explication, demande du système, mise en place, calibrage. En cas de refus, terminer par « Découvrir en écoutant » (lecture d'une démonstration) ; le critère des 5 minutes est alors sans objet. Fichier ouvert avant le premier lancement : aperçu d'import d'abord, accueil raccourci ensuite. Après le premier compte-rendu : bibliothèque, avec une carte « Importer ma partition ». |
| UX-16 | Mineur | 03 US-CPT-02 CA1 | Sans description, les niveaux « débutant, intermédiaire, avancé » sont compris de façons différentes. Les types de piano gagneraient à être illustrés. | Une phrase par niveau, rédigée avec `pedagogue-piano` (ex. « Je lis les notes lentement »). Illustrations : piano droit ou à queue, piano numérique. Ajouter la réponse « Je ne sais pas », qui mène au micro. |
| UX-17 | Mineur | 03 US-CPT-06 CA3 | Le protocole du panel de 4 personnes ne précise ni les profils, ni l'observation, ni la compréhension ; seul le temps est mesuré. Le parcours MIDI ne peut pas être mesuré. | Panel : au moins un vrai débutant, une personne de plus de 45 ans et une personne qui utilise un téléphone qui n'est pas le sien. Observation sans aide, avec relevé des points de blocage. Réussite : ≤ 5 min **et** la personne sait dire ce que lui apprend le compte-rendu. Parcours MIDI : à vérifier en bêta. |

### 2.4 Autorisation du micro

| ID | Gravité | Référence | Constat | Correction proposée |
|---|---|---|---|---|
| UX-18 | Majeur | 03 US-ECO-03 CA2 à CA4 | La demande du système Android parle d'autoriser l'application à « **enregistrer** » de l'audio (libellé exact à relever sur le téléphone). Si on ne l'annonce pas, ce mot contredit notre « aucun enregistrement » et éveille la méfiance (Nadia). Le choix « Uniquement cette fois-ci » fait revenir la demande à la séance suivante. Depuis Android 11, après **deux refus**, le système n'affiche plus la demande ; or CA4 parle de « refus définitif » sans cette règle. | Textes du §3.4, en français et en anglais : l'écran qui précède la demande annonce le mot employé par Android et conseille l'option « Pendant l'utilisation de l'appli » (libellé à vérifier). Reformuler CA4 : « après deux refus, ou si le système n'affiche plus la demande, l'application guide vers les réglages du système ». Textes à valider par `juriste-conformite`. Ajouter un CA (US-CNF-01 ou US-ECO-03) : aucune autre autorisation n'est demandée au MVP (notifications, stockage). |

### 2.5 Préparation de séance, compte-rendu et restitution

| ID | Gravité | Référence | Constat | Correction proposée |
|---|---|---|---|---|
| UX-19 | Majeur | 03 US-PRA-01 ; EDB E12 | Le contenu MVP de E12 (§3.6) compte une dizaine de réglages, plus des états et des avertissements. Sur un téléphone en paysage (environ 390 dp de haut), la feuille défile et « Commencer » peut sortir de l'écran. Le passage ne se choisit qu'à partir d'une sélection faite avant. Le tempo est proposé en mode attente, où il n'a pas d'effet. | Affichage progressif. Toujours visibles : passage (avec champs « de… à… »), mains, mode, et tempo en jeu continu seulement. Sous « Plus d'options » : retour en direct, métronome, décompte, autre main. « Commencer » reste fixé en bas. Une option indisponible est grisée, avec sa raison. Mention « expérimental » selon QO-29. Retirer « Boucle progressive » et « Suivi » ; ajouter « Répéter » si US-PRA-09 est retenue. Afficher le niveau d'exigence et l'état de la captation. |
| UX-20 | Majeur | 03 US-RES-01, US-RES-02, US-PRA-01 CA1 ; EDB E12 | Le retour en direct (aucun, discret, complet) est proposé quel que soit le mode. En mode attente, l'affichage de l'erreur est le cœur de la fonction (US-RES-02) : avec « aucun », le mode attente devient inutilisable. | Le retour en direct ne s'applique qu'au jeu continu. En mode attente, l'erreur est toujours affichée ; seule l'indication textuelle est en option. E12 masque « Retour » en mode attente. |
| UX-21 | Majeur | 03 US-RES-03, US-PRA-02 CA4, US-PRA-07 CA3, US-RES-07 ; EDB E14 | Les variantes de E14 ne sont pas spécifiées. En mode attente, US-PRA-02 CA4 demande « les notes le plus souvent ratées », absentes de l'ordre fixé par US-RES-03 CA2. Ne sont pas décrits non plus : séance interrompue ou partielle, aucune note jouée, séance non fiable (comment le score est-il « présenté comme non fiable » ?), chargement de l'analyse (US-ECO-08 CA2 admet 10 s par minute jouée en jeu continu au micro). Enfin, E14 se lit depuis le banc. | Spécifier E14 selon le §3.6 : blocs et variantes. État « Analyse en cours… » avec progression au-delà de 2 s. Aucune note jouée : pas de score, message, séance non comptée. Séance non fiable : bandeau en tête, score « indicatif » en gris, exclu du meilleur score. Haut de l'écran lisible à 60 cm (score ≥ 45 sp, premier passage ≥ 24 sp). Aligner US-PRA-02 CA4 et US-RES-03 CA2. |
| UX-22 | Majeur | 03 US-RES-03 CA3 et CA4 ; 08 QO-24 a ; 01 §5.3 et §5.4 | Sans aucune action depuis un passage, travailler les mesures signalées demande au moins 6 gestes : fermer, ouvrir la partition, trouver les mesures, sélectionner, Pratiquer, Commencer. Le critère J3 « compte-rendu actionnable » (cible ≥ 80 %) est donc fragile. | Sans réaliser EF-RES-09 : chaque passage devient un lien « Voir sur la partition », qui ouvre la partition annotée à cet endroit avec la plage présélectionnée. « Pratiquer » ouvre alors E12 prérempli avec la plage, sans tempo ni main suggérés. Coût faible : US-VIS-06 et US-PRA-01 existent déjà. |
| UX-23 | Mineur | EDB E13 ; 03 US-RES-04 CA5 | La maquette E13 montre le clavier virtuel (V1, EF-VIS-07) et des « ? » pendant le jeu, alors que les écarts incertains ne sont visibles que dans la partition annotée en mode détaillé. | E13 au MVP : ni clavier, ni « ? ». Barre du haut : « Micro actif », numéro de mesure, Pause, Terminer. La légende est accessible depuis la partition annotée. |
| UX-24 | Mineur | 03 US-RES-04 CA5, US-RES-05 CA2 | Le « mode détaillé » n'est défini nulle part. | Interrupteur « Afficher les détections incertaines » dans la partition annotée, désactivé par défaut. |
| UX-25 | Mineur | 03 US-RES-05 CA1 | Un écart exprimé en millisecondes et en fraction de temps est du jargon pour un débutant. | Une phrase d'abord (« un peu en avance, environ un huitième de temps »), la valeur en ms sur une seconde ligne. |

### 2.6 Palette et symboles des résultats

| ID | Gravité | Référence | Constat | Correction proposée |
|---|---|---|---|---|
| UX-26 | Majeur | EDB 10 §5, repris par 03 US-RES-02 CA1 et US-RES-04 CA1 et CA2 ; ENF-ACC-02 | Plusieurs formes proposées ont déjà un sens en notation musicale : une tête de note **évidée** (« manquée ») ou en **contour** (« fausse ») se lit comme une blanche ou une ronde ; un **losange** (« en trop ») se lit comme un harmonique. Posés sur les têtes de note, les symboles masquent les altérations. Leur taille à 60 cm n'est pas fixée. | Code du §3.7 : ne jamais modifier la forme d'une tête de note. Les marques sont des **pastilles hors de la tête** (au-dessus de la portée pour la main droite, au-dessous pour la main gauche) ou des halos, d'au moins 1,5 interligne. Validation par `pedagogue-piano`. |
| UX-27 | Majeur | EDB 10 §5 ; 03 US-RES-04 CA3 ; ENF-ACC-02 | La palette indicative (7 couleurs) contient des confusions connues : vert et rouge, bleu et violet pour les daltonismes rouge-vert (protanopie, deutéranopie), rouge et orange. Avec des teintes courantes, le gris (≈ #9E9E9E) et l'orange (≈ #F57C00) n'ont qu'un contraste d'environ 2,7:1 sur blanc, le gris clair d'environ 1,9:1, sous le minimum de 3:1 (WCAG 1.4.11) : voir le pré-contrôle du §3.7. Les couleurs dynamiques de Material You peuvent aussi produire une couleur principale proche d'une couleur de résultat ; le violet par défaut de Material 3, utilisé pour le curseur, se confond avec le bleu du rythme en protanopie. | Trois familles de couleur (hauteur en vermillon, rythme en bleu, absence en gris) ; un symbole précise le type d'erreur. Notes justes sans marque par défaut. Valeurs du §3.7, toutes ≥ 3,6:1. Pas de couleurs dynamiques dans la zone de partition. Curseur en bande translucide, d'une teinte hors de la palette des résultats. Validation sur le téléphone avec la simulation de daltonisme d'Android et, si possible, par un testeur daltonien. Couleurs définies en jetons (tokens) dès le MVP, pour dériver le thème sombre en V1. |

### 2.7 Ton et contenus

| ID | Gravité | Référence | Constat | Correction proposée |
|---|---|---|---|---|
| UX-28 | Majeur | 08 QO-24 g ; 03 US-RES-03 CA5, US-CPT-03 CA2 | « Sobre et encourageant » n'est pas défini de façon opérationnelle : CA5 n'est pas testable. De plus, le principal levier d'encouragement, la comparaison avec les séances précédentes (EF-RES-08), est absent du MVP. | Adopter les règles du §3.3 et les utiliser comme liste de contrôle à la relecture (D9). Leviers disponibles au MVP : « passage sans erreur », « meilleur score sur ce morceau » (le meilleur score existe déjà), « justes du premier coup » en mode attente. Formulations impersonnelles par défaut, vouvoiement quand il faut s'adresser à l'utilisateur. |
| UX-29 | Mineur | 03 (ensemble) ; EDB 14 | Aucune liste des termes affichés (modes, niveaux, résultats, « rejouée », « incertaine ») n'existe en français et en anglais. Le vocabulaire risque de différer entre la légende, le détail, le compte-rendu et le rapport d'import. | Lexique des termes d'interface dans `docs/UX/`, en français et en anglais, validé par `pedagogue-piano`. |

### 2.8 Navigation, parcours et spécifiabilité

| ID | Gravité | Référence | Constat | Correction proposée |
|---|---|---|---|---|
| UX-30 | Majeur | EDB 10 §2 et maquette E05 ; 01 §6.2 | La navigation du MVP n'est pas définie. La structure à trois onglets inclut « Progression » (V1), et la maquette E05 montre « Progrès ». Material 3 recommande 3 à 5 destinations dans une barre de navigation ; une barre à 2 entrées n'est pas conseillée. | Au MVP : pas de barre de navigation. La bibliothèque est l'écran d'accueil ; les réglages s'ouvrent par une icône dans la barre du haut. La barre de navigation arrive en V1 avec « Progression ». À écrire dans le PRD. |
| UX-31 | Mineur | 03 US-BIB-01, US-BIB-07 ; EDB 10 §2 | Le chemin quotidien Bibliothèque, Fiche, Partition, Pratiquer, Commencer demande 4 appuis avant de jouer ; la fiche est une étape intermédiaire. | Un appui sur un morceau ouvre la partition (E11) ; la fiche s'ouvre par une icône « i » ou un menu. Ajouter une carte « Reprendre » en haut de la bibliothèque : dernier morceau et derniers réglages, qui ouvre E12 prérempli. Cela soutient aussi la rétention (OP-3). |
| UX-32 | Majeur | 03 (ensemble) ; EDB 10 §3 ; 07 R11 | L'inventaire des écrans ne couvre pas toutes les stories du MVP, et beaucoup de stories ne citent aucun écran. Manquent notamment : rapport d'import, séparation des mains d'un MIDI (US-IMP-02 CA4), choix de l'appareil MIDI, calibrage de la latence MIDI, mise en place du téléphone, avertissement « écouteurs », suspension et reprise, légende, « Aller à la mesure », morceaux masqués, suppression des données, « À propos » et licences, export du rapport de test. Aucune maquette téléphone n'existe pour E11 et E13. | Ajouter une colonne « Écran » dans chaque fiche de story et dans la matrice ; numéroter les nouveaux écrans à partir de E24 (liste au §3.8). `designer-ux` livre les spécifications dans `docs/UX/` avant chaque incrément (R11). |
| UX-33 | Majeur | 03 (ensemble) ; 07 R11 | Plusieurs états vides, d'erreur et de chargement manquent : voir le tableau du §3.8. | Ajouter les CA correspondants, ou renvoyer à la spécification UX. Compléter R11 : « maquette avec ses états vide, erreur et chargement ». |
| UX-34 | Mineur | 08 QO-24 e ; EDB ENF-ACC-03 et ENF-ACC-04 ; 07 D6 | Le contraste AA et la taille de texte du système sont reportés en V1. Ajoutés après coup, ils coûtent cher ; avec Compose et Material 3, ils sont presque gratuits s'ils sont prévus dès le départ. | Ajouter à D6 une règle de conception : textes en sp, pas de hauteur fixe pour les conteneurs de texte, jetons Material 3 conformes AA, descriptions des icônes pour TalkBack. Vérifier les écrans principaux avec une taille de police à 130 % ; 200 % en V1. |

---

## 3. Recommandations argumentées

### 3.1 QO-27 — Mode d'affichage unique du MVP

**Recommandation.** Comme mode unique du MVP, une **bande horizontale qui défile par paliers** : un seul système de grande portée, de longueur illimitée, **optimisé pour le paysage**. Le mode page (tourne en deux temps) arrive en V1 avec US-CNT-03, pour la tablette et le portrait. Cette recommandation est **à confirmer par le test ci-dessous, avant I1**. Si le test donne l'avantage au mode page en portrait, je me range à la recommandation du PO, à condition de faire du portrait l'orientation conseillée pour la pratique.

**Ordres de grandeur sur le téléphone de référence.** Hypothèses : écran de 6,1 à 6,7 pouces au format 20:9, soit environ 64 à 70 mm × 141 à 155 mm ; mode immersif avec une barre de 40 dp ; portée de 7,6 mm (cible du §3.2) ; un système de grande portée occupe en hauteur environ 5,75 fois la hauteur d'une portée, soit environ 44 mm. Ces valeurs sont à mesurer sur le prototype.

| Configuration | Systèmes visibles | Mesures visibles (niveau débutant) | Lecture en avance au pire moment | Lisible à 60 cm et conforme à US-VIS-05 CA1 ? |
|---|:-:|:-:|---|---|
| Page, paysage | 1 (et l'amorce du suivant) | 3 à 4 | Nulle en fin de système | **Non**, sauf portée d'environ 5,3 mm, lisible à environ 45 cm seulement |
| Page, portrait, tourne en deux temps | 3 | 3 à 4 (1 à 1,5 par système) | Environ 1 système (1 à 1,5 mesure) | Oui, mais retour à la ligne à chaque mesure et clés répétées sur environ 30 % de la largeur |
| **Bande horizontale, paysage** | 1 | 3,5 à 4,5 | ≥ 40 % de la largeur, soit environ 1,5 mesure au pire, 2 à 3 en général | **Oui** ; la portée peut monter à environ 10 mm, lisible à 80 cm |
| Bande horizontale, portrait | 1 | Environ 1,5 | Moins d'une mesure | Non recommandé : inviter à tourner le téléphone |

**Arguments.**
1. Le seul appareil de test est un téléphone. Le paysage y est l'orientation naturelle sur un pupitre de piano droit : plus stable, plus large. En paysage, seule la bande permet à la fois la lecture en avance et la lisibilité à 60 cm.
2. Pas de tourne de page, donc moins de risques de « perdre sa place ». La lecture se fait sans retour à la ligne, et la clé et l'armure restent affichées en rappel à gauche au lieu d'être répétées.
3. La bande convient naturellement au mode attente : le curseur avance d'événement en événement, et la bande se décale quand il faut.
4. Elle simplifie la lecture en avance aux reprises : la partition est déroulée dans l'ordre de jeu (UX-04).
5. C'est le modèle le plus répandu dans les applications d'apprentissage du piano sur téléphone ; les utilisateurs le connaissent.
6. La page de la partition d'origine est de toute façon recalculée sur téléphone (US-VIS-03 CA2) : le mode page n'offre donc pas la « géographie » d'une page imprimée.

**Inconvénients et parades.**
- Vue d'ensemble réduite. Parade : « Aller à la mesure » ; numéros de mesure sur chaque mesure ; partition annotée parcourue passage par passage depuis le compte-rendu.
- Mouvement de la partition. Parade : décalage **par paliers** plutôt que défilement continu.
- Portrait médiocre. Parade : message d'invitation à tourner le téléphone ; le portrait reste utilisable (US-VIS-07).

**Comportement à spécifier (si la bande est retenue).**
- Le curseur se déplace entre 20 % et 60 % de la largeur. Quand il dépasse 60 %, la bande se décale pour le ramener vers 20 %. En jeu continu, le décalage se fait sur une barre de mesure et dure 250 à 300 ms ; il est instantané si les animations sont désactivées dans Android.
- La clé et l'armure courantes restent affichées à gauche en rappel ; chaque mesure porte son numéro.
- Les reprises et les première et deuxième fins sont déroulées dans l'ordre de jeu, avec les numéros d'origine et la mention « 2e fois ».
- En lecture, un appui sur une mesure place le point de départ. Un appui long suivi d'un glissement sélectionne une plage, réglable avec des poignées. En séance, les appuis sur la partition sont ignorés.
- Le moteur de rendu de POC-04 doit savoir produire un système de longueur illimitée et indiquer la position de chaque mesure : critère à ajouter à POC-04.

**Test qui tranche (avant I1, coût nul).**

| Élément | Contenu |
|---|---|
| Prototype | Page HTML locale affichée en plein écran dans le navigateur du téléphone. Partition rendue en SVG par la version JavaScript du moteur évalué dans POC-04, curseur animé au tempo par un minuteur. Aucune analyse n'est nécessaire. Trois variantes : (A) page en portrait, tourne en deux temps ; (B) page en paysage à la taille cible ; (C) bande horizontale en paysage par paliers. |
| Matériel musical | Trois morceaux de démonstration pressentis : un à une main de niveau débutant ; un à deux mains de niveau débutant, avec altérations, doigtés et deux lignes supplémentaires ; un à deux mains de niveau intermédiaire, avec une reprise. |
| Participants | Le porteur et au moins 3 pianistes, dont au moins un débutant et une personne de plus de 45 ans, chacun avec sa correction visuelle habituelle. |
| Installation | Téléphone sur le pupitre du piano droit, distance des yeux à l'écran mesurée (60 ± 5 cm), éclairage habituel du soir, luminosité automatique. |
| Tâches (ordre des variantes alterné) | 1. Lire à voix haute les notes et les doigtés de 8 mesures. 2. Jouer en suivant un curseur qui s'arrête à chaque note (simulation du mode attente, avancé par un complice). 3. Jouer en suivant le curseur à 60 %, puis à 80 %. |
| Mesures | Hauteur de portée (mesurée à la règle sur l'écran) ; nombre de temps visibles après le curseur au pire moment ; nombre de « j'ai perdu ma place » ; arrêts dus à l'affichage ; confort noté de 1 à 5 ; préférence ; orientation choisie spontanément. |
| Règle de décision (fixée avant le test) | Retenir la variante qui (a) ne produit aucune erreur de lecture due à la taille chez au moins 3 participants sur 4, (b) montre toujours au moins une mesure entière après le curseur, (c) produit le moins de pertes de place. En cas d'égalité, retenir la variante préférée. Si C l'emporte en paysage et A en portrait, choisir selon l'orientation spontanément adoptée au pupitre. |
| Preuve | Fiche de test, photos du montage, mesures ; archivée comme preuve (D1) et citée dans la décision QO-27. |

### 3.2 Cible de lisibilité et protocole de test de US-VIS-03

**Base de la cible.** Une portée de piano imprimée mesure environ 7 mm et se lit à environ 55 cm ; elle est vue sous un angle d'environ 44 minutes d'arc. Garder le même angle donne les cibles suivantes.

| Usage | Distance | Hauteur de portée minimale | Équivalent nominal | Doigtés et numéros de mesure |
|---|:-:|:-:|:-:|:-:|
| Téléphone sur pupitre (MVP) | 60 cm | **7,6 mm** | ≈ 48 dp | Chiffres ≥ 2,8 mm (≥ 16 minutes d'arc) |
| Tablette sur pupitre (EF-VIS-03 CA2) | 70 cm | **8,9 mm** | ≈ 56 dp | ≥ 3,3 mm |
| Lecture lointaine (taille « Très grande ») | 80 cm | 10,2 mm | ≈ 64 dp | ≥ 3,7 mm |

**Textes à lire au pupitre** (E04, barre de E13, décompte, reprise, haut de E14). Les recommandations d'ergonomie demandent une hauteur de caractère de 16 à 22 minutes d'arc. À 60 cm, cela correspond à **24 sp au minimum, 28 à 32 sp recommandés** : rôles `headlineMedium` et `headlineLarge` de Material 3. Les tailles de corps de texte (14 à 16 sp) ne se lisent pas depuis le banc.

**Reformulation proposée de US-VIS-03 CA4.**
> Sur le téléphone de référence, en paysage, avec le réglage par défaut, la hauteur de portée affichée, calculée à partir de la densité physique réelle de l'écran, est d'au moins 7,6 mm et les doigtés d'au moins 2,8 mm ; le protocole de lisibilité est réussi à 60 cm. [PO]

**Vérification automatique.** Un test instrumenté calcule la taille physique de la portée par défaut à partir de la densité de l'écran (`DisplayMetrics.xdpi` et `ydpi`) sur le téléphone de référence et sur les profils d'émulateur réglés à leur taille physique réelle. Ce test protège la cible contre les régressions de mise en page.

**Protocole humain.**

| Élément | Contenu |
|---|---|
| Participants | Au moins 4 lecteurs de musique (le porteur et 3 autres), dont au moins une personne de plus de 45 ans (presbytie), chacun avec sa correction habituelle. |
| Installation | Téléphone en paysage sur le pupitre du piano droit, participant assis en position de jeu, distance des yeux à l'écran mesurée au mètre ruban (60 ± 5 cm). Éclairage habituel du soir, noté (une application de luxmètre gratuite suffit) ; luminosité automatique. |
| Matériel | 3 extraits de 8 mesures, inconnus des participants, de niveau débutant. Ils contiennent : notes sur 2 lignes supplémentaires, altérations accidentelles, armure à 2 altérations, doigtés, croches et notes pointées, liaisons, une nuance, numéros de mesure. |
| Tâche | Nommer à voix haute les notes et les doigtés des 8 mesures, sans limite de temps ; puis jouer la main droite en suivant le curseur. |
| Comptage | Erreurs de lecture dues à la taille : une erreur qui disparaît quand on relit à 30 cm est due à la taille. Demandes de se rapprocher ; buste penché en avant. |
| Critère de réussite | Aucune erreur due à la taille chez au moins 3 participants sur 4, et aucun participant penché. |
| Marge de sécurité | Recherche du seuil : réduire la taille par paliers de 10 % jusqu'à la première erreur due à la taille. La taille par défaut doit valoir au moins 1,2 fois le seuil médian. |
| Preuve | Fiche de test, photos, valeurs mesurées, version de l'application. Même protocole sur tablette à 70 cm en bêta. |

### 3.3 QO-24 g — Style unique « sobre et encourageant »

**Recommandation.** Je confirme la position du PO : **un style unique au MVP, « sobre et encourageant »**, et le style ludique en V1 (US-RSX-06).

Pour la manière de s'adresser à l'utilisateur (QO-12), je recommande :
- des **formulations impersonnelles par défaut** (« Passage joué sans erreur », « Rapprocher le téléphone du piano ») ;
- le **vouvoiement** quand il faut s'adresser à l'utilisateur (autorisation, confirmations) ;
- jamais le tutoiement au MVP ;
- en anglais, « you ».

Justification :
- les utilisateurs du MVP sont surtout des adultes (le porteur, les testeurs de la bêta) et des parents qui gèrent les autorisations ;
- les tournures impersonnelles conviennent aussi à un enfant et resteront valables quand le style ludique (tutoiement) arrivera ;
- l'écart pour Lucas est déjà assumé dans le PRD ([01 §2](../01-vision-et-objectifs-produit.md)).

**Règles opérationnelles**, à utiliser comme liste de contrôle à la relecture (D9) :
1. **Les faits, pas de jugement** : des chiffres et des lieux (« Mesures 9-12 »), jamais d'adjectif sur la personne.
2. **Commencer par ce qui va bien**, seulement si c'est vrai ; pas de félicitation automatique.
3. Les erreurs sont « **à retravailler** », jamais des « fautes », des notes « ratées » ou un jeu « mauvais ».
4. Quand la captation est douteuse, **c'est l'application qui entend mal**, pas l'utilisateur qui joue mal.
5. Au plus un point d'exclamation par écran ; pas d'emoji ; pas d'animation de récompense (réservée au style ludique).
6. Phrases courtes : 12 mots au plus pour ce qui se lit au pupitre.
7. Le vocabulaire est celui du lexique (UX-29).

**Exemples de textes au MVP.**

| Situation | Texte proposé |
|---|---|
| Passage sans erreur | « Passage joué sans erreur. » |
| Nouveau meilleur score | « 86 / 100 — meilleur score sur ce morceau. » |
| Partie réussie dans une séance imparfaite | « Sans erreur : mesures 1 à 8. » |
| Passage à retravailler (type dominant) | « Mesures 17-18 : surtout des notes fausses. » |
| Fin du mode attente | « 14 notes sur 16 justes du premier coup. » |
| Score bas | « 48 / 100. À retravailler en priorité : mesures 5 à 8. » (aucun « dommage ») |
| Captation douteuse | « L'application vous a mal entendu : score indicatif. Rapprochez le téléphone du piano ou réduisez le bruit, puis refaites le réglage du micro. » |
| Séance interrompue | « Séance interrompue à la mesure 12. » |
| Aucune note entendue | « Aucune note entendue. Vérifiez que le micro est autorisé et que le téléphone est près du piano. » |

Ces textes sont à valider par `pedagogue-piano` (vocabulaire musical) ; les traductions anglaises sont à faire relire par un musicien anglophone (ENF-I18N-03, V1).

### 3.4 Textes de la demande d'autorisation du micro

Le texte de la boîte de dialogue d'Android ne peut pas être modifié. Ses libellés exacts (titre, options) varient selon la version et le fabricant : **à relever sur le téléphone de référence** avant d'arrêter les textes ci-dessous. Les passages entre crochets sont à remplacer par les libellés relevés. Tous ces textes sont à valider par `juriste-conformite`.

**Écran d'explication, avant la demande du système (E03, branche micro).**

| Élément | Français | Anglais |
|---|---|---|
| Titre | L'application a besoin d'entendre votre piano | Piano Helper needs to hear your piano |
| Point 1 | Pour savoir quelles notes vous jouez, elle écoute votre piano avec le micro de ce téléphone. | To know which notes you play, it listens to your piano through this phone's microphone. |
| Point 2 | Le son est analysé sur ce téléphone. Il n'est envoyé nulle part. | The sound is analysed on this phone. It is not sent anywhere. |
| Point 3 | Rien n'est enregistré : le son est oublié dès qu'il a été analysé. | Nothing is recorded: the sound is discarded as soon as it has been analysed. |
| Point 4 | Le micro n'est ouvert que pendant vos séances et le réglage du micro. Le voyant « Micro actif » vous l'indique. | The microphone is only on during your practice sessions and microphone setup. The "Microphone on" indicator shows it. |
| Annonce de la demande du système | Android va vous demander d'autoriser l'application à [« enregistrer des contenus audio »] : c'est le nom qu'Android donne à l'accès au micro. Choisissez [« Pendant l'utilisation de l'appli »]. | Android will ask you to allow the app to ["record audio"]: this is what Android calls microphone access. Choose ["While using the app"]. |
| Bouton principal | Continuer | Continue |
| Bouton secondaire | Pas maintenant | Not now |

**Après un premier refus, ou après « Pas maintenant ».**
- Français : « Sans le micro, l'application ne peut pas vous écouter jouer. Vous pouvez quand même lire et écouter vos partitions, ou brancher un piano numérique en USB. » Boutons : [Autoriser le micro] [Continuer sans micro].
- Anglais : « Without the microphone, the app can't hear you play. You can still read and listen to your scores, or connect a digital piano over USB. » Boutons : [Allow microphone] [Continue without microphone].

**Après deux refus, ou si le système n'affiche plus la demande.**
- Français : « L'accès au micro est désactivé dans les réglages d'Android. Pour l'activer : Réglages > Applications > Piano Helper > Autorisations > Micro > [« Pendant l'utilisation de l'appli »]. » Boutons : [Ouvrir les réglages] [Annuler].
- Anglais : « Microphone access is turned off in Android settings. To turn it on: Settings > Apps > Piano Helper > Permissions > Microphone > ["While using the app"]. » Boutons : [Open settings] [Cancel].

**Si l'utilisateur avait choisi « Uniquement cette fois-ci ».** Bandeau court, sans répéter tout l'écran d'explication :
- Français : « Android redemande l'accès au micro, car vous l'aviez autorisé une seule fois. »
- Anglais : « Android is asking again because you allowed the microphone only once. »

**Voyant (US-ECO-04).** « Micro actif » / « Microphone on » pendant la captation ; « Micro coupé » / « Microphone off » pendant une pause. Le voyant associe une icône et un texte, jamais une couleur seule.

**Ligne dans les réglages (Réglages > Captation).**
- Français : « Micro : autorisé · utilisé seulement pendant les séances et le réglage du micro ».
- Anglais : « Microphone: allowed · used only during sessions and microphone setup ».

**Point de vigilance juridique.** « Il n'est envoyé nulle part » est vrai au MVP. Si la V1 permet de partager des enregistrements à la demande de l'utilisateur (ENF-CONF-01), il faudra mettre ce texte à jour.

### 3.5 Premier lancement : enchaînement et budget de temps (E01 à E04)

**Enchaînement proposé.**

```
E01 Accueil (3 étapes) ─► E02a Niveau ─► E02b Type de piano
                                            │
             ┌──────────────────────────────┴──────────────────────────────┐
     Numérique USB                                       Acoustique / sans connexion / Bluetooth / « Je ne sais pas »
             │                                                               │
  E03-MIDI « Branchez votre piano »                         E03-micro Explication (§3.4)
  (détection en direct, adaptateur USB OTG)                         │
             │   └─ rien de détecté ─► « Utiliser le micro pour l'instant » ─┤
             │                                              Demande du système Android
             │                                                     │ refus ─► « Découvrir en écoutant »
             │                                                     │          (démonstration en lecture, E11)
             │                                              E24 Mise en place : « Posez le téléphone
             │                                                  sur le pupitre, en paysage »
             │                                                     │
             │                                              E04 Réglage du micro (niveau, diapason) :
             │                                                  avance seul, lisible à 60 cm
             └──────────────────────────────┬──────────────────────┘
                                 Proposition de démonstration (« Plus tard » possible)
                                            │
                       E13 Mode attente, main droite, 4 mesures ─► E14 Compte-rendu
                                            │
                       Bibliothèque (démonstration marquée « pratiqué à l'instant »,
                       carte « Importer ma partition »)
```

**Budget de temps (parcours micro, utilisateur débutant).**

| Étape | Écran | Budget |
|---|---|:-:|
| Accueil | E01 | 10 s |
| Niveau, type de piano | E02a, E02b | 30 s |
| Explication et demande d'autorisation | E03-micro | 30 s |
| Mise en place sur le pupitre | Mise en place | 20 à 30 s |
| Réglage du micro (niveau, diapason ; sans mesure de latence) | E04 | 30 à 45 s |
| Proposition de démonstration | — | 10 s |
| 4 mesures à la main droite en mode attente, lecture à vue | E13 | 60 à 90 s |
| Affichage du compte-rendu | E14 | ≤ 2 s |
| **Total** | | **≈ 3 min 20 à 4 min 15** |

Avec le calibrage complet (environ 60 s) et un passage non borné, le parcours dépasse 5 minutes chez un débutant, d'où UX-14.

**Exigences propres à ces écrans.**
- E01 à E03 se lisent le téléphone en main : tailles normales.
- À partir de l'étape de mise en place, tout se lit à 60 cm (§3.2) et rien n'exige de toucher l'écran, sauf « Recommencer » et « Passer ».

### 3.6 Contenu MVP des écrans E12 et E14

L'écart entre les maquettes de l'EDB et le périmètre du MVP est déjà connu (README du PRD, point 4). Voici le contenu à spécifier.

**E12 — Préparation de séance (téléphone).**

```
┌─────────────────────────────────┐
│ Pratiquer · Für Elise        ✕  │
├─────────────────────────────────┤
│ Passage [Tout] [Mes. 9 → 12  ✎] │
│ Mains   [MD] [MG] [Les deux]    │
│ Mode    (•) Attente             │
│         ( ) Jeu continu         │
│ Tempo   [−]  70 % · ♩ = 84  [+] │  ← jeu continu seulement
│ ▸ Plus d'options                │
│   Retour en direct (jeu continu)│
│   Métronome · Décompte          │
│   Autre main jouée par l'appli  │
├─────────────────────────────────┤
│ Micro · réglé ✓                 │
│ Exigence : indulgente           │
│ ⚠ Import incomplet (voir)       │  ← si anomalie
├─────────────────────────────────┤
│          [ COMMENCER ]          │  ← fixé en bas
└─────────────────────────────────┘
```

Règles :
- Avec le micro, une option indisponible (jeu continu sans US-ECO-08) est grisée avec sa raison.
- Le jeu continu au micro porte la mention « expérimental » (QO-29).
- « Répéter » n'apparaît que si US-PRA-09 est retenue.
- Le métronome sonore, l'autre main et le son d'erreur suivent la règle des écouteurs au micro (UX-12, UX-13).

**E14 — Compte-rendu (téléphone), variante jeu continu.**

```
┌─────────────────────────────────┐
│ Séance terminée              ✕  │
├─────────────────────────────────┤
│            82 / 100             │  ← ≥ 45 sp, lisible à 60 cm
│  Meilleur score sur ce morceau  │
│ Notes 88 · Rythme 74 · Contin.90│
├─────────────────────────────────┤
│ À retravailler                  │
│ 1. Mes. 17-18 · notes fausses  ›│  ← lien vers la partition (UX-22)
│ 2. Mes. 31-32 · en avance      ›│
│ Sans erreur : mes. 1 à 8        │
├─────────────────────────────────┤
│ 5 fausses · 2 manquées · 1 en   │
│ trop · 3 en avance              │
│ 4 min 12 · Jeu continu · 2 mains│
│ 70 % · Exigence standard · Micro│
├─────────────────────────────────┤
│ [Voir la partition annotée]     │
│ [Recommencer]       [Terminer]  │
└─────────────────────────────────┘
```

Variantes à spécifier :

| Variante | Différence |
|---|---|
| Mode attente | Score de notes seul ; « 14 notes sur 16 justes du premier coup » ; « notes le plus souvent ratées » (US-PRA-02 CA4) à la place des erreurs de rythme. |
| Captation douteuse (US-RES-07) | Bandeau en tête avec la cause et le conseil ; score « indicatif » en gris, exclu du meilleur score ; bouton « Refaire le réglage du micro ». |
| Séance interrompue ou terminée avant la fin | « Séance interrompue à la mesure 12 » ; résultats limités à la partie jouée. |
| Aucune note jouée | Pas de score ; message (§3.3) ; séance non comptée dans l'historique, ou comptée « vide » : à trancher par le PO. |
| Analyse en cours | « Analyse en cours… » ; barre de progression au-delà de 2 s ; le bouton « Terminer » reste actif. |
| Échec de l'analyse | Message ; séance enregistrée « non analysée », avec la possibilité de relancer l'analyse. |

« Recommencer » relance directement la séance avec les mêmes réglages ; la préparation reste accessible par « Modifier les réglages ». Après « Terminer », l'utilisateur revient à la partition (E11), à l'endroit du passage joué.

### 3.7 Palette et symboles des résultats (ENF-ACC-02)

**Principe.** La **couleur indique la famille** d'erreur, le **symbole indique le type**. Les notes justes ne portent aucune marque par défaut. Une tête de note ne change jamais de forme.

| Famille | Couleur (thème clair) | Contraste sur blanc |
|---|---|:-:|
| Hauteur (fausse, en trop, rejouée) | Vermillon `#C23B00` | 5,4:1 |
| Rythme (en avance, en retard, accord non synchronisé) | Bleu `#005BB5` | 6,6:1 |
| Absence et continuité (manquée, hésitation, arrêt) | Gris `#5F6368` | 6,1:1 |
| Incertain (option « détections incertaines » seulement) | Gris moyen `#80868B` | 3,7:1 |
| Curseur | Bande translucide, d'une teinte hors des trois familles (ni bleu, ni violet) | — |

| Résultat | Marque, toujours hors de la tête de note |
|---|---|
| Juste | Aucune ; en mode attente, l'avance du curseur suffit |
| Fausse | Tête **pleine**, en vermillon, à la hauteur jouée, avec une pastille « ✕ » ; halo discret autour de la note attendue |
| En trop | Petite tête (taille d'appoggiature), en vermillon, avec une pastille « + » |
| Rejouée | Pastille « ↻ » en vermillon, petite (sévérité faible) |
| Manquée | Anneau en pointillé gris autour de la tête attendue ; la tête n'est pas évidée |
| En avance / en retard | Flèche bleue ← avant la note / → après la note |
| Accord non synchronisé | Pastille « ≠ » bleue au-dessus de l'accord ; pas de trait ondulé, qui se lirait comme un arpège |
| Hésitation / arrêt | Pastille « ‖ » grise entre les deux événements ; pas de virgule, qui se lirait comme une respiration |
| Incertain | « ? » et contour en pointillé, gris moyen |

Les pastilles mesurent au moins 1,5 interligne (environ 2,9 mm avec une portée de 7,6 mm), avec un trait d'au moins 0,3 interligne. Elles se placent au-dessus de la portée pour la main droite et au-dessous pour la main gauche, sans masquer les altérations ni les doigtés.

**Pré-contrôle par le calcul.** Méthode : simulation des daltonismes de Machado (2009) et distance de couleur ΔE (CIE 1976) entre les deux couleurs les plus proches. C'est une approximation, à confirmer sur l'appareil.

| Palette | Contrastes < 3:1 | ΔE minimal, protanopie | ΔE minimal, deutéranopie | ΔE minimal, tritanopie |
|---|---|:-:|:-:|:-:|
| Palette indicative de l'EDB, avec des teintes courantes (vert, rouge, gris, orange, bleu, violet, gris clair) | gris 2,7:1 ; orange 2,7:1 ; gris clair 1,9:1 | vert/rouge 17 ; bleu/violet 22 | bleu/violet 15 ; vert/rouge 17 | vert/bleu 18 |
| Proposition à 3 familles | aucun | ≥ 48 entre familles | ≥ 54 entre familles | ≥ 24 (rythme/absence, distingués aussi par la forme) |

Les écarts entre les deux gris (14) sont voulus : la forme les distingue, et l'« incertain » n'apparaît qu'en option. En niveaux de gris, les trois familles ont une luminance proche : les symboles portent seuls l'information, ce qui satisfait ENF-ACC-02.

**Vérification (I2).**
1. Option développeur d'Android « Simuler l'espace colorimétrique » (deutéranomalie, protanomalie, tritanomalie, monochromie) sur le téléphone de référence, avec une partition annotée qui contient tous les types de résultats.
2. Si possible, contrôle par un testeur daltonien.
3. Pas de couleurs dynamiques (Material You) pour les jetons de la zone de partition ; jetons nommés par rôle (`result.pitch`, `result.rhythm`, `result.absence`, `result.uncertain`, `cursor`), à décliner en thème sombre en V1.

### 3.8 Ce qu'il manque pour qu'un écran soit spécifiable

**Contenu minimal d'une spécification d'écran** (proposé pour R11) :
- stories et exigences couvertes ;
- données affichées ;
- actions et navigation (entrée et sortie) ;
- états : nominal, vide, chargement, erreur, hors ligne si besoin ;
- disposition sur téléphone en portrait et en paysage, et sur tablette ;
- ce qui se lit au pupitre ;
- textes en français et en anglais ;
- accessibilité ;
- questions en suspens.

**État des écrans du MVP.**

| Écran | Stories | Maquette EDB | Spécifiable maintenant ? | En attente de |
|---|---|:-:|---|---|
| E01 Accueil | US-CPT-01 | Non | Oui | — |
| E02 Profil (2 écrans) | US-CPT-02 | Non | Oui, sauf la description des niveaux | `pedagogue-piano` |
| E03 Captation et autorisation | US-ECO-01, -02, -03 | Non | Oui | Textes (§3.4) validés par `juriste-conformite` |
| E04 Calibrage | US-ECO-05, -06, -09 | Non | En partie | Méthode de POC-01 et POC-02 ; QO-23 |
| E05 Bibliothèque | US-BIB-01, -02, -04, -06 | Oui (navigation à corriger) | Oui | UX-30 |
| E07 Aperçu et rapport d'import | US-IMP-02, -04, -05, -07 | Non | Oui | Écran de séparation des mains à concevoir |
| E10 Fiche du morceau | US-BIB-03, -05, -07, US-HIS-02 | Non | Oui | — |
| E11 Partition, lecture | US-VIS-01 à -08, US-LEC-01 à -07 | Tablette seulement | **Non** | QO-27, POC-04 |
| E12 Préparation | US-PRA-01, -03, -05, -09, US-ECO-11 | Oui (à aligner) | En partie | QO-23, QO-24 b, QO-26, QO-29 |
| E13 Partition, jeu | US-PRA-02, -04, -06, -07, -08, US-RES-01, -02, US-ECO-04, -10 | Tablette seulement | **Non** | QO-27, QO-23 |
| E14 Compte-rendu | US-RES-03, -06, -07 | Oui (à aligner) | Oui, avec le §3.6 | QO-24 a et g |
| E15 Détail d'une note | US-RES-05, -08 | Non | Oui | QO-28 |
| E19 Réglages | US-CPT-03, -04, -05, US-BIB-04, US-CNF-02, -05 | Non | Oui | QO-24 e et f |

**Écrans à ajouter à l'inventaire** (numéros proposés, à confirmer par le PO) :

| N° | Écran | Stories |
|---|---|---|
| E24 | Mise en place du téléphone sur le pupitre | US-CPT-06, US-ECO-05 |
| E25 | Séparation des mains d'un MIDI à une piste | US-IMP-02 CA4 |
| E26 | Connexion MIDI : état, choix de l'appareil | US-ECO-02 CA2 et CA4 |
| E27 | Calibrage de la latence MIDI | US-ECO-06 |
| E28 | Séance suspendue ou interrompue : reprendre, recommencer, compte-rendu | US-PRA-06, US-PRA-08 |
| E29 | Légende des résultats | US-RES-04 CA4 |
| E30 | À propos, licences, confidentialité | US-CNF-02 |
| E31 | Supprimer toutes mes données (deux confirmations) | US-CPT-05 |
| E32 | Morceaux de démonstration masqués | US-BIB-04 CA4 |
| E33 | Export du rapport de test | US-CNF-05 |
| Dialogues | « Aller à la mesure », avertissement « écouteurs », micro refusé, sortie Bluetooth détectée | US-VIS-06, US-ECO-11, US-ECO-03, UX-13 |

**États manquants (UX-33).**

| Écran ou situation | État manquant | Proposition |
|---|---|---|
| E05 | Aucun morceau importé et toutes les démonstrations masquées | Message, « Importer », « Réafficher les démonstrations » |
| E07 | Stockage insuffisant | Message ; rien n'est ajouté |
| E07 | Fichier ouvert avant le premier lancement | Aperçu d'abord ; accueil raccourci ensuite |
| E10 | Aucune séance | « Pas encore de séance » et bouton « Pratiquer » |
| E11 | Partition importée impossible à afficher (après une mise à jour du moteur) | Message, « Réimporter », accès au rapport d'import |
| E11, E13 | Sortie audio occupée ou perdue (appel, autre application) | Lecture en pause, message |
| E11, E13 | Sortie Bluetooth détectée | Avertissement de latence (UX-13) |
| E04 | Aucune note détectée après 15 s | Conseil de placement, « Recommencer », « Passer » |
| E04 | Bruit ambiant trop fort | Conseil concret |
| E13 | Micro pris par une autre application, ou autorisation retirée pendant l'usage | Séance suspendue, message, marche à suivre |
| E13 vers E14 | Analyse de plus de 2 s | « Analyse en cours… » avec progression |
| E14 | Aucune note jouée ; échec de l'analyse | Voir §3.6 |
| Lancement | Migration des données en échec (US-HIS-03 CA3) | Message, données conservées, accès en lecture seule, export du rapport de test pour le support |

---

## 4. Résumé de la relecture

**Livrable.** Ce fichier : 34 remarques (2 bloquantes, 22 majeures, 10 mineures) et des propositions pour QO-27, QO-24 g, la lisibilité, la demande d'autorisation du micro, E12, E14 et le code des résultats.

**Choix de design proposés.**
- Bande horizontale par paliers, en paysage.
- Cible de lisibilité exprimée en millimètres de portée (7,6 mm à 60 cm).
- Textes à lire au pupitre d'au moins 28 sp.
- Démarrage et reprise du jeu en jouant une note.
- Commandes de séance d'au moins 64 dp.
- Code des résultats à trois familles de couleur plus un symbole, sans modifier les têtes de note.
- Style sobre, impersonnel par défaut, vouvoiement si nécessaire.
- Pas de barre de navigation au MVP.

**Questions ouvertes qui me concernent ou que je transmets.**

| Pour | Question |
|---|---|
| Porteur | Accepter le test comparatif du §3.1 avant I1 et la règle de décision fixée à l'avance ; relever sur le téléphone les libellés exacts de la demande d'autorisation du micro. |
| `pedagogue-piano` | Déroulé des reprises dans l'ordre de jeu (UX-04) ; doigtés agrandis (UX-03) ; symboles du §3.7 ; description des niveaux (UX-16) ; lexique (UX-29) ; textes du §3.3. |
| `expert-audio-mir` | Détecter « une note quelconque » pour lancer le décompte au micro (UX-08) ; régler le diapason avec des notes libres (UX-14). |
| `architecte` | Compenser la latence de sortie audio, notamment en Bluetooth (UX-13) ; rendu sur un système de longueur illimitée dans POC-04 (UX-01). |
| `juriste-conformite` | Textes du §3.4. |
| `product-owner` | Colonne « Écran » et nouveaux écrans E24 à E33 (UX-32) ; séance « vide » comptée ou non (§3.6) ; navigation du MVP (UX-30). |

**Suite pour `designer-ux`.**
- Dans `docs/UX/` : maquettes téléphone de E11 et E13 (après QO-27), E01 à E04, E12, E14, et les jetons de couleur et de typographie.
- Lexique des termes d'interface.
- Prototype HTML du test de QO-27.

## Suite donnée (PRD v0.2, `product-owner`)

Bilan : **34 intégrées (dont 1 en partie), 0 rejetée, 0 en attente (les 3 points en attente en v0.2 sont tranchés par les décisions du porteur du 2026-11-07, PRD v0.3)**. Modifications par arbitrage ([08 §3](../08-questions-ouvertes.md)) : son d'erreur indisponible au micro au lieu d'être soumis à la règle des écouteurs (A5) ; après un refus du micro, un seul bouton « Continuer sans micro » (A4). Questions transmises : porteur → QO-27 et relevé des libellés d'Android (actions du [fichier 09](../09-decisions-a-prendre.md)) ; `architecte` → latence de sortie Bluetooth, rendu sur un système de longueur illimitée (POC-04) ; `product-owner` → colonne « Écran » et E24 à E33 (A17), séance vide (A13), navigation (A16).

| Remarque | Statut | Suite donnée |
|---|---|---|
| UX-01 | Intégrée (décision QO-27, 2026-11-07) | Test comparatif repris comme règle de décision ; position provisoire : bande horizontale (US-VIS-02) ; US-VIS-05 CA1 indépendant du mode ; critère ajouté à POC-04 |
| UX-02 | Intégrée | US-VIS-03 CA1 à CA3 (7,6 mm, arbitrage A7) |
| UX-03 | Intégrée | US-VIS-01 CA2 (à valider par PED) |
| UX-04 | Intégrée | US-VIS-05 CA2 |
| UX-05 | Intégrée | US-VIS-02 CA6 |
| UX-06 | Intégrée | US-VIS-03 CA4 |
| UX-07 | Intégrée | US-VIS-02 CA5 |
| UX-08 | Intégrée | US-PRA-05 CA1 |
| UX-09 | Intégrée | US-PRA-08 CA2, US-PRA-02 CA6, US-PRA-06 CA2 (arbitrage A6) |
| UX-10 | Intégrée | US-PRA-07 CA2, US-PRA-04 CA4, US-VIS-06 CA3, D6 |
| UX-11 | Intégrée | US-LEC-05 CA4, US-PRA-05 CA3 |
| UX-12 | Intégrée en partie | US-PRA-02 CA5, US-ECO-11 CA2 ; son d'erreur indisponible au micro plutôt que soumis aux écouteurs (A5) |
| UX-13 | Intégrée | US-ECO-11 CA2 et CA3 ; compensation de latence demandée à `architecte` |
| UX-14 | Intégrée | US-CPT-06 CA1 à CA4, US-ECO-05 CA1 et CA2 |
| UX-15 | Intégrée | US-CPT-06 CA1 à CA3, US-CPT-02 CA3, US-CPT-01 CA5 |
| UX-16 | Intégrée | US-CPT-02 CA1 à CA3 (phrases de PED) |
| UX-17 | Intégrée | US-CPT-06 CA6 (arbitrage A11) |
| UX-18 | Intégrée | US-ECO-03 CA1, CA4, CA5 et §8.1 ; libellés à relever par le porteur |
| UX-19 | Intégrée | US-PRA-01 |
| UX-20 | Intégrée | US-RES-01 CA1 ; MOD-16 |
| UX-21 | Intégrée | US-RES-03 CA1 et CA6, US-RES-07, US-PRA-07 CA5, US-ANA-09 CA5 |
| UX-22 | Intégrée (décision QO-24 a, 2026-11-07) | Lien « Voir sur la partition » intégré (US-RES-03 CA4) |
| UX-23 | Intégrée | US-RES-01 CA3 ; MOD-38 |
| UX-24 | Intégrée | US-RES-04 CA5 |
| UX-25 | Intégrée | US-RES-05 CA2 |
| UX-26 | Intégrée | US-RES-04 CA1 et CA2 ; MOD-35 (symboles à valider par PED) |
| UX-27 | Intégrée | US-RES-04 CA3, US-VIS-04 CA1 |
| UX-28 | Intégrée (décision QO-24 g, 2026-11-07) | Règles de ton intégrées (03 §1.3, US-RES-03 CA2 et CA8, D9) |
| UX-29 | Intégrée | US-CPT-04 CA8 |
| UX-30 | Intégrée | 03 §1.3, US-CPT-01 CA6 (arbitrage A16) |
| UX-31 | Intégrée | US-BIB-01 CA3 et CA4 |
| UX-32 | Intégrée | Champ « Écran » dans chaque story, écrans E24 à E33 (03 §1.4, A17) ; MOD-36 |
| UX-33 | Intégrée | R11 ; états dans US-BIB-01 CA5, US-BIB-07 CA1, US-IMP-06 CA5, US-VIS-08 CA4, US-LEC-02 CA4, US-ECO-04 CA5, US-ECO-05 CA6, US-RES-03 CA1, US-PRA-07 CA5, US-HIS-03 CA3 |
| UX-34 | Intégrée | D6 ; MOD-29 |
