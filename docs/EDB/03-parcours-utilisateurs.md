# 03 — Parcours utilisateurs

Chaque parcours décrit un scénario de bout en bout. Les exigences correspondantes sont référencées entre crochets.

## P1 — Premier lancement

**Acteur :** nouvel utilisateur.
**Déclencheur :** installation de l'application.

1. L'utilisateur ouvre l'application. Un écran d'accueil présente la promesse en trois étapes : importer, écouter, jouer et être corrigé.
2. L'application lui demande son niveau (débutant, intermédiaire, avancé) et le type de piano (numérique avec câble ou Bluetooth, numérique sans connexion, acoustique). [EF-CPT-02]
3. Selon la réponse, elle propose le mode de captation : MIDI ou micro. [EF-ECO-01]
4. Si micro : demande d'autorisation du micro, avec explication claire de l'usage (traitement local, aucun enregistrement conservé par défaut). [EF-ECO-05]
5. Calibrage rapide : l'utilisateur joue quelques notes demandées pour vérifier la captation (et mesurer la latence si besoin). [EF-ECO-06]
6. L'application propose un **morceau de démonstration** du domaine public déjà intégré pour découvrir le fonctionnement sans avoir à importer. [EF-BIB-05]

**Résultat attendu :** l'utilisateur a joué un premier passage et vu un premier compte-rendu en moins de 5 minutes.

**Création de compte :** non obligatoire au premier lancement [QO-03].

## P2 — Importer une partition

**Acteur :** utilisateur.
**Déclencheur :** l'utilisateur dispose d'une partition (fichier ou papier).

### Variante A — fichier MusicXML ou MIDI

1. Depuis la bibliothèque, l'utilisateur appuie sur « Importer ».
2. Il choisit la source : fichiers du téléphone, partage depuis une autre application (mail, navigateur, gestionnaire de fichiers), stockage en ligne. [EF-IMP-01, EF-IMP-05]
3. L'application analyse le fichier, affiche un aperçu (titre, compositeur, nombre de mesures, portées détectées). [EF-IMP-06]
4. L'utilisateur confirme ou corrige les métadonnées. La partition rejoint la bibliothèque. [EF-BIB-01]

### Variante B — PDF ou photo (reconnaissance)

1. L'utilisateur choisit un PDF ou prend en photo les pages d'une partition papier. [EF-IMP-03, EF-IMP-04]
2. L'application recadre et redresse les photos, l'utilisateur ordonne les pages.
3. La reconnaissance optique s'exécute (localement ou sur serveur selon la décision [QO-04]). Une progression est affichée.
4. Le résultat est présenté **en vis-à-vis de l'original** avec les zones à faible confiance surlignées. [EF-IMP-07]
5. L'utilisateur peut corriger les erreurs simples (hauteur d'une note, altération, durée) ou accepter le résultat en l'état. [EF-IMP-08]
6. La partition rejoint la bibliothèque, marquée « issue de reconnaissance ».

**Cas d'erreur :**
- Fichier corrompu ou format non pris en charge : message explicite, lien vers les formats acceptés.
- Reconnaissance très dégradée : l'application le signale et déconseille l'analyse du jeu sur cette partition tant qu'elle n'est pas corrigée.

## P3 — Découvrir un morceau (écouter et regarder)

1. L'utilisateur ouvre une partition. Elle s'affiche en mode page ou en défilement continu. [EF-VIS-01, EF-VIS-02]
2. Il appuie sur Lecture. Le morceau est joué avec un son de piano ; un curseur suit la lecture et les notes jouées sont mises en évidence. [EF-LEC-01, EF-VIS-04]
3. Il réduit le tempo à 60 %, coupe la main gauche pour n'entendre que la main droite. [EF-LEC-03, EF-LEC-04]
4. Il active le clavier virtuel en bas de l'écran pour voir quelles touches sont jouées. [EF-VIS-07]
5. Il sélectionne les mesures 9 à 12 et les fait jouer en boucle. [EF-LEC-06]

## P4 — Pratiquer en mode attente (débutant)

**Acteur :** Claire, piano numérique connecté en USB.

