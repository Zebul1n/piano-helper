# 05 — Exigences fonctionnelles

Format de chaque exigence : identifiant, intitulé, priorité, description, critères d'acceptation (CA). Les priorités suivent la convention MoSCoW du [README](README.md).

---

## 1. Import de partition (IMP)

### EF-IMP-01 — Import MusicXML — **M**
L'utilisateur peut importer une partition au format MusicXML : `.musicxml`, `.xml` (non compressé) et `.mxl` (compressé).

- CA1 : un fichier MusicXML 3.1 ou 4.0 valide exporté depuis MuseScore, Finale, Sibelius ou Dorico s'importe sans erreur.
- CA2 : les éléments suivants sont conservés : portées, clés, armures, chiffrages, notes, silences, durées, altérations, liaisons de prolongation, liaisons d'expression, nuances, indications de tempo, reprises, doigtés, numéros de mesure.
- CA3 : les éléments non pris en charge sont ignorés sans bloquer l'import et listés dans le rapport d'import (EF-IMP-12).

### EF-IMP-02 — Import MIDI — **M**
L'utilisateur peut importer un fichier MIDI standard (`.mid`, `.midi`, types 0 et 1).

- CA1 : l'application produit une partition affichable à partir du MIDI (quantification des durées, détection des mesures si l'information de chiffrage est présente).
- CA2 : pour un fichier de type 1 avec deux pistes piano, chaque piste est associée à une portée. Pour un fichier de type 0 ou une seule piste, l'application propose un point de séparation main gauche / main droite (par défaut : do central) modifiable.
- CA3 : l'utilisateur est averti que l'affichage d'une partition issue d'un MIDI peut être approximatif (quantification).
- CA4 : l'orthographe des notes suit des règles musicales : tonalité du fichier, ou estimée et indiquée au rapport d'import ; notes de la gamme selon la tonalité ; 6e et 7e degrés haussés en mineur ; chromatismes selon le sens de la ligne (dièse en montant, bémol en descendant) ; pas de double altération ; même orthographe d'une hauteur dans une mesure. Exemple de contrôle : *Für Elise* importé d'un MIDI affiche ré♯ (et non mi♭).
- CA5 : l'aperçu permet d'indiquer une anacrouse ou de décaler les barres de mesure ; un premier temps qui semble mal placé est signalé au rapport.
- CA6 : pour un fichier à une piste, quand c'est possible, une note est attribuée à la main la plus proche de la note précédente de chaque main (séparation par proximité, en plus du point de séparation).

### EF-IMP-03 — Import PDF avec reconnaissance — **S** (V1)
L'utilisateur peut importer un PDF de partition. L'application en extrait une partition structurée par reconnaissance optique (OMR).

- CA1 : les PDF « numériques » (générés par un logiciel de gravure) sont reconnus avec un taux d'erreur par note inférieur à un seuil à définir après [POC-03].
- CA2 : les PDF scannés de bonne qualité (≥ 300 dpi) sont acceptés ; les autres déclenchent un avertissement.
- CA3 : un PDF de plusieurs pages est traité comme une seule partition.
- CA4 : la durée de traitement est affichée (progression) ; l'utilisateur peut quitter l'écran pendant le traitement et être notifié à la fin.

### EF-IMP-04 — Import par photo — **C** (V2)
L'utilisateur photographie une ou plusieurs pages de partition papier.

- CA1 : détection automatique des bords, redressement de perspective, amélioration du contraste.
- CA2 : ajout, suppression et réordonnancement des pages avant reconnaissance.
- CA3 : mêmes règles de reconnaissance et de vérification que EF-IMP-03.

### EF-IMP-05 — Réception depuis d'autres applications — **M**
L'application est proposée comme destination pour les fichiers des formats supportés (« Ouvrir avec », « Partager vers »), et donne accès au sélecteur de fichiers du système (y compris les stockages en ligne intégrés : Google Drive, Nextcloud, etc.).

- CA1 : un fichier `.mxl` reçu en pièce jointe d'un mail peut être ouvert directement dans l'application.
- CA2 : un fichier téléchargé depuis le navigateur peut être importé en moins de trois interactions.

### EF-IMP-06 — Aperçu et métadonnées — **M**
Avant validation, l'application affiche un aperçu : première page rendue, titre, compositeur, nombre de mesures, nombre de portées, tempo indiqué.

- CA1 : les métadonnées présentes dans le fichier sont préremplies.
- CA2 : l'utilisateur peut modifier titre et compositeur avant validation. L'ajout d'une étiquette à l'import arrive en V1 avec les étiquettes (EF-BIB-02) (décision QO-24 d).

### EF-IMP-07 — Vérification du résultat de reconnaissance — **S** (V1)
Pour une partition issue de reconnaissance, l'application affiche le résultat en vis-à-vis de l'image d'origine (ou en superposition), avec les zones à faible confiance mises en évidence.

- CA1 : l'utilisateur peut naviguer de zone douteuse en zone douteuse.
- CA2 : il peut écouter le résultat pour détecter les erreurs à l'oreille.

### EF-IMP-08 — Correction simple de la partition — **S** (V1)
L'utilisateur peut corriger les erreurs courantes de reconnaissance : hauteur d'une note, altération, durée, ajout ou suppression d'une note, armure, chiffrage.

- CA1 : chaque correction est annulable.
- CA2 : il ne s'agit pas d'un éditeur complet ; les opérations sont limitées à celles listées.

### EF-IMP-09 — Format MuseScore — **C** (V2)
Import des fichiers `.mscz`. [QO-08 : faisabilité sans dépendre de MuseScore]

### EF-IMP-10 — Formats MEI et ABC — **C** (V2)

### EF-IMP-11 — Sélection de la partie piano — **S** (V1)
Si la partition contient plusieurs instruments (ex. chant et piano), l'utilisateur choisit la ou les parties qu'il joue. Les autres parties peuvent être affichées et jouées en accompagnement, mais ne sont pas analysées.

### EF-IMP-12 — Rapport d'import — **M**
Après import, un rapport liste ce qui n'a pas pu être interprété (éléments ignorés, mesures incomplètes, incohérences de durée).

- CA1 : le rapport est consultable plus tard depuis la fiche du morceau.
- CA2 : si des incohérences risquent de fausser l'analyse du jeu (ex. mesures de durée incorrecte), l'utilisateur en est averti.

---

## 2. Bibliothèque (BIB)

### EF-BIB-01 — Liste des partitions — **M**
- CA1 : liste avec titre, compositeur, date d'ajout, dernière pratique, meilleur score.
- CA2 : tri par titre, compositeur, date d'ajout, dernière pratique.
- CA3 : recherche texte sur titre et compositeur.

### EF-BIB-02 — Organisation — **S**
Étiquettes libres, niveau de difficulté (déclaratif), favoris, statut (à découvrir, en cours, maîtrisé).