1. Claire ouvre son morceau et choisit « Pratiquer », puis le mode « Attente », main droite seule. [EF-PRA-02]
2. Le curseur se place sur la première note. L'application attend.
3. Claire joue la note : si elle est juste, le curseur avance. Si elle est fausse, la note jouée s'affiche en couleur d'erreur à sa hauteur réelle, avec un symbole, la note attendue est entourée d'un halo, le curseur reste en place. [EF-RES-02]
4. Pour les accords, l'application attend que toutes les notes de l'accord soient enfoncées (dans une fenêtre de tolérance). [EF-ANA-06]
5. En fin de passage, un mini-compte-rendu indique le nombre d'erreurs et les notes les plus souvent ratées.

**Remarque :** le mode attente ne juge pas le rythme, seulement les hauteurs.

## P5 — Jouer en continu et obtenir un compte-rendu

**Acteur :** Lucas, piano acoustique, tablette posée sur le pupitre, captation micro.

1. Lucas choisit « Pratiquer », mode « Jeu continu », les deux mains, tempo 80 %. [EF-PRA-03]
2. Un décompte au métronome (une mesure) lance le jeu. Le curseur avance au tempo choisi. [EF-PRA-05]
3. Lucas joue. Selon le réglage, les erreurs s'affichent en direct (léger, non bloquant) ou seulement à la fin. [EF-RES-01]
4. Lucas s'arrête au milieu. Après quelques secondes sans jouer, l'application met la séance en pause, curseur sur la dernière note jouée ; Lucas reprend simplement en rejouant, sans toucher l'écran (un décompte relance le jeu au début de la mesure précédente). Sans reprise, la séance se termine au bout de 30 s. [EF-PRA-08]
5. Le compte-rendu s'affiche : [EF-RES-03 à EF-RES-08]
   - score global et sous-scores (notes, rythme, continuité) ;
   - partition colorée : notes justes, fausses, manquées, en trop, en avance, en retard ;
   - les trois passages à retravailler en priorité ;
   - comparaison avec la séance précédente (V1).
6. Lucas appuie sur un passage signalé : la partition annotée s'ouvre sur ce passage, déjà sélectionné, prêt à pratiquer (MVP) ; en V1, l'application propose directement de le travailler en boucle, au tempo réduit. [EF-RES-03, EF-RES-09]
7. Lucas peut réécouter son propre jeu, comparé à la version de référence [EF-RES-10] (si l'enregistrement est activé).

## P6 — Travailler un passage difficile

**Acteur :** Marc.

1. Marc sélectionne les mesures 33 à 40.
2. Il active le mode « Boucle progressive » : tempo de départ 60 %, cible 100 %, +5 % à chaque passage réussi au-dessus d'un seuil de 90 %. [EF-PRA-06]
3. L'application enchaîne les boucles avec décompte, analyse chaque passage, et monte le tempo quand le seuil est atteint.
4. En fin de séance, un graphique montre le score par boucle et le tempo atteint.

## P7 — Suivre sa progression

1. L'utilisateur ouvre l'onglet « Progression ». [EF-PRO-01]
2. Il voit : temps de pratique par jour et par semaine, morceaux travaillés, évolution du score par morceau.
3. Sur un morceau, une **carte de chaleur** de la partition montre les mesures où les erreurs se concentrent historiquement. [EF-PRO-04]
4. L'application propose un objectif pour la prochaine séance (ex. « mesures 17-24, main gauche, tempo 70 % »). [EF-PRO-06]

## P8 — Suivi parental (Should)

1. Nadia ouvre l'application (même appareil, profil parent, ou appareil distinct selon [QO-07]).
2. Elle voit un résumé simple : jours de pratique, durée, morceaux travaillés, tendance (progresse, stable, régresse). [EF-PRO-07]
3. Aucune connaissance musicale n'est nécessaire pour comprendre ce résumé.

## P9 — Partage avec le professeur (V2)

1. Sophie crée une « classe » et invite Lucas par un code ou un lien. [EF-PRF-01]
2. Elle lui envoie une partition annotée avec une consigne : « mesures 1-16, main droite, tempo 72 à la noire ». [EF-PRF-02]
3. Lucas voit le devoir dans son application et le travaille.
4. Sophie consulte les comptes-rendus de pratique de Lucas avant le cours suivant. [EF-PRF-03]