### EF-BIB-03 — Gestion — **M**
Renommer, modifier les métadonnées, supprimer (avec confirmation ; la suppression efface aussi l'historique de pratique associé après avertissement), exporter le fichier source.

### EF-BIB-04 — Synchronisation multi-appareils — **C** (V2)
Bibliothèque et historique synchronisés entre téléphone et tablette d'un même compte.

### EF-BIB-05 — Morceaux de démonstration — **M**
L'application embarque 5 à 10 morceaux courts du domaine public, de niveaux variés (débutant à intermédiaire), avec une partition dont les droits de la gravure numérique sont libres. [voir 11-juridique]

- CA1 : critères juridiques : air traditionnel ou compositeur **mort avant 1900** ; texte musical original, sans arrangement ni simplification moderne d'un tiers ; saisie d'après une édition ancienne du domaine public, sans reprendre d'apports d'éditeur moderne ; licence et attribution inscrites dans le fichier ; fiche d'inventaire (œuvre, compositeur et dates, édition source avec adresse et date de consultation, auteur de la saisie, licence, date de vérification).
- CA2 : source et saisie (décision QO-31) : **gravure maison** en priorité, saisie dans un logiciel libre d'après une édition ancienne numérisée, publiée sous licence **CC0 1.0** ; la saisie est faite par un agent, relue note par note par `pedagogue-piano`, puis validée à l'écoute par le porteur. Des fichiers existants sous licence libre (par exemple Mutopia, CC BY ou CC BY-SA, attribution obligatoire) peuvent compléter la sélection.
- CA3 : la sélection comprend au moins deux morceaux à une main (dont un à la main gauche en clé de fa), un morceau avec accords, un morceau avec reprise simple et un morceau à deux mains de niveau intermédiaire.

---

## 3. Visualisation de la partition (VIS)

### EF-VIS-01 — Rendu fidèle — **M**
La partition est rendue selon les conventions de la gravure musicale classique.

- CA1 : grand portée (clés de sol et fa, accolade), changements de clé, armures, chiffrages, altérations accidentelles, liaisons, ligatures, points, triolets, silences, reprises, nuances, indications de tempo, doigtés, pédale, ornements usuels (trille, mordant, gruppetto, appogiature).
- CA2 : le rendu est lisible et sans chevauchement sur les partitions de test du corpus de référence (à constituer).

### EF-VIS-02 — Modes d'affichage — **M** (un mode) / **S** (les deux)
- Mode page : pages entières, tourne de page.
- Mode défilement continu : une seule ligne ou bande verticale qui défile.
- CA1 : l'utilisateur change de mode sans perdre sa position.
- CA2 : mode du MVP (décision QO-27) : **bande horizontale en paysage**, qui avance par paliers, en attendant le test au pupitre fait par le porteur avant I1 ; la décision issue de ce test est provisoire et reconfirmée en bêta avec des testeurs. Le mode page arrive en V1.

### EF-VIS-03 — Zoom et lisibilité — **M**
- CA1 : zoom par pincement et par réglage persistant par morceau.
- CA2 : sur tablette 10 pouces en paysage, une partition de niveau débutant est lisible à 70 cm avec le réglage par défaut : hauteur physique de portée ≥ 8,9 mm. Sur téléphone posé sur le pupitre, lecture à 60 cm : portée ≥ 7,6 mm, doigtés et numéros de mesure ≥ 2,8 mm.
- CA3 : en mode page, la mise en page se recalcule (nombre de mesures par ligne) selon la taille d'écran et le zoom ; en bande, le zoom est une mise à l'échelle.

### EF-VIS-04 — Curseur synchronisé — **M**
Pendant la lecture ou la pratique, un curseur indique la position courante ; les notes en cours sont mises en évidence.

- CA1 : décalage visuel entre le son et le curseur imperceptible (voir ENF-PERF-03).

### EF-VIS-05 — Tourne de page et défilement automatiques — **M**
- CA1 : quel que soit le mode d'affichage, au moins **une mesure entière et 4 temps** sont visibles au-delà du curseur à tout instant, au réglage par défaut ; la page suivante (ou la suite de la bande) apparaît avant que le curseur n'atteigne la fin de ce qui est affiché, de façon à pouvoir lire en avance.
- CA2 : option de tourne de page en deux temps (moitié haute puis moitié basse), comme sur les pupitres numériques (mode page).
- CA3 : aux reprises, la musique jouée ensuite est visible avant la fin de la mesure (lecture en avance dans l'ordre de jeu).

### EF-VIS-06 — Couleurs des mains — **C**
Option de colorer différemment les notes de chaque main.

### EF-VIS-07 — Clavier virtuel — **S**
Un clavier affiché en bas de l'écran montre les touches à jouer (lecture) et les touches effectivement jouées (pratique).

- CA1 : l'étendue affichée s'adapte à la tessiture du passage.
- CA2 : distinction visuelle entre attendu et joué, et entre main gauche et droite.

### EF-VIS-08 — Nom des notes — **S**
Option d'afficher le nom des notes sous les têtes de note, en notation latine (do, ré, mi) ou anglo-saxonne (C, D, E), selon les paramètres.

### EF-VIS-09 — Annotations personnelles — **C** (V2)
Ajout de doigtés, d'annotations libres et de surlignages, conservés avec la partition, masquables.

### EF-VIS-10 — Thème — **S**
Mode clair, mode sombre (fond sombre, notes claires), mode contraste élevé.

### EF-VIS-11 — Navigation — **M**
Aller à une mesure par numéro, revenir au début, sélectionner une plage de mesures par glisser.

### EF-VIS-12 — Orientation — **M**
Portrait et paysage pris en charge, sur téléphone et tablette.

---

## 4. Lecture audio de la partition (LEC)

### EF-LEC-01 — Lecture — **M**
La partition est jouée avec un son de piano réaliste, généré localement (sans connexion).

### EF-LEC-02 — Commandes — **M**
Lecture, pause, arrêt, démarrage depuis n'importe quelle mesure ou note (appui sur la partition).

### EF-LEC-03 — Tempo — **M**
Réglage du tempo de 25 % à 150 % du tempo indiqué, ou en battements par minute absolus.

- CA1 : le changement de tempo ne modifie pas la hauteur.
- CA2 : tempo par défaut : l'indication métronomique de la partition ; sinon une valeur déduite de l'indication textuelle (Adagio, Allegro…) ; sinon une valeur selon le chiffrage (tables de `pedagogue-piano` ; exemples : 2/4, 3/4, 4/4 → ♩ = 80 ; 6/8 → ♩. = 54). Un tempo estimé est affiché comme tel.
- CA3 : l'utilisateur peut fixer le tempo de référence du morceau (celui que demande le professeur), mémorisé avec le morceau.

### EF-LEC-04 — Mains séparées — **M**
Main droite seule, main gauche seule, les deux. Option : main non sélectionnée jouée à volume réduit plutôt que coupée.

- CA1 : par défaut, portée du haut = main droite, portée du bas = main gauche. Les exceptions (notes croisées marquées dans le fichier) sont respectées si l'information est disponible.

### EF-LEC-05 — Métronome — **M**
Métronome activable, avec accentuation du premier temps, subdivision optionnelle, son et volume réglables.

- CA1 : l'unité de battement dépend du chiffrage (noire en 4/4, noire pointée en 6/8…) ; sous 40 battements par minute, la subdivision est activée automatiquement.
- CA2 : au micro, le métronome est visuel par défaut (EF-ECO-08).

### EF-LEC-06 — Boucle — **M**
Sélection d'une plage de mesures (ou de temps) lue en boucle, avec pause optionnelle entre deux répétitions.

### EF-LEC-07 — Reprises et sauts — **M** (reprises simples) / **S** (complet)
Respect des barres de reprise, première et deuxième fins, D.C., D.S., al Fine, al Coda. Option pour ignorer les reprises.

**Reprises simples (MVP)** :
- barre de reprise de fin avec sa barre de début : rejouer depuis la barre de début ;
- barre de fin **sans** barre de début : revenir à la dernière barre de début ; à défaut, à la mesure qui suit la dernière reprise jouée ; à défaut, au début du morceau ;
- quand la section commence par une anacrouse, la reprise revient à l'**anacrouse** ;
- fins multiples « 1. / 2. » et « 1.-3. / 4. » ; barre de reprise au milieu d'une mesure si le fichier la représente par deux mesures partielles ;
- option « ignorer les reprises » : chaque section est jouée une fois, les fins autres que la dernière sont sautées ; elle s'applique à la lecture, au curseur et à l'analyse ;
- passage sélectionné : une reprise n'est suivie que si ses deux barres sont dans la sélection ; une sélection qui coupe une reprise est jouée une fois, de façon linéaire.

Exclus du MVP (signalés au rapport d'import, lecture linéaire) : D.C., D.S., Fine, Coda, reprises imbriquées, répétitions écrites en texte (« 3 fois »).

### EF-LEC-08 — Interprétation à la lecture — **C**
Respect des nuances, de la pédale et des articulations pour une lecture plus musicale.

### EF-LEC-09 — Sortie MIDI — **C**
Si un piano numérique est connecté, la lecture peut être envoyée vers celui-ci (le piano joue).

### EF-LEC-10 — Mixage — **S**
Volumes séparés pour la lecture, le métronome et les sons de retour.

---

## 5. Modes de pratique (PRA)

### EF-PRA-01 — Préparation d'une séance — **M**
Avant de jouer, l'utilisateur choisit : passage (morceau entier ou plage de mesures), main(s), tempo, mode, type de retour en direct.

- CA1 : les derniers réglages utilisés sur un morceau sont proposés par défaut.

### EF-PRA-02 — Mode attente — **M**
Le curseur s'arrête sur chaque note ou accord attendu et n'avance que lorsque l'utilisateur l'a joué correctement. Le rythme n'est pas évalué.

- CA1 : une note fausse est signalée immédiatement (visuel ; son optionnel, désactivé par défaut et indisponible au micro) ; le curseur ne bouge pas. L'erreur est toujours affichée, quel que soit le réglage du retour en direct (EF-RES-01).
- CA2 : un accord est validé quand toutes ses notes ont été enfoncées dans une fenêtre de tolérance (EF-ANA-06), même si elles ne sont pas parfaitement simultanées.
- CA3 : les notes tenues qui se prolongent ne bloquent pas la progression.
- CA4 : option « main non travaillée jouée automatiquement » lorsque l'utilisateur ne joue qu'une main : sans restriction en MIDI ; au micro, seulement avec des écouteurs filaires (EF-ECO-08) (décision QO-24 b).
- CA5 : une note en trop jouée avec la ou les notes attendues n'empêche pas d'avancer seulement si elle est à 1 ou 2 demi-tons d'une note attendue et seule de son espèce dans cette main ; elle est comptée « en trop ». Au-delà (plusieurs notes en trop, ou note éloignée), l'événement n'est pas validé et l'utilisateur est invité à jouer seulement la ou les notes indiquées.
- CA6 : une note attendue est « juste » seulement si elle est trouvée du premier coup ([06 §3.1](06-analyse-du-jeu-et-erreurs.md#31-erreurs-de-notes)).

### EF-PRA-03 — Mode jeu continu — **M**
Le curseur avance au tempo choisi ; l'utilisateur joue en suivant. L'analyse porte sur les hauteurs et le rythme.

- CA1 : le jeu n'est jamais interrompu par l'application à cause d'une erreur.
- CA2 : le métronome est activable pendant le jeu (au micro, selon EF-ECO-08).
- CA3 : au micro, si POC-01 est concluant en jeu continu, la fonction est marquée « expérimental », avec par défaut aucun retour en direct (résultats dans le compte-rendu) ; elle est revérifiée en bêta sur d'autres appareils (décision QO-29).

### EF-PRA-04 — Mode suivi — **S** (V1)
L'application suit la position de l'utilisateur dans la partition à son propre tempo (suivi de partition, « score following »), sans métronome imposé. Le curseur et les tournes de page suivent le joueur.

- CA1 : le suivi tolère les variations de tempo naturelles (rubato modéré), les hésitations et les petites erreurs.
- CA2 : après une reprise d'un passage (l'utilisateur recommence quelques mesures en arrière), le suivi se recale en moins de deux mesures [HYP].

### EF-PRA-05 — Décompte — **M**
Décompte d'une mesure (ou réglable) au métronome avant le début du jeu, en modes continu et boucle (EF-PRA-11).

- CA1 : le décompte dure au moins 3 pulsations : 1 mesure par défaut, 2 mesures en 2/4, 6/8 et 3/8 ; réglable de 1 à 2 mesures.
- CA2 : par défaut, le décompte se lance quand l'utilisateur joue une note (n'importe laquelle, non analysée) ; option : lancement par un bouton, précédé d'un court délai.
- CA3 : avec une anacrouse, le décompte compte les mesures demandées puis continue sur les temps qui précèdent l'anacrouse ; la première note tombe à sa place, jamais sur un « 1 » ajouté.
- CA4 : le décompte est toujours visuel ; il est aussi sonore en MIDI, et au micro selon EF-ECO-08.

### EF-PRA-06 — Boucle progressive — **S** (V1)
L'utilisateur fixe un tempo de départ, un tempo cible, un incrément et un seuil de réussite. Le tempo augmente à chaque boucle réussie au-dessus du seuil.

- CA1 : option de redescendre le tempo après N échecs.
- CA2 : synthèse de la séance : scores et tempo de chaque boucle.

### EF-PRA-07 — Accompagnement de l'autre main — **S** (V1)
Quand l'utilisateur travaille une main, l'application peut jouer l'autre main au tempo du jeu.

- CA1 : en captation micro, l'accompagnement joué par l'application ne doit pas être analysé comme du jeu de l'utilisateur (voir EF-ECO-08).

### EF-PRA-08 — Détection d'arrêt — **S** (MVP)
En jeu continu, un silence prolongé (aucune nouvelle attaque) **met la séance en pause**, sans la terminer. L'atteinte de la fin du passage termine la séance. L'utilisateur peut aussi mettre en pause ou terminer manuellement. Valeurs : décision QO-25, détaillées en [06 §4.1](06-analyse-du-jeu-et-erreurs.md#41-niveaux-dexigence).

- CA1 : pause après le délai de pause du niveau (seuil d'arrêt de base + 3 s : 7 s en indulgent, 6 s en standard, 5 s en exigeant) ; le curseur reste sur la dernière position jouée et le micro reste ouvert, voyant allumé.
- CA2 : reprise sans toucher l'écran : jouer une note relance le décompte et la reprise au début de la mesure précédente ; l'interruption compte pour un arrêt, quelle que soit sa durée.
- CA3 : fin automatique après 30 s de pause (réglable de 15 à 60 s), avec affichage du compte-rendu.
- CA4 : pendant la pause, l'écran propose aussi : voir le compte-rendu, recommencer.
- CA5 : en mode attente, aucune pause avant 2 minutes sans note ; au-delà, le micro se ferme et la reprise demande un appui.

### EF-PRA-09 — Mode « écoute puis joue » — **C**
L'application joue un fragment, puis l'utilisateur le rejoue.

### EF-PRA-10 — Mode performance — **C** (V2)
Jeu du morceau complet, sans retour en direct, éventuellement sans curseur, avec compte-rendu à la fin.

### EF-PRA-11 — Boucle de pratique — **S** (MVP)
En jeu continu, l'utilisateur peut faire répéter le passage sélectionné plusieurs fois d'affilée (mode « boucle », décision QO-26).

- CA1 : 3 répétitions par défaut, nombre réglable, ou répétition jusqu'à l'arrêt.
- CA2 : entre deux répétitions, une mesure de pause puis le décompte (EF-PRA-05).
- CA3 : chaque répétition est analysée séparément (un « passage » de la séance, voir [09](09-modele-de-donnees.md)) ; le compte-rendu montre le score de chacune.
- CA4 : le tempo ne change pas d'une répétition à l'autre ; la boucle à tempo croissant est la boucle progressive (EF-PRA-06, V1).

---

## 6. Écoute du pianiste (ECO)

### EF-ECO-01 — Choix de la source — **M**
L'utilisateur choisit la source de captation : MIDI (piano numérique connecté) ou micro (tout piano). Le choix est mémorisé et modifiable à tout moment.

- CA1 : si un appareil MIDI est connecté, l'application le propose automatiquement.

### EF-ECO-02 — MIDI USB — **M**
Connexion d'un piano numérique ou d'un clavier par câble USB (directement ou via adaptateur).

- CA1 : l'application reçoit les événements note activée / note désactivée, vélocité, pédale de sustain.
- CA2 : connexion et déconnexion à chaud détectées et signalées.

### EF-ECO-03 — MIDI Bluetooth — **S** (V1)
Connexion d'un instrument en Bluetooth Low Energy MIDI, avec écran d'appairage intégré.

### EF-ECO-04 — Captation micro — **M** (MVP : mode attente obligatoire, jeu continu si POC-01 concluant ; V1 : tous les modes)
L'application capte le son du piano par le micro de l'appareil et en extrait les notes jouées (EF-ANA-01).

- CA1 : fonctionne avec un piano droit ou à queue, et un piano numérique joué sur ses haut-parleurs.
- CA2 : l'appareil est posé sur le pupitre ou à proximité (≤ 1,5 m).
- CA3 : respecte les seuils de qualité définis dans [01 — indicateurs](01-contexte-et-objectifs.md#5-indicateurs-de-succès) après validation [POC-01].
- CA4 : le jeu continu au micro, s'il entre au MVP, est marqué « expérimental » (EF-PRA-03, décision QO-29).

### EF-ECO-05 — Autorisations et transparence — **M**
Avant la première demande d'accès au micro, l'application explique pourquoi elle en a besoin, que le traitement est local par défaut et qu'aucun enregistrement n'est conservé sans accord.

- CA1 : en cas de refus, l'application reste utilisable (lecture, MIDI) et explique comment réactiver l'accès.
- CA2 : un indicateur visible signale que le micro est actif.

### EF-ECO-06 — Calibrage — **M**
Assistant de calibrage, relançable :

- vérification du niveau d'entrée (micro) ;
- vérification de la cohérence des horodatages de captation (micro et MIDI), avec une correction seulement si un écart est constaté (CA2) ;
- détection du diapason du piano (micro), pour tolérer un piano accordé un peu haut ou bas (EF-ECO-10).

- CA1 : le calibrage dure moins d'une minute.
- CA2 : au micro, un son émis par l'appareil et capté par son micro sert de **test de cohérence** des horodatages (et non le jeu de l'utilisateur au clic, qui mesure surtout l'anticipation du joueur) ; une correction de latence n'est appliquée que si ce test révèle un écart, sinon la même latence serait comptée deux fois.
- CA3 : en MIDI USB, aucune compensation de latence par défaut (le décalage constant du joueur est déjà neutralisé par l'analyse, [06 §4.2](06-analyse-du-jeu-et-erreurs.md#42-référence-temporelle)) ; le test au clic reste une option avancée, absente du premier lancement, et la compensation qu'il produit est bornée à 30 ms [HYP].

### EF-ECO-07 — Indicateur de signal — **S** (MVP, décision QO-23)
Pendant le jeu, un indicateur discret signale un niveau trop faible, une saturation ou un bruit ambiant excessif. Il ne masque jamais la partition et ne demande aucune interaction.

### EF-ECO-08 — Gestion des sons produits par l'application — **S** (MVP : forme minimale, décision QO-23 ; V1 : complète)
Quand l'application joue elle-même (métronome, décompte, autre main, accompagnement) et que la captation est par micro, ces sons ne doivent pas être pris pour du jeu de l'utilisateur.

- CA1 (MVP) : au micro, métronome et décompte **visuels** par défaut.
- CA2 (MVP) : au micro, le métronome sonore et l'autre main jouée par l'application ne sont disponibles qu'avec des **écouteurs filaires**, après un avertissement ; le son d'erreur du mode attente est indisponible.
- CA3 (MVP) : une sortie audio Bluetooth est détectée et fait l'objet d'un avertissement (latence).
- V1 : accompagnement au micro sans écouteurs (EF-PRA-07). Pistes : annulation d'écho, soustraction des notes connues de l'accompagnement. [POC-01]

### EF-ECO-09 — Enregistrement de la séance — **S** (V1)
Option d'enregistrer l'audio (micro) ou les événements (MIDI) de la séance pour réécoute (EF-RES-10).

- CA1 : désactivé par défaut pour l'audio ; activable globalement ou par séance.
- CA2 : les enregistrements sont stockés localement et supprimables individuellement ou en masse.

### EF-ECO-10 — Tolérance de diapason — **S** (MVP, décision QO-23)
Un piano acoustique désaccordé globalement (ex. La à 435 Hz ou 446 Hz) ne doit pas provoquer de fausses erreurs. L'application signale un désaccord important et l'intègre dans l'analyse.

- CA1 : l'accord étiré (aigus trop hauts, graves trop bas par construction) est pris en compte par une tolérance plus large dans les deux octaves extrêmes ([06 §4.3](06-analyse-du-jeu-et-erreurs.md#43-cas-particuliers)).
- CA2 : la plage de diapason tolérée est étendue vers 430 Hz si POC-01 le permet ; au-delà, un conseil d'accordage est donné, sans bloquer.

---

## 7. Analyse et comparaison (ANA)

Les règles détaillées de l'analyse sont décrites dans [06 — Analyse du jeu et détection des erreurs](06-analyse-du-jeu-et-erreurs.md).

### EF-ANA-01 — Transcription du jeu — **M**
Le jeu est converti en une suite d'événements « note » : hauteur, instant de début, instant de fin, intensité. En MIDI, ces événements sont fournis par l'instrument ; au micro, ils sont estimés à partir du signal.

### EF-ANA-02 — Alignement avec la partition — **M**
Chaque note jouée est associée, si possible, à une note attendue de la partition, en tenant compte des erreurs, des hésitations et des variations de tempo.

- CA1 : une erreur locale (une note fausse, une note oubliée) ne décale pas l'alignement du reste du passage.
- CA2 : un passage recommencé (l'utilisateur revient en arrière) est détecté et aligné correctement.

### EF-ANA-03 — Erreurs de notes — **M**
Détection des notes : justes, fausses (mauvaise hauteur), manquées (attendues et non jouées), en trop (jouées et non attendues).

### EF-ANA-04 — Erreurs de rythme — **M**
En modes continu, boucle (EF-PRA-11) et suivi : détection des notes jouées en avance ou en retard au-delà d'une tolérance, par rapport au tempo de référence ou au tempo local du joueur. Au micro, tolérance effective et marques selon [06 §4.1](06-analyse-du-jeu-et-erreurs.md#41-niveaux-dexigence) (décision QO-32). Points d'orgue, césures et ralentis ou accélérations écrits : rythme non évalué ([06 §4.3](06-analyse-du-jeu-et-erreurs.md#43-cas-particuliers), décision QO-40).

### EF-ANA-05 — Tempo — **S**
Mesure du tempo effectif, de sa stabilité, et détection des accélérations ou ralentissements non écrits.

### EF-ANA-06 — Accords — **M**
Les notes d'un accord sont considérées simultanées si elles sont jouées dans une fenêtre de tolérance (par défaut 80 ms [HYP], réglable selon le niveau ; au micro, jamais sous 2σ, voir [06 §4.1](06-analyse-du-jeu-et-erreurs.md#41-niveaux-dexigence)). Les accords arpégés notés comme tels, et les accords de plus d'une octave dans une main, ont une tolérance adaptée ([06 §4.3](06-analyse-du-jeu-et-erreurs.md#43-cas-particuliers)).

### EF-ANA-07 — Attribution des mains — **S**
Chaque note jouée est attribuée à la main gauche ou droite (d'après la note attendue associée), pour permettre des scores et des retours par main.

### EF-ANA-08 — Tolérances par niveau — **S** (MVP sans les ornements, décision QO-24 c ; V1 : complète)
Les tolérances (rythme, simultanéité, ornements) dépendent d'un niveau d'exigence choisi : indulgent, standard, exigeant. Au MVP, les trois niveaux règlent le rythme, la simultanéité, l'hésitation et l'arrêt ; le niveau se choisit dans le profil et à chaque séance. Les ornements sont réglés par niveau en V1 (EF-ANA-09).

### EF-ANA-09 — Ornements — **S** (V1)
Les trilles, mordants, gruppettos et appogiatures sont évalués avec tolérance : nombre de notes variable, réalisation libre dans la durée prévue. Au MVP, les ornements ne sont pas évalués, selon la règle de [06 §4.3](06-analyse-du-jeu-et-erreurs.md#43-cas-particuliers).

### EF-ANA-10 — Reprises et sauts — **S** (MVP : reprises simples, décision QO-24 c ; V1 : complet)
L'analyse suit l'ordre de lecture effectif (reprises jouées ou non, selon le réglage). Au MVP, elle suit les reprises simples définies en EF-LEC-07 ; les sauts D.C., D.S., Fine et Coda sont suivis en V1.

### EF-ANA-11 — Dynamique — **C** (V2)
Comparaison des nuances écrites (pp à ff, crescendo, decrescendo) avec l'intensité jouée, en relatif. Fiable surtout en MIDI.

### EF-ANA-12 — Pédale — **C** (V2)
En MIDI uniquement : comparaison de l'usage de la pédale de sustain avec les indications de la partition.

### EF-ANA-13 — Temps réel et différé — **M**
- Le mode attente et le retour en direct exigent une analyse en temps réel (latence : voir ENF-PERF-01).
- Le compte-rendu peut utiliser une analyse plus complète a posteriori, qui affine le résultat du temps réel.
- En **mode attente** (décision QO-52) : le compte-rendu reprend les décisions prises pendant le jeu ; l'analyse différée, facultative dans ce mode, peut seulement rendre une décision « incertaine » (hors score), jamais créer une erreur.

- CA1 : en jeu continu, si l'analyse différée corrige une erreur signalée en direct, le compte-rendu fait foi.
- CA2 : en mode attente, le compte-rendu ne contient aucune erreur absente des décisions prises pendant le jeu ; une note validée pendant le jeu n'est jamais déclarée fausse a posteriori.

### EF-ANA-14 — Durée des notes — **C** (V2)
Détection des notes relâchées trop tôt ou tenues trop longtemps (legato, staccato). Fiable surtout en MIDI.

---

## 8. Restitution des erreurs (RES)

### EF-RES-01 — Retour en direct — **M**
Trois niveaux, choisis par l'utilisateur : aucun, discret (couleur des notes après passage du curseur), complet (signalement immédiat avec note jouée affichée). Ce réglage ne s'applique qu'au **jeu continu** ; en mode attente, l'erreur est toujours affichée (EF-RES-02).

- CA1 : le retour en direct ne gêne pas la lecture de la suite de la partition : marques seulement sur les notes déjà passées.

### EF-RES-02 — Retour du mode attente — **M**
Note fausse : la note jouée apparaît (en couleur d'erreur) à sa hauteur réelle sur la portée, la note attendue est mise en évidence. Option : indication textuelle (« trop haut d'un demi-ton », « oubli du dièse »).

### EF-RES-03 — Compte-rendu de fin — **M**
À la fin d'une séance, un écran de synthèse présente : scores, un point positif réel s'il existe, passages à retravailler (EF-RES-06), partition annotée, durée de la séance.

- CA1 : affiché en moins de 2 s après la fin du jeu (ENF-PERF-04).
- CA2 : au MVP, jusqu'à 3 passages à retravailler ; chacun est un lien « Voir sur la partition » qui ouvre la partition annotée à cet endroit, avec la plage déjà sélectionnée, prête à pratiquer (décision QO-24 a).

### EF-RES-04 — Partition annotée — **M**
Les notes de la partition sont colorées selon leur résultat. Les notes en trop et les notes fausses sont affichées à leur hauteur jouée. Les écarts de rythme sont représentés (ex. marque avant/après la note).

- CA1 : un code couleur accessible (ne reposant pas que sur la couleur : formes, symboles) — voir ENF-ACC-02 et le code de [10 §5](10-ecrans-et-ergonomie.md#5-code-couleur-et-symboles-des-résultats).
- CA2 : appui sur une note : détail (attendu, joué, écart en ms ou en fraction de temps).

### EF-RES-05 — Scores — **M**
Score global sur 100 et sous-scores : justesse des notes, rythme, continuité (absence d'arrêts et d'hésitations). Formules décrites en [06](06-analyse-du-jeu-et-erreurs.md#5-calcul-des-scores).

### EF-RES-06 — Passages prioritaires — **S** (MVP : forme minimale, décision QO-24 a ; V1 : complète)
Identification des 1 à 3 passages (groupes de mesures) qui concentrent les erreurs, avec le type d'erreur dominant.

- MVP : jusqu'à 3 passages de 1 à 4 mesures, classés selon [06 §7](06-analyse-du-jeu-et-erreurs.md#7-restitution--hiérarchisation), avec leur type d'erreur dominant et le lien « Voir sur la partition » (EF-RES-03 CA2).
- V1 : action suggérée et lancement direct du travail du passage (EF-RES-09), motifs regroupés.

### EF-RES-07 — Liste des erreurs — **S**
Liste chronologique filtrable (par type, par main), chaque entrée ouvrant la partition au bon endroit.

### EF-RES-08 — Comparaison — **S**
Comparaison avec la séance précédente et avec la meilleure séance sur le même passage : évolution des scores, erreurs corrigées, erreurs nouvelles.

### EF-RES-09 — Travailler ce passage — **S** (V1)
Depuis un passage signalé, lancement direct d'une boucle sur ce passage avec des réglages suggérés (tempo réduit, main concernée). Au MVP, le lien « Voir sur la partition » ouvre le passage déjà sélectionné (EF-RES-03 CA2).

### EF-RES-10 — Réécoute — **S**
Réécoute du jeu de l'utilisateur (audio ou MIDI) avec curseur synchronisé sur la partition ; possibilité d'alterner avec la version de référence.

### EF-RES-11 — Piano-roll comparatif — **C** (V2)
Vue « piano-roll » superposant notes attendues et notes jouées sur un axe temporel.

### EF-RES-12 — Ton du retour — **C** (V1)
Style de restitution : ludique (étoiles, encouragements, animations) ou sobre (chiffres, sans animation). Au MVP, un style unique, sobre et encourageant (décision QO-24 g, voir [10 §6](10-ecrans-et-ergonomie.md#6-contenus-et-ton)) ; le style ludique, avec tutoiement, arrive en V1.

### EF-RES-13 — Diagnostic pédagogique — **C**
Détection de motifs d'erreurs récurrents et formulation en langage clair : altération de l'armure oubliée systématiquement, erreur d'octave, confusion de clé (lecture en clé de sol d'une portée en clé de fa), précipitation sur les passages rapides, etc.

---

## 9. Progression et historique (PRO)

### EF-PRO-01 — Tableau de bord — **S** (V1)
Vue d'ensemble : temps de pratique (jour, semaine, mois), morceaux en cours, dernières séances, tendance.

### EF-PRO-02 — Historique des séances — **M**
Chaque séance est enregistrée : date, morceau, passage, mode, main(s), tempo, scores, erreurs. Les comptes-rendus passés restent consultables. Au MVP, le détail complet de toutes les séances est conservé ; la place occupée est mesurée en bêta avant de décider d'un compactage pour la V1 (décision QO-11).

### EF-PRO-03 — Évolution par morceau — **S**
Graphique de l'évolution des scores et du tempo atteint sur un morceau.

### EF-PRO-04 — Carte de chaleur — **S**
Sur la partition, superposition de la fréquence des erreurs par mesure sur les N dernières séances.

### EF-PRO-05 — Régularité — **S**
Série de jours consécutifs de pratique, objectif de temps quotidien paramétrable, rappel optionnel par notification.

### EF-PRO-06 — Objectif suggéré — **C**
Proposition d'un objectif pour la prochaine séance à partir de l'historique (passage, main, tempo).

### EF-PRO-07 — Vue parent — **C**
Résumé non technique pour un parent : jours et durée de pratique, morceaux, tendance. [QO-07]

### EF-PRO-08 — Gamification — **C** (V2)
Badges, niveaux, défis. Désactivable.

### EF-PRO-09 — Export — **C**
Export de l'historique (CSV) et d'un compte-rendu (PDF ou image) pour le partager, par exemple avec un professeur.

---

## 10. Compte et paramètres (CPT)

### EF-CPT-01 — Usage sans compte — **M**
L'application est entièrement utilisable sans compte, avec stockage local. [QO-03]

### EF-CPT-02 — Profil — **M**
Niveau (débutant, intermédiaire, avancé), type de piano, source de captation, niveau d'exigence (EF-ANA-08). Le style de retour (EF-RES-12) s'ajoute au profil en V1 (décision QO-24 g).

### EF-CPT-03 — Compte et sauvegarde — **S** (V1)
Création de compte (e-mail, Google ; Apple lors du portage iOS). Sauvegarde et restauration de la bibliothèque et de l'historique.

### EF-CPT-04 — Multi-profils — **S** (V1)
Plusieurs profils sur un appareil (famille), chacun avec sa bibliothèque et son historique, ou une bibliothèque partagée [QO-07].

### EF-CPT-05 — Paramètres — **M**
Langue (français et anglais au minimum ; anglais américain), notation des notes (latine ou anglo-saxonne), sons, thème, retour en direct par défaut, réglages audio.

- CA1 : français et anglais dès le MVP (décisions QO-16 et QO-24 f) ; par défaut la langue du système si elle est prise en charge, sinon l'anglais.
- CA2 : thème clair seul au MVP ; le choix du thème (sombre, contraste élevé, EF-VIS-10) arrive en V1 (décision QO-24 e).

### EF-CPT-06 — Suppression des données — **M**
Suppression de toutes les données locales et, le cas échéant, du compte et des données serveur, depuis l'application.

- CA1 : aucune donnée n'est restaurée ensuite par la sauvegarde automatique d'Android, désactivée (ENF-CONF-01, décision QO-36).

### EF-CPT-07 — Abonnement ou don — **C** (selon [QO-01])
Gestion des achats intégrés ou de l'abonnement via les mécanismes des stores, restauration des achats.

---

## 11. Espace professeur (PRF) — V2, **W** pour l'instant

| ID | Exigence |
|---|---|
| EF-PRF-01 | Créer une classe, inviter des élèves par code ou lien, gérer les élèves. |
| EF-PRF-02 | Envoyer une partition à un ou plusieurs élèves avec une consigne (passage, main, tempo cible, échéance) et des annotations. |
| EF-PRF-03 | Consulter les séances et comptes-rendus des élèves, avec leur accord. |
| EF-PRF-04 | Commenter une séance ou un passage. |
| EF-PRF-05 | Accord explicite de l'élève (ou de son responsable légal) pour le partage de ses données. |
