# Revue du PRD v0.1 — point de vue musical et pédagogique

| Élément | Valeur |
|---|---|
| Relecteur | `pedagogue-piano` |
| Date | 2026-11-07 |
| Document relu | PRD v0.1 (`docs/PRD/`, tous les fichiers), en particulier [03](../03-user-stories-mvp.md) et [08](../08-questions-ouvertes.md) |
| Références | EDB v0.4 : [02](../../EDB/02-utilisateurs-et-personas.md), [03](../../EDB/03-parcours-utilisateurs.md), [05](../../EDB/05-exigences-fonctionnelles.md), [06](../../EDB/06-analyse-du-jeu-et-erreurs.md), [10](../../EDB/10-ecrans-et-ergonomie.md), [14](../../EDB/14-glossaire.md) |
| Hors périmètre | Les 11 incohérences de l'EDB listées dans le [README du PRD](../README.md) ne sont pas répétées ici. |

Convention : les numéros d'octave suivent la notation française quand ils sont nécessaires (do3 = do central, la3 = 440 Hz) ; les mesures citées sont celles des éditions courantes et doivent être revérifiées sur la gravure retenue.

---

## 1. Verdict global

**Prêt avec corrections.**

Le PRD reprend fidèlement les règles de l'EDB et applique bien le principe « une fausse erreur coûte plus cher qu'une erreur non signalée ». Plusieurs choix du PO sont musicalement justes et je les valide : ornements non évalués au MVP, ordre des arpèges non sanctionné, « avancé → standard », note en trop non bloquante en mode attente (avec une limite), rythme non évalué en mode attente, sauts D.C. / D.S. exclus du MVP et signalés à l'import.

Il manque en revanche des règles sur des situations que l'on rencontre **dans presque chaque morceau** : points d'orgue et ritardandos, reprise après un arrêt, appogiatures classiques, ornements qui commencent par la note supérieure, et note juste non reconnue au micro en mode attente. Sans ces règles, le jeu continu en MIDI produira des fausses erreurs de rythme systématiques, et le mode attente au micro pourra bloquer un élève qui joue juste. Ces quatre points sont **bloquants avant l'incrément I2** (I3 pour le micro). Ils demandent d'ajouter des critères d'acceptation, pas de réorganiser le backlog.

Les autres corrections portent sur des définitions et des contenus attendus de moi : reprises simples, numérotation des mesures, tempos par défaut, orthographe des notes d'un MIDI, formulations des erreurs, morceaux de démonstration. Je les fournis en annexe, prêts à reprendre dans les stories.

| Gravité | Nombre |
|---|:-:|
| Bloquant | 4 |
| Majeur | 18 |
| Mineur | 18 |
| **Total** | **40** |

---

## 2. Tableau des remarques

### 2.1 Bloquants

| ID | Gravité | Référence | Constat | Correction proposée |
|---|---|---|---|---|
| PED-01 | Bloquant | 03, US-ANA-05 et US-ANA-07 (cas particuliers) ; EDB 06 §4.2 | Aucune règle pour les **points d'orgue**, les **césures**, ni les passages sous **rit. / rall. / allarg. / accel.** En jeu continu, le curseur avance sur la grille du tempo : un élève qui tient correctement le point d'orgue d'une fin de phrase (ex. Schumann, *Choral* op. 68 n°4, un point d'orgue à chaque fin de phrase) ou qui ralentit sur un « rit. » écrit dans les dernières mesures recevra des « en retard » sur toutes les notes suivantes. C'est une source **systématique** de fausses erreurs en MIDI, incompatible avec la cible de 1 %. | Ajouter à US-ANA-07 CA3 : « **Point d'orgue, césure** : le curseur attend l'attaque suivante (comme en mode attente pour cet événement) ; la référence temporelle repart de cette attaque ; l'événement qui suit n'est pas évalué en rythme. **Rit., rall., allarg., accel.** (jusqu'au prochain « a tempo » ou changement de tempo) : le rythme n'est pas évalué au MVP, seules les hauteurs le sont ; le curseur suit le joueur (recalage par l'alignement). **Dernier événement du passage** : seule l'attaque est évaluée. » Ajouter un cas de test par indication dans le corpus (US-QUA-01). |
| PED-02 | Bloquant | 03, US-ANA-05 CA2-CA3, US-PRA-04 ; EDB 06 §3.2 et §4.2 | (a) L'hésitation et l'arrêt sont décrits comme des « pauses » ou des « interruptions », sans préciser qu'ils se mesurent **par rapport à l'instant attendu**. Or une ronde à ♩ = 50 dure 4,8 s : un silence écrit ou une note tenue n'est pas un arrêt. (b) Seul le **décalage global** constant est compensé. Si l'élève s'arrête 3 s mesure 9 puis repart parfaitement, toutes les notes des mesures 9 à 16 seront « en retard ». (c) Après un retour en arrière, rien ne dit que le **curseur** suit l'élève. En jeu continu, il continue alors sur la grille et l'élève perd sa place. | US-ANA-05 : « L'hésitation et l'arrêt sont mesurés comme le **retard de l'attaque sur son instant attendu**, après la durée écrite de l'événement précédent (notes tenues et silences compris). Après chaque hésitation, arrêt ou retour en arrière, **la référence temporelle et le curseur se recalent sur la position du joueur** : les notes suivantes sont jugées par rapport à cette nouvelle origine. » Au micro, **« silence » signifie « aucune nouvelle attaque »** et non « absence de son » : avec la pédale, un piano droit résonne plusieurs secondes après l'arrêt (ex. Satie, *Gymnopédie n°1*). Cas de test : un arrêt de 3 s suivi d'un jeu parfait ne produit aucun « en retard » après la reprise. |
| PED-03 | Bloquant | 03, US-PRA-02 CA3 | La règle « une note en trop jouée en même temps que la bonne n'empêche pas d'avancer » est juste pour un **accrochage** (le pouce frôle une touche voisine). Mais telle qu'elle est écrite, un enfant qui pose la main à plat sur do-ré-mi-fa-sol pour « trouver » le mi fait avancer le curseur à chaque fois. Le mode attente perd alors tout son sens pédagogique. | Remplacer CA3 par : « Une note en trop jouée avec la ou les notes attendues n'empêche pas d'avancer **si elle est à un ou deux demi-tons d'une note attendue et s'il n'y en a pas plus d'une par main** ; elle est comptée « en trop ». Au-delà (plusieurs notes en trop, ou note éloignée), l'événement n'est pas validé et l'indication « Jouez seulement la ou les notes indiquées » s'affiche. » Cas de test : mi attendu ; mi + fa → avance (fa en trop) ; do-ré-mi-fa-sol → n'avance pas. |
| PED-04 | Bloquant | 03, US-ECO-07 CA4, US-PRA-02 ; US-CPT-06 | « Détection incertaine → l'application attend » protège contre les fausses erreurs, mais rien ne prévoit le cas inverse : l'élève **joue juste** et le micro ne reconnaît pas la note. Exemples : basse très grave (octaves graves de la fin du Prélude op. 28 n°20 de Chopin), note répétée sous la pédale (« Au clair de la lune », do-do-do). Le curseur reste bloqué, l'élève rejoue trois, quatre, cinq fois, puis conclut qu'il se trompe ou que l'application ne marche pas. C'est la pire expérience possible, et elle peut survenir dès le premier passage guidé (US-CPT-06). De plus, l'**erreur d'octave** est l'erreur typique de la détection de hauteur au micro (harmoniques), ce qui risque des « fausses notes » injustes en mode attente. | Ajouter à US-ECO-07 : « **Bénéfice du doute** : si la même attaque incertaine compatible avec la note attendue est détectée 2 fois de suite, ou après 2 attaques incertaines dans les 5 s, le curseur avance, la note est marquée « incertaine » (mode détaillé) et n'entre pas dans le score. » Et : « Au micro, une **erreur d'octave** n'est signalée en mode attente qu'avec un seuil de confiance renforcé (à fixer avec `expert-audio-mir`) ; en dessous, l'application attend. » Compléter US-RES-08 (voir PED-36). |

### 2.2 Majeurs

| ID | Gravité | Référence | Constat | Correction proposée |
|---|---|---|---|---|
| PED-05 | Majeur | 03, US-PRA-05 CA3 | « Le décompte se termine exactement sur le temps de l'anacrouse » se comprend de deux façons. La mauvaise (« un-deux-trois-quatre » puis l'anacrouse jouée sur « un ») décale tout le morceau d'un temps. | Réécrire CA3 : « Avec une anacrouse, le décompte compte les mesures complètes demandées **puis continue sur les temps qui précèdent l'anacrouse** dans la mesure incomplète ; la première note tombe à sa place dans la mesure, jamais sur un « 1 » ajouté. » Exemples de test : Brahms, *Berceuse* op. 49 n°4 (3/4, anacrouse de deux croches sur le 3e temps) → « 1-2-3 \| 1-2 » puis jeu sur « 3 » ; Beethoven, *Für Elise* (3/8, anacrouse de deux doubles croches) → « 1-2-3 \| 1-2 » puis jeu sur « 3 » ; anacrouse sur une subdivision (« 4-et ») → subdivision sonore ou visuelle sur le dernier temps du décompte. Voir aussi PED-25 (décompte minimal). |
| PED-06 | Majeur | 03, US-LEC-07 et US-ANA-01 | Les « reprises simples » ne couvrent pas des cas courants : barre de reprise **sans barre de début**, reprise qui revient à l'**anacrouse**, fins « 1.-3. / 4. ». L'option « ignorer les reprises » ne dit pas qu'il faut alors **sauter la 1re fin**. Le compte-rendu ne dit pas à quel passage (1re ou 2e fois) une erreur s'est produite. | Reprendre la définition de l'[annexe A.1](#a1-définition-des-reprises-simples-mvp) dans US-LEC-07 CA1 et CA3. Ajouter à US-RES-03 : « Une erreur dans une section reprise est localisée par sa mesure et, si elle ne se produit qu'à l'un des passages, par « (1re fois) » ou « (2e fois) ». » Exemple : *Menuet en sol* BWV Anh. 114, fa♯ juste au premier passage, oublié au second. |
| PED-07 | Majeur | 03, US-VIS-06 CA5 et notes | La numérotation des mesures dans les cas d'anacrouse, de 1re et 2e fins et de mesures coupées par une barre de reprise n'est pas définie. Or ces numéros servent dans « Aller à la mesure », la sélection, le compte-rendu et l'historique. | Reprendre l'[annexe A.2](#a2-numérotation-des-mesures) : les numéros sont ceux du fichier, sans renumérotation, et sont traités comme du texte (« 8a » existe). L'anacrouse n'a pas de numéro affiché (mesure 0) ; elle s'appelle « anacrouse » dans les textes. Si le fichier ne donne pas de numéros, ils sont calculés selon la convention de l'annexe. |
| PED-08 | Majeur | 03, US-LEC-03 CA5 | La table des tempos par défaut est attendue de moi. Le PRD ne prévoit le cas « sans tempo » que selon le chiffrage, alors que beaucoup de MusicXML portent une **indication textuelle** (« Allegro », « Lent ») sans valeur métronomique. Un BPM sans unité est ambigu en 6/8 (noire pointée ou croche ?). Enfin, l'élève ne sait pas que le « 100 % » est une estimation. | Reprendre l'[annexe B](#annexe-b--tempos-par-défaut-us-lec-03-ca5) (indication textuelle d'abord, chiffrage ensuite, unité de pulsation affichée : « ♩. = 54 »). Ajouter : « Un tempo estimé est affiché comme tel (« tempo non indiqué, estimé à ♩ = 80 ») ; l'utilisateur peut fixer le **tempo de référence** du morceau (celui que demande le professeur), mémorisé sur la fiche. » |
| PED-09 | Majeur | 03, US-IMP-02 (notes) | (a) « Orthographe déduite de l'armure » ne suffit pas : un MIDI n'a souvent pas d'armure, et même avec une armure, les notes chromatiques et la sensible des tons mineurs restent ambiguës. Dans *Für Elise* (la mineur), un mi♭ affiché à la place du ré♯ rend fausse toute indication du type « oubli du dièse ». (b) **Anacrouse** : un MIDI qui commence au tick 0 par l'anacrouse décale toutes les barres de mesure (*Für Elise* afficherait « mi-ré♯-mi-ré♯-mi-si \| ré-do-la… »), ce qui fausse aussi tous les numéros de mesure. | (a) Reprendre les règles de l'[annexe C](#annexe-c--orthographe-des-notes-issues-dun-midi-us-imp-02). (b) Ajouter un CA à US-IMP-02 : « L'aperçu permet d'indiquer une anacrouse (durée) ou de décaler les barres de mesure ; si le premier temps fort semble mal placé, le rapport d'import le signale (« peut fausser l'analyse »). » |
| PED-10 | Majeur | 03, US-ANA-07 CA3 (trilles, mordants, gruppettos) | Je valide la non-évaluation des ornements au MVP (les professeurs font souvent omettre aux débutants les ornements ajoutés par les éditeurs). Mais la règle ne dit pas ce qui est **attendu** : la note principale reste-t-elle exigée ? En mode attente, un trille baroque ou classique qui **commence par la note supérieure** (règle d'époque, ex. trilles de cadence des menuets du *Petit livre d'Anna Magdalena Bach*), ou un gruppetto (supérieure-principale-inférieure-principale), produirait une « fausse note » sur la première note jouée. | Préciser : « Un événement orné est validé (attente) ou juste (continu) si l'on entend, dans sa durée écrite, **la note principale ou sa voisine supérieure ou inférieure diatonique** ; les autres notes de l'ornement, y compris les notes de terminaison écrites en petites notes après un trille, ne sont ni fausses ni en trop ; un ornement omis (note principale jouée seule) n'est pas une erreur. » Cas de test : trille commencé par la note supérieure en mode attente → avance sans erreur. |
| PED-11 | Majeur | 03, US-ANA-07 CA3 (petites notes, appogiatures) | Une **appogiature longue** du style classique (Mozart, Haydn) se joue **sur le temps** et prend la moitié de la valeur de la note principale (ou les deux tiers sur une note pointée). Celle-ci est donc jouée plus tard que son instant écrit : en jeu continu, elle sera « en retard ». À l'inverse, l'acciaccature (petite note barrée) se joue avant le temps ou sur le temps selon le style. Compter son absence comme « erreur mineure » dans le score pénalise une omission que beaucoup de professeurs autorisent. | « Après une petite note, la note principale est jugée avec une tolérance rythmique **élargie jusqu'à la moitié de sa durée** (pas d'« en retard » dans cette fenêtre). Une petite note absente est signalée dans la partition annotée mais **n'entre pas dans le score** au MVP. En mode attente, une petite note jouée en même temps que la note principale (acciaccature écrasée) est acceptée. » |
| PED-12 | Majeur | 03, US-ANA-06 CA4 et notes ; EDB 06 §4.3 | Je **valide** le non-sanctionnement de l'ordre des notes d'un arpège : il existe des arpèges descendants (flèche vers le bas), les pianistes arpègent souvent les deux mains ensemble, et beaucoup jouent les accords marqués arpégés plaqués quand l'accord tient sous la main. Mais la « fenêtre élargie » n'a pas de valeur. Rien ne dit non plus quelle note sert de référence rythmique, ni ce qui se passe pour les accords **plus larges qu'une octave dans une main**, qu'une petite main (Lucas, 11 ans) doit arpéger même sans signe. | « Fenêtre d'un accord arpégé = max(fenêtre du niveau ; 60 ms × (nombre de notes − 1)), plafonnée à la moitié de la durée écrite de l'accord. La référence rythmique est la **première** note entendue ; les autres ne sont pas évaluées en rythme. Un accord marqué arpégé joué plaqué est juste. Un accord dont l'écart dépasse l'octave dans une main reçoit automatiquement la fenêtre d'arpège. » Cas de test : Chopin, Prélude op. 28 n°7, mes. 12 (grand accord arpégé sur les deux mains, Andantino). |
| PED-13 | Majeur | 03, US-ANA-04 CA2-CA3 et US-RES-02 CA2 ; EDB 06 §3.1 | Avec la définition actuelle, une **altération reportée** au-delà de la barre de mesure ou un **bécarre oublié** sont classés « touche voisine » ou mal formulés, alors que ce sont les erreurs de lecture les plus fréquentes chez les débutants. Exemple : *Für Elise*, mes. 1, ré♯ puis ré♮ dans la même mesure ; l'élève qui rejoue ré♯ a oublié le bécarre. « Oubli du dièse » est faux quand l'altération attendue est un bémol ou un bécarre. Enfin, le libellé « touche voisine » suppose une cause (doigt qui glisse), alors qu'un écart d'un degré est souvent une **erreur de lecture** (sol lu à la place de la). | Ajouter aux sous-types d'« erreur d'altération » : altération omise (armure ou accidentelle), **bécarre omis**, **altération reportée** (valable seulement jusqu'à la barre), altération ajoutée. Principe de formulation au MVP : **décrire ce qui a été joué et ce qui était attendu, sans supposer la cause**, sauf pour l'altération et l'octave. Reprendre les formulations de l'[annexe D.1](#d1-indications-du-mode-attente-us-res-02). |
| PED-14 | Majeur | 03, US-RES-06 CA1 et US-PRA-02 CA4 | En mode attente, rien ne dit comment compter une note **jouée fausse puis corrigée**. Si l'essai faux compte comme « fausse » **et** comme « en trop », l'erreur est comptée deux fois. Si la note finalement trouvée compte comme « juste », le score vaut toujours 100 %. Les scores ne seraient alors ni stables ni comparables d'une séance à l'autre. | « En mode attente, une note attendue est « juste du premier coup » ou « trouvée après correction ». S_notes = notes justes du premier coup / notes attendues. Les essais faux ne sont pas comptés en plus comme « en trop ». Le compte-rendu affiche « 38 notes sur 42 trouvées du premier coup » et les notes le plus souvent corrigées. » |
| PED-15 | Majeur | 03, US-RES-06 CA1 et CA3 ; formule de l'EDB 06 §5.4 | Avec la formule pondérée, une séance avec **15 % de fausses notes** jouée en mesure et sans arrêt obtient 0,55 × 85 + 0,30 × 100 + 0,15 × 100 ≈ **92 / 100**. Aucun professeur ne dirait « 92 » à un élève qui ne connaît pas encore ses notes ; l'élève croira le morceau acquis. Le rythme n'étant calculé que sur les notes justes, il ne peut pas compenser cet effet. Le score de continuité n'est pas rapporté à la longueur du passage (voir PED-40). | Proposer au porteur (l'EDB qualifie ses formules de « propositions ») : **S_global = S_notes × (0,55 + 0,30 × S_rythme/100 + 0,15 × S_continuité/100)**. Le score global ne dépasse jamais le score de notes : 15 % de fausses notes donnent 85, et des notes parfaites avec un rythme à 50 donnent 85. Remplacer le CA3 « 5 séances types » par la batterie de l'[annexe E](#annexe-e--séances-types-pour-valider-les-scores-us-res-06-ca3), avec le classement attendu. |
| PED-16 | Majeur | 03, US-RES-03 CA2-CA3 et CA5 | (a) Le classement de gravité (« manquée et fausse > rythme > en trop > rejouée ») oublie les **arrêts**. Pour un professeur, l'endroit où l'élève s'arrête est l'indicateur le plus fiable d'un passage non su (ex. arrêt à chaque fois avant un changement de position). (b) « Nombre d'erreurs par type » arrive tôt dans l'écran, et aucun point positif n'est montré au MVP, alors qu'un passage sans erreur dans la séance peut être mis en avant sans historique. (c) Des libellés comme « rejouée » sont obscurs pour un élève. | (a) « Arrêt ≥ fausse ; hésitation = rythme ». (b) Ordre : score, **un point positif de la séance** (« Mesures 1 à 4 : aucune erreur »), passages à retravailler, puis le détail chiffré dans une section repliée. (c) Libellés de l'[annexe D.2](#d2-libellés-du-compte-rendu-us-res-03). |
| PED-17 | Majeur | 03, US-PRA-08 CA2 ; QO-25 | (a) La suspension par le silence demande ensuite de **toucher l'écran** pour reprendre, ce qui contredit « mains sur le clavier » (EDB 10 §1). (b) Rien n'exclut le **mode attente**, où une débutante qui lit mal la clé de fa (Claire) peut chercher une note pendant 10 à 20 s : la séance ne doit pas se suspendre dans ce cas. | « En jeu continu, après le délai de silence, la séance passe en **pause** (curseur figé sur la dernière position jouée, message « En pause — reprenez quand vous voulez ») ; **jouer reprend la séance** à l'endroit joué (alignement), et l'interruption compte pour un arrêt. La séance se termine automatiquement après 30 s de pause (réglable). En mode attente, aucune suspension avant 2 min de silence ; au-delà, le micro se ferme et la reprise demande un appui. » Valeurs : voir [§3.1](#31-qo-25--délai-de-silence-et-seuil-darrêt). |
| PED-18 | Majeur | 03, US-PRA-03 CA2 | « Les notes de l'autre main qui tombent sur l'événement validé sont jouées » ne règle pas le cas fréquent où l'autre main joue **entre** deux événements de la main travaillée. Mozart, Sonate K. 545, mes. 1 : la main droite a une blanche (do) puis deux noires, la main gauche une basse d'Alberti en croches. Quand Claire travaille la main droite, trois croches de la main gauche tombent pendant la blanche. | « Après validation d'un événement, l'application joue les notes de l'autre main **au tempo de la séance** jusqu'à l'instant du prochain événement de la main travaillée, puis attend. » |
| PED-19 | Majeur | 03, US-ECO-06 CA1 (et US-ECO-05 CA1 pour la latence micro) | Mesurer la latence en demandant de jouer sur des clics mesure surtout **le joueur** : chacun anticipe ou retarde naturellement le clic d'un métronome, de façon constante, d'environ 20 à 50 ms (anticipation bien connue en synchronisation sensorimotrice). En USB, la latence matérielle est de quelques millisecondes : la « compensation » retirerait donc l'habitude rythmique du joueur au lieu d'un défaut du matériel. Le test CA4, fait avec des entrées simulées, ne peut pas révéler ce biais. | « En MIDI USB, aucune compensation par défaut ; le décalage constant du joueur est déjà neutralisé par la compensation du décalage global (US-ANA-05 CA2). Le test au clic reste une option avancée, et sa valeur est bornée (au plus 30 ms, à confirmer en bêta). » Même précaution pour la latence micro : la mesurer sur un son connu (son émis et capté), pas sur le jeu au clic. |
| PED-20 | Majeur | 03, US-CPT-04 CA3, US-RES-05 CA1, US-IMP-02 CA4, US-ECO-05 | La **numérotation des octaves** diffère selon les conventions : en France, le do central se nomme do3 (la3 = 440 Hz), en notation anglo-saxonne C4 (A4 = 440 Hz). Une consigne de calibrage « jouez le la3 » ou un détail « joué : do4 » sera compris avec une octave d'écart par la moitié des utilisateurs. | « Les textes destinés à l'élève n'utilisent pas de numéro d'octave ; ils disent « le do du milieu du clavier », « une octave plus grave » et montrent la touche sur un petit clavier. Si un numéro est indispensable (mode détaillé), il suit la notation choisie : latine → do3 = do central ; anglo-saxonne → C4. » |
| PED-21 | Majeur | 03, US-QUA-01 et US-QUA-02 | (a) Le protocole d'annotation n'est pas défini. Annoter après coup des erreurs volontaires est peu fiable : il vaut mieux des **erreurs scénarisées** à l'avance. (b) Un « jeu correct » ne sert de référence en jeu continu que s'il est **joué au métronome**. Les jeux de données publics (MAESTRO) sont des interprétations expressives avec rubato : utilisés pour mesurer les fausses erreurs de rythme, ils fausseraient le banc. (c) Les cas particuliers à couvrir ne sont pas reliés à des pièces. | Reprendre l'[annexe F](#annexe-f--corpus-de-test--annotations-et-pièces-us-qua-01). Ajouter à US-QUA-02 CA2 : « Les jeux de données expressifs servent aux métriques de transcription et d'alignement, pas au taux de fausses erreurs de rythme. » |
| PED-22 | Majeur | 03, US-BIB-06 CA1 et CA3 ; QO-31 | Les critères oublient : un morceau **à la main gauche en clé de fa** (Claire lit mal cette clé), des pièces **courtes** (le parcours P1 doit tenir en 5 min), une **indication métronomique** écrite (sinon l'annexe B s'applique), des **doigtés** pour les pièces débutantes, l'absence de **D.C.** (non suivi au MVP) et de points d'orgue tant que PED-01 n'est pas traité, et une gravure **sans ornements ajoutés par les éditeurs**. | Ajouter ces critères au CA3. Liste proposée : [§3.2](#32-qo-31-et-us-bib-06--morceaux-de-démonstration). |

### 2.3 Mineurs

| ID | Gravité | Référence | Constat | Correction proposée |
|---|---|---|---|---|
| PED-23 | Mineur | 03, US-CPT-02 CA3 | **Je valide « avancé → standard ».** Un pianiste avancé qui déchiffre une nouvelle pièce n'a pas besoin du niveau exigeant, et ce niveau est fragile au micro (QO-32). En revanche, un élève sait mal se situer sans repère : Lucas (cycle 1, 4e année) peut aussi bien se dire « débutant » qu'« intermédiaire ». | Ajouter une phrase repère sous chaque niveau du questionnaire : **Débutant** : « je joue des morceaux de méthode, souvent mains séparées » ; **Intermédiaire** : « je joue des menuets de Bach, des sonatines de Clementi, l'*Album pour la jeunesse* de Schumann » ; **Avancé** : « je joue des inventions de Bach, des sonates de Mozart, du Chopin ou du Debussy ». Correspondance indicative avec le conservatoire : cycle 1 → débutant ou intermédiaire, cycle 2 → intermédiaire, cycle 3 → avancé. |
| PED-24 | Mineur | 03, US-CPT-03 CA2 et CA4, US-PRA-01 | Le niveau d'exigence dépend de **l'état du morceau** autant que de l'élève : Marc déchiffre un nocturne (indulgent) et peaufine une invention de Bach (exigeant). Selon CA4, le niveau n'est que « visible » dans la préparation de séance. | Rendre le niveau **modifiable** dans la préparation de séance (mémorisé par morceau, comme les autres réglages). Phrases proposées : **Indulgent** : « Pour apprendre un morceau : seules les erreurs nettes sont relevées. » **Standard** : « Pour consolider : notes, rythme et accords vérifiés avec une précision normale. » **Exigeant** : « Pour peaufiner avant un cours ou une audition : le moindre décalage est relevé. » |
| PED-25 | Mineur | 03, US-LEC-03 CA1, US-LEC-05, US-PRA-05 CA1 | (a) À 25 % de ♩ = 60, le métronome bat à 15 par minute : 4 s entre deux clics, impossible de garder la pulsation. (b) L'unité de battement n'est pas définie selon le chiffrage. (c) Un décompte d'une mesure en 2/4, en 6/8 battu à deux ou en 3/8 battu à un ne donne qu'un ou deux clics, insuffisant pour prendre le tempo. | (a) « Sous 40 battements par minute, la subdivision est activée automatiquement. » (b) Unité de battement : celle de l'[annexe B](#annexe-b--tempos-par-défaut-us-lec-03-ca5). (c) « Le décompte dure au moins 3 pulsations : en 2/4, 6/8 ou 3/8, il dure 2 mesures par défaut. » |
| PED-26 | Mineur | 03, US-PRA-07 CA1 | En jeu continu, la séance se termine quand le dernier événement est « dépassé ». Le compte-rendu risque de s'afficher pendant que l'élève **tient encore l'accord final**, ce qui coupe la fin musicale. | « …se termine après la **durée écrite** du dernier événement plus 1 s, ou dès le relâchement des touches en MIDI. » |
| PED-27 | Mineur | 03, US-PRA-06 CA2 | Reprendre au début de la mesure en cours est souvent trop tard : un musicien reprend un peu avant, en général au début de la mesure précédente ou de la phrase. | Ajouter l'option « reprendre une mesure avant » (proposée par défaut en jeu continu). |
| PED-28 | Mineur | 03, US-RES-05 CA1 | « Écart de début en ms » ne dit rien à un élève. | Afficher d'abord une **valeur musicale** (« un peu en retard : environ une double croche »), les millisecondes en second, en petit. |
| PED-29 | Mineur | 03, US-ANA-01 CA1-CA3, US-ANA-07 | (a) Quand deux voix ou les deux mains se rejoignent sur la **même touche** (alto et ténor à l'unisson dans un choral), une seule attaque est possible ; si deux attaques sont attendues, l'élève aura une note « manquée ». (b) Les liaisons « laissez vibrer » (l.v.) n'ont pas de note d'arrivée. | « Un unisson entre voix ou entre mains n'attend qu'une attaque. Une liaison « laissez vibrer » est ignorée par l'analyse. » |
| PED-30 | Mineur | 03, US-ANA-05 CA1 et CA3 | Une note jouée après une hésitation est à la fois « en retard » et précédée d'une « hésitation » : une seule faute est comptée deux fois. | « La note qui suit une hésitation ou un arrêt n'est pas comptée en plus comme « en retard » (voir aussi le recalage de PED-02). » |
| PED-31 | Mineur | 03, US-IMP-02 CA4 | Séparer les mains au do central se trompe dans la **position de do central** des méthodes pour débutants : les deux pouces partagent le do central, et le do de la main gauche est attribué à la main droite. | Garder le do central par défaut (le point est modifiable), mais attribuer les notes à la main la plus proche de la note précédente de chaque main quand c'est possible ; ajouter ce cas au corpus d'import. |
| PED-32 | Mineur | 03, US-ECO-09 CA1-CA2 | Un piano est accordé **étiré** : par construction, l'aigu est trop haut et le grave trop bas par rapport au tempérament égal (jusqu'à environ ± 20 à 30 cents dans les octaves extrêmes). Un piano droit non entretenu est souvent nettement sous 435 Hz. | Tolérance par note **plus large dans les deux octaves extrêmes** (valeur à fixer avec `expert-audio-mir`) ; plage du CA1 à étendre vers le bas si POC-01 le permet (ex. 430 Hz), et sinon le conseil d'accordage du CA3. |
| PED-33 | Mineur | 03, US-ECO-11, US-PRA-02, US-CPT-04 CA4 | Le **son d'erreur** du mode attente est aussi un son de l'application, capté par le micro. Un « buzzer » est en outre décourageant, surtout pour un enfant. | Son d'erreur **désactivé par défaut**, toujours désactivé au micro ; quand il est activé, son doux et neutre, sans connotation d'échec. |
| PED-34 | Mineur | 03, US-CPT-04 CA1 ; EDB ENF-I18N-03 (S) | L'anglais est au MVP, mais la validation de la terminologie par un musicien (ENF-I18N-03) n'arrive qu'en V1 (US-PUB-06). Il faut choisir entre measure et bar, quarter note et crotchet, et la traduction de « note rejouée » (re-struck tied note) ne va pas de soi. | Avancer au MVP un **glossaire bilingue** des termes affichés (types d'erreurs, niveaux, notions de solfège), validé par `pedagogue-piano` avant I6. Je propose l'anglais américain (measure, quarter note). |
| PED-35 | Mineur | 03, US-ANA-03 CA3 | Un élève revient rarement « deux mesures en arrière » : il revient au **début de la ligne, de la phrase** (4 à 8 mesures) ou au début du morceau. | Ajouter des cas de test : retour au début de la phrase (4 mesures) et retour au début du morceau ; recalage en 2 mesures au plus dans chaque cas. |
| PED-36 | Mineur | 03, US-RES-08 | Le signalement ne couvre que les erreurs affichées. En mode attente au micro, la fausse erreur la plus pénible est la **note juste non reconnue**, qui bloque le curseur (PED-04). | Ajouter le signalement « J'ai joué juste mais l'application n'a pas avancé », enregistré avec le même contexte. |
| PED-37 | Mineur | 03, US-CPT-06 CA1 | (a) Un passage guidé « main droite seule, mode attente » paraît enfantin à un pianiste avancé. (b) Les premières mesures des mélodies pour débutants contiennent presque toutes des **notes répétées** (« Au clair de la lune » : do-do-do), délicates au micro sous la pédale. | (a) Pour « intermédiaire » et « avancé », proposer les deux mains. (b) Inscrire le cas « notes répétées lentes en mode attente au micro » dans les critères de POC-01, puisqu'il conditionne le premier contact avec l'application. |
| PED-38 | Mineur | EDB 06 §9.1 repris par 03, US-QUA-01 | Le corpus prévoit du « jazz simple ». Une partition écrite en croches égales avec l'indication « Swing » se joue en croches inégales : toutes les croches sur le temps faible seraient « en retard ». | Au MVP, exclure du corpus les pièces avec indication swing, ou ne pas évaluer le rythme quand l'indication « Swing » est présente. Pour la catégorie populaire ou jazz simple, utiliser du ragtime écrit en croches égales (Joplin, *The Entertainer*, domaine public). |
| PED-39 | Mineur | 03, US-LEC-01 CA4 | Juger le son sur trois morceaux de démonstration ne garantit pas toute la tessiture. | Ajouter une gamme chromatique de la0 à do8 (88 touches) et un passage avec pédale (*Gymnopédie n°1*) aux morceaux d'écoute. |
| PED-40 | Mineur | 03, US-RES-06 ; formule de l'EDB 06 §5.3 | S_continuité = 100 − 5 × hésitations − 15 × arrêts ne dépend pas de la longueur jouée : 3 hésitations sur 4 mesures ou sur 3 minutes donnent le même 85. | Rapporter les pénalités à la longueur, par exemple par tranche de 16 mesures jouées, avec un minimum d'une tranche. À vérifier avec la batterie de l'annexe E. |

---

## 3. Recommandations sur les questions ouvertes

### 3.1 QO-25 — Délai de silence et seuil d'arrêt

**Recommandation : option 1 du PO (délai de pause = seuil d'arrêt + 3 s), à quatre conditions.**

1. **Une pause, pas une fin.** Un débutant qui s'arrête met en général 2 à 5 s à retrouver sa place, replacer ses mains et repartir, souvent au début de la mesure ou de la ligne. Au-delà d'environ 8 s, il ne cherche plus : il parle, regarde ailleurs ou a abandonné. Le délai de pause de 7 / 6 / 5 s (indulgent / standard / exigeant) est donc réaliste, **à condition que rejouer reprenne la séance sans toucher l'écran** (PED-17). Le curseur figé sur la dernière position jouée aide d'ailleurs l'élève à retrouver sa place.
2. **Plus de conflit entre arrêt et fin de séance.** Toute interruption au-delà du seuil d'arrêt suivie d'une reprise compte pour **un arrêt**, quelle que soit sa durée, même si elle a dépassé le délai de pause. Le problème soulevé par QO-25 (un arrêt suivi d'une reprise devenu indétectable) disparaît.
3. **Fin automatique** après 30 s de pause (réglable de 15 à 60 s), ou à la fin du passage (PED-26), ou par « Terminer ».
4. **Seuil d'arrêt mesuré en retard sur l'instant attendu**, avec un plancher en pulsations : arrêt = retard > max(seuil du niveau ; 2 temps au tempo de la séance). Sans ce plancher, à ♩ = 40 un retard d'un seul temps (1,5 s) est presque un « arrêt » en exigeant, alors qu'un professeur parlerait d'hésitation. Au micro, le silence signifie « aucune nouvelle attaque » (PED-02).

| Mode | Hésitation | Arrêt | Pause | Fin automatique |
|---|---|---|---|---|
| Attente | non évaluée | non évalué | après 2 min (micro fermé, reprise par un appui) | non |
| Continu, indulgent | facteur 2,5 | max(4 s ; 2 temps) | 7 s | 30 s de pause |
| Continu, standard | facteur 1,8 | max(3 s ; 2 temps) | 6 s | 30 s de pause |
| Continu, exigeant | facteur 1,4 | max(2 s ; 2 temps) | 5 s | 30 s de pause |

Valeurs à affiner en bêta avec les rapports de test (nombre de pauses suivies d'une reprise ou d'un abandon).

### 3.2 QO-31 et US-BIB-06 — Morceaux de démonstration

**Source : je recommande d'inverser l'ordre du PO et de retenir l'option 3 (gravure maison) en priorité.**

- **Maîtrise pédagogique** : il faut des choix éditoriaux précis (doigtés pour les débutants, indication métronomique, numéros de mesure, aucun ornement ajouté, D.C. réécrit en toutes notes, répartition claire des mains). Un fichier existant les a rarement tous.
- **Vérité terrain** : ces fichiers serviront aussi au corpus et au premier contact avec l'application ; ils doivent être exacts note pour note. Les fichiers communautaires sont de qualité inégale et leur licence est souvent incertaine. Les fonds sous CC0 de qualité (par exemple OpenScore) portent surtout sur des lieder et de la musique de chambre, pas sur des pièces pour piano de débutant.
- **Effort modeste** : environ 200 mesures au total, saisies dans un logiciel libre (MuseScore), exportées en MusicXML, relues par moi d'après une source d'époque du domaine public (premières éditions numérisées). Une licence CC0 sur la gravure, si le porteur la choisit, simplifie la compatibilité avec la GPL. Les droits sont à valider par `juriste-conformite`.
- L'option 1 (fichier CC0 existant) reste possible pièce par pièce, si un fichier vérifié satisfait tous les critères.

**Liste proposée (10 pièces, de la plus facile à la plus difficile).** Les six premières suffisent pour I1 ; les quatre autres complètent la gamme.

| # | Pièce | Niveau | Mains, clé | Ce qu'elle apporte | Critère US-BIB-06 |
|:-:|---|---|---|---|---|
| 1 | *Au clair de la lune* (traditionnel), gravure maison, do majeur, 4/4, ♩ = 80 | Tout premier débutant | MD seule | 5 notes, notes répétées lentes ; premier passage guidé du niveau « débutant » | Une main |
| 2 | *Frère Jacques* (traditionnel), gravure maison, do majeur, 4/4 | Débutant | **MG seule, clé de fa** | Lecture en clé de fa, la difficulté de Claire | Une main |
| 3 | Beethoven, *Ode à la joie* (thème de la 9e symphonie), arrangement maison | Débutant | MD seule, puis version avec une MG en notes longues | Mouvement conjoint, rythme pointé (mi. ré ré) et, dans la version de Beethoven, une note anticipée liée (liaison de prolongation) | Une main |
| 4 | Mozart, thème des Variations K. 265 (« Ah ! vous dirai-je, maman »), do majeur, 2/4 | Débutant (premier morceau à deux mains) | Deux mains | Deux sections reprises (‖: :‖), notes répétées | Reprise simple |
| 5 | Schumann, *Marche du soldat* op. 68 n°2 | Débutant avancé | Deux mains | Accords à la MD, rythme pointé (à vérifier sur l'édition de référence ; si un D.C. figure, l'écrire en toutes notes) | Accords |
| 6 | Bach (attribué à Petzold), *Menuet en sol majeur* BWV Anh. 114, 3/4 | Élémentaire, début intermédiaire | Deux voix | Fa♯ à l'armure (exemple type de l'altération oubliée, celui de la maquette E14), deux sections reprises, sans ornements | Reprise simple |
| 7 | Schumann, *Mélodie* op. 68 n°1, do majeur, 4/4 | Élémentaire | Deux mains | Mélodie et accompagnement, jeu lié ; premier passage guidé pour « intermédiaire » | Deux mains |
| 8 | Beethoven, *Für Elise* WoO 59, première partie seulement (refrain avec 1re et 2e fins) | Intermédiaire | Deux mains | **Anacrouse**, ré♯ / ré♮ dans la même mesure, 1re et 2e fins, arpèges brisés entre les mains, pédale ; très motivant | Deux mains intermédiaire, reprise |
| 9 | Satie, *Gymnopédie n°1*, extrait | Intermédiaire | Deux mains | Tempo lent, pédale continue, sauts de la MG ; très demandé par les adultes (Claire, Marc) ; cas difficile au micro | Deux mains intermédiaire |
| 10 | Chopin, Prélude op. 28 n°7 en la majeur (16 mesures) | Intermédiaire | Deux mains | Anacrouse, **grand accord arpégé** (mes. 12), pédale ; premier passage guidé pour « avancé » | Accords, deux mains |

Les quatre critères du CA3 sont couverts : une main (1, 2, 3), accords (5, 10), reprise simple (4, 6, 8), deux mains de niveau intermédiaire (8, 9, 10).

Passages guidés proposés pour US-CPT-06 : débutant → n°1, mes. 1 à 4, MD ; intermédiaire → n°7, mes. 1 à 4, deux mains ; avancé → n°10, mes. 1 à 4, deux mains (voir PED-37).

La pièce n°8 ne sera validée qu'après traitement de PED-05 (décompte) et de PED-06 (reprises). Le *Choral* op. 68 n°4 de Schumann, plein de points d'orgue, est réservé au corpus tant que PED-01 n'est pas traité.

### 3.3 QO-32 — Tolérance rythmique au micro

**Recommandation : option 1 du PO, précisée ainsi.**

1. **Tolérance effective** : T_eff = max(T du niveau **après application du plafond** ; 2σ), où σ est l'écart type de l'erreur de détection d'attaque mesuré par POC-01, puis par le calibrage. Le maximum doit être pris **après** le plafond en fraction de durée, sinon les notes rapides passent sous l'incertitude même en standard. Exemple : une gamme en doubles croches à ♩ = 120 donne des notes de 125 ms ; le plafond standard (35 %) donne 44 ms, l'exigeant (25 %) 31 ms, soit moins que l'incertitude annoncée de 20 à 50 ms.
2. **Même règle pour la fenêtre d'accord** : W_eff = max(W du niveau ; 2σ). La fenêtre « exigeant » de 40 ms pose le même problème que la tolérance de 50 ms (point non relevé dans le README).
3. **Niveau exigeant au micro** : proposé seulement si 2σ ≤ 50 ms. Sinon il est grisé avec le message « Le micro ne permet pas de mesurer le rythme aussi finement ; le niveau exigeant est disponible avec un piano numérique relié. »
4. **Marques individuelles au micro** : une note n'est marquée « en avance » ou « en retard » que si |Δ| ≥ 1,5 × T_eff. Entre T_eff et ce seuil, l'écart n'agit que sur le score, sans marque sur la partition.

**Justification pédagogique.** Les erreurs de rythme qui comptent pour un élève débutant ou intermédiaire sont des **erreurs de valeur** : rythme pointé joué égal, silence sauté, note longue écourtée. Elles dépassent largement une double croche, soit plus de 100 ms aux tempos de travail. Perdre la finesse sous 60 à 80 ms au micro coûte donc peu à la cible principale, alors qu'une fausse erreur de rythme coûte la confiance de l'élève et du professeur (persona Sophie). Exemples chiffrés : avec σ = 20 ms, rien ne change en standard et l'exigeant reste utilisable ; avec σ = 35 ms, l'exigeant devient en pratique un standard et doit être masqué.

### 3.4 Autres questions ouvertes à portée musicale

| QO | Avis |
|---|---|
| QO-23 | **Option 1, avec insistance sur EF-ECO-10 (diapason)** : beaucoup de pianos droits familiaux ne sont pas à 440 Hz, et l'accord étiré écarte les registres extrêmes (PED-32). Sans tolérance, Lucas aurait des fausses notes dans l'aigu. EF-ECO-08 est aussi indispensable : décompte et métronome sonores captés par le micro (PED-33 pour le son d'erreur). |
| QO-24 c | **Garder les trois niveaux** au MVP : le réglage indulgent / standard est l'outil pédagogique principal entre déchiffrage et consolidation (PED-24). Analyser les reprises simples dès le MVP est nécessaire : sinon *Für Elise* ou le *Menuet* BWV Anh. 114 seraient analysés dans un ordre faux. |
| QO-24 g et QO-12 | Style unique au MVP : je recommande des formulations **impersonnelles**, ni « tu » ni « vous » (« Fa♯ de l'armure oublié (4 fois) », « Mesures 1 à 4 : aucune erreur », « En pause — reprenez quand vous voulez » étant l'exception inévitable). Elles conviennent à un enfant de 11 ans comme à un adulte et laissent QO-12 ouverte jusqu'au style ludique de la V1. Voir l'annexe D. |
| QO-26 | **Oui.** La répétition est le cœur du travail d'un passage. Proposer 3 répétitions par défaut (réglable), une pause d'une mesure plus le décompte entre deux répétitions, et un score par répétition. Ne pas changer le tempo (boucle progressive en V1). |
| QO-27 | Mode page avec tourne en deux temps : c'est l'habitude du pupitre. Critère musical à ajouter au test de `designer-ux` : **au moins deux mesures visibles après la mesure en cours** à tout instant, au zoom par défaut, sur le téléphone en paysage. |
| QO-29 | **Option 1 (« expérimental »).** En jeu continu au micro, retour en direct « aucun » par défaut (résultats dans le compte-rendu seulement), et marques de rythme selon le §3.3 point 4. |

---

## 4. Points validés

- Principe « fiabilité d'abord » et filtrage par confiance (US-ANA-08) : conforme à ce qu'attend un professeur avant de recommander un outil.
- Ornements non évalués au MVP (US-ANA-07), sous réserve des précisions de PED-10.
- Ordre des notes d'un arpège non sanctionné (US-ANA-06 CA4), sous réserve de PED-12.
- « Avancé → standard » et « exigeant » non proposé par défaut (US-CPT-02), avec les repères de PED-23.
- Note en trop non bloquante en mode attente (US-PRA-02 CA3), dans la limite de PED-03.
- Rythme non évalué en mode attente ; notes liées non attendues ; compensation du décalage global constant.
- Valeurs de l'EDB 06 §4.1 (150 / 90 / 50 ms ; 120 / 80 / 40 ms) plausibles **en MIDI** comme points de départ.
- D.C., D.S. et Coda exclus du MVP et signalés à l'import (US-LEC-07 CA4, US-IMP-05).
- Au plus 3 passages à retravailler, groupes de 1 à 4 mesures (US-RES-03) : la bonne quantité pour une séance.
- Partition annotée sans dépendre de la seule couleur (US-RES-04).

## 5. Questions pour le porteur du projet

1. Acceptez-vous qu'au MVP le rythme **ne soit pas évalué** autour des points d'orgue et sous les rit. / accel. (PED-01), plutôt que de modéliser leur durée ?
2. Acceptez-vous de remplacer la formule du score global par la forme multiplicative (PED-15), ce qui modifie une proposition de l'EDB 06 §5.4 ?
3. Gravure maison des morceaux de démonstration (§3.2) : qui saisit les partitions (vous, ou un agent avec ma relecture) et sous quelle licence (CC0 recommandée, à valider par `juriste-conformite`) ?
4. Formulations impersonnelles au MVP pour trancher provisoirement QO-12 (§3.4) ?
5. Validez-vous les seuils de QO-25 (§3.1), en particulier la reprise automatique par le jeu et la fin après 30 s de pause ?

---

## Annexes : contenus proposés

### Annexe A — Reprises et numérotation

#### A.1 Définition des reprises simples (MVP)

**Inclus au MVP :**

1. **Barre de reprise de fin (:‖) avec sa barre de début (‖:)** : rejouer depuis la barre de début. Le signe double (:‖:) termine une section et commence la suivante.
2. **Barre de reprise de fin sans barre de début** : revenir à la dernière barre de reprise de début ; à défaut, à la mesure qui suit la dernière barre de reprise de fin déjà jouée ; à défaut, au début du morceau (cas de la première section du *Menuet* BWV Anh. 114).
3. **Reprise et anacrouse** : quand le morceau ou la section commence par une anacrouse, la reprise revient à l'**anacrouse**, pas à la mesure 1. La mesure incomplète qui précède la barre de reprise complète l'anacrouse (*Für Elise*).
4. **Fins multiples** : « 1. » et « 2. », et plus généralement « 1.-3. » / « 4. » ; le nombre de passages est déduit des numéros de fin ou de l'attribut de répétition du fichier.
5. **Barre de reprise au milieu d'une mesure** (fréquente avec une anacrouse) : prise en charge si le fichier la représente par deux mesures partielles ; sinon, signalée à l'import.
6. **Option « ignorer les reprises »** : chaque section est jouée une fois ; **les fins autres que la dernière sont sautées** (on joue la 2e fin directement). Elle s'applique à la lecture, au curseur et à l'analyse.
7. **Passage sélectionné** : les reprises sont suivies seulement si leurs deux barres sont **à l'intérieur** de la sélection ; si la sélection coupe une reprise, elle est jouée une fois, de manière linéaire.

**Exclus du MVP (signalés à l'import, lecture linéaire) :** D.C., D.S., Fine, Coda, « To Coda », reprises imbriquées, indications textuelles de répétition (« 3 fois ») sans encodage structuré.

#### A.2 Numérotation des mesures

1. Les numéros affichés sont **ceux du fichier**, sans renumérotation. Ce sont des chaînes de caractères (« 8a » existe).
2. **Anacrouse** (première mesure incomplète, marquée implicite dans le fichier) : pas de numéro affiché ; dans les textes, « anacrouse » ; la mesure 1 est la première mesure complète. « Revenir au début » inclut l'anacrouse ; « Aller à la mesure 1 » va à la première mesure complète.
3. **Mesures partielles** de part et d'autre d'une barre de reprise : un seul numéro pour les deux moitiés, celui de la partie qui commence la mesure.
4. **1re et 2e fins** : les conventions varient selon les éditions (numéros consécutifs, ou même numéro avec a / b) ; on suit le fichier. Si le fichier n'a pas de numéros, on numérote de façon consécutive, comme le font les logiciels de gravure courants.
5. Dans le compte-rendu, une erreur dans une section reprise porte la mention « (1re fois) » ou « (2e fois) » si elle ne s'est produite qu'à l'un des passages (PED-06).

### Annexe B — Tempos par défaut (US-LEC-03 CA5)

**Ordre de décision :** (1) indication métronomique du fichier ; (2) sinon, indication textuelle (table B.1) ; (3) sinon, chiffrage (table B.2). Dans les cas (2) et (3), le tempo est affiché comme **estimé** et l'utilisateur peut fixer le tempo de référence du morceau (PED-08). Un MIDI sans indication de tempo prend la valeur de la norme MIDI (♩ = 120), aussi affichée comme estimée.

#### B.1 Indications textuelles (valeur appliquée à l'unité de pulsation de la table B.2)

| Indication (variantes reconnues, sans tenir compte des majuscules) | Pulsations / min |
|---|:-:|
| Grave, Largo, Très lent, Sehr langsam | 46 |
| Lento, Lent, Langsam | 52 |
| Adagio | 60 |
| Larghetto | 63 |
| Andante, Gehend | 76 |
| Andantino | 84 |
| Moderato, Modéré, Mäßig, Moderately | 96 |
| Allegretto | 108 |
| Allegro moderato | 116 |
| Allegro, Animé, Lebhaft, Fast | 132 |
| Vivace, Vivo, Vif | 152 |
| Presto, Schnell, Très vif | 176 |
| Prestissimo | 200 |

Au MVP, les modificateurs (molto, assai, ma non troppo, poco, un poco più) sont ignorés : seul le mot principal compte. Les valeurs sont celles du milieu des plages courantes. *Andantino* reste ambigu depuis le XIXe siècle (plus vite ou plus lent qu'*Andante*) : on retient l'usage moderne, légèrement plus vite.

#### B.2 Selon le chiffrage (aucune indication)

| Chiffrage | Unité de pulsation (métronome et BPM) | Valeur par défaut | Remarque |
|---|---|:-:|---|
| 2/4, 3/4, 4/4, 5/4 | noire | ♩ = 80 | Tempo modéré confortable |
| 2/2 (C barré), 3/2, 4/2 | blanche | blanche = 60 | Alla breve |
| 3/8 | croche | ♪ = 120 | En mesure à un temps : ♩. = 40 (affichage au choix) |
| 6/8, 9/8, 12/8 | noire pointée | ♩. = 54 | Subdivision en croches proposée sous 50 |
| 6/4, 9/4 | blanche pointée | blanche pointée = 40 | |
| 5/8, 7/8 (mesures irrégulières) | croche | ♪ = 160 | Accent selon le groupement s'il est indiqué |
| x/16 | double croche | double croche = 144 | Rare au niveau visé |
| Sans chiffrage | noire | ♩ = 80 | |

### Annexe C — Orthographe des notes issues d'un MIDI (US-IMP-02)

1. **Tonalité** : utiliser l'indication d'armure du fichier si elle est présente et plausible ; sinon, **estimer la tonalité** d'après la répartition des notes et l'indiquer dans le rapport (« armure estimée : la mineur »).
2. **Notes de la gamme** : écrites selon la tonalité (sol majeur : fa♯).
3. **Tons mineurs** : 6e et 7e degrés haussés écrits comme des altérations de la gamme (la mineur : fa♯ et sol♯, jamais sol♭ ni la♭).
4. **Autres notes chromatiques** : dièse si la ligne monte vers la note supérieure (fa – fa♯ – sol), bémol si elle descend (la – la♭ – sol). Note chromatique isolée (accord) : dièses dans les tonalités à dièses et en do majeur / la mineur, sauf **si♭** ; bémols dans les tonalités à bémols, sauf la sensible des tons mineurs.
5. **Pas de double altération** au MVP ; pas de mi♯, si♯, fa♭, do♭ sauf dans les tonalités qui les contiennent (fa♯ majeur : mi♯).
6. **Cohérence dans la mesure** : une même hauteur garde la même orthographe dans une mesure (jamais fa♯ puis sol♭).
7. **Note jouée (messages d'erreur, toutes sources)** : écrite par rapport à la note attendue. Même nom de note à un demi-ton près : même nom avec altération (attendu fa♯, joué fa → « fa naturel », pas « mi♯ ») ; sinon, orthographe de la tonalité.
8. Exemple de contrôle : *Für Elise* importé d'un MIDI doit afficher ré♯ (et non mi♭) dans le motif mi – ré♯ – mi, et sol♯ (et non la♭) dans la main gauche.

### Annexe D — Formulations

Principes : décrire ce qui a été joué et ce qui était attendu, et donner l'action ; ne pas supposer la cause, sauf pour l'altération et l'octave ; pas de « Faux ! », « Raté », « Erreur ! » ; « plus grave / plus aigu » doublé d'un repère sur le clavier (« à gauche / à droite ») ; formulations impersonnelles au MVP (§3.4).

#### D.1 Indications du mode attente (US-RES-02)

| Situation | Exemple | Indication proposée |
|---|---|---|
| Altération de l'armure omise | Sol majeur, attendu fa♯, joué fa | « Fa♯ : le dièse est à l'armure. » |
| Altération accidentelle omise | Attendu do♯ (dièse devant la note), joué do | « Do♯ : dièse écrit devant la note. » |
| Accidentelle valable dans la mesure | 2e fa♯ de la mesure, joué fa | « Fa♯ : le dièse vaut jusqu'à la fin de la mesure. » |
| Bécarre omis | *Für Elise* mes. 1, attendu ré♮ après ré♯, joué ré♯ | « Ré naturel : le bécarre annule le dièse. » |
| Altération reportée au-delà de la barre | Mesure suivante, attendu ré, joué ré♯ | « Ré naturel : le dièse de la mesure précédente ne compte plus. » |
| Altération ajoutée | Do majeur, attendu fa, joué fa♯ | « Fa naturel (touche blanche). » |
| Erreur d'octave | Attendu do, joué le do une octave au-dessus | « Bonne note, une octave plus grave (vers la gauche). » |
| Un demi-ton trop haut, sans lien avec une altération | Attendu mi, joué fa | « Mi : un demi-ton plus grave, la touche juste à gauche. » |
| Un degré à côté | Attendu la, joué sol | « La : la note juste au-dessus. » |
| Plus loin | Attendu la, joué ré | « Attendu : la — joué : ré. » |
| Accord incomplet | Attendu do-mi-sol, joué do-sol | « Il manque le mi de l'accord. » |
| Trop de notes (PED-03) | Attendu mi, joué do-ré-mi-fa-sol | « Jouez seulement la note indiquée. » |
| Note en trop tolérée (CA3) | Attendu mi, joué mi + fa | Pas de message bloquant ; fa marqué discrètement. |

#### D.2 Libellés du compte-rendu (US-RES-03)

| Notion technique | Libellé pour l'élève |
|---|---|
| Juste du premier coup (attente) | « 38 notes sur 42 trouvées du premier coup » |
| Fausse | « Fausses notes » |
| Manquée | « Notes non jouées » |
| En trop | « Notes en trop » |
| Rejouée | « Notes liées rejouées (à tenir sans rejouer) » |
| En avance / en retard | « En avance » / « En retard » |
| Hésitation | « Hésitations » |
| Arrêt | « Arrêts » |
| Point positif (PED-16) | « Mesures 1 à 4 : aucune erreur » |
| Passage à retravailler, type notes | « Mes. 5-6 · main droite · fa♯ joué fa (3 fois) » |
| Passage à retravailler, type rythme | « Mes. 12 · en retard sur le premier temps (2 fois) » |
| Passage à retravailler, type arrêt | « Mes. 9 · arrêt — à travailler lentement » |
| Séance peu fiable (US-RES-07) | Formulation « sobre » de l'EDB 10 §6, conservée |

### Annexe E — Séances types pour valider les scores (US-RES-06 CA3)

Passage de référence : *Für Elise*, première partie (environ 100 notes), jeu continu, MIDI, niveau standard.

| Séance | Contenu | Score attendu (jugement du professeur) |
|---|---|---|
| A | Tout juste, en mesure, constamment 30 ms en retard | ≈ 100 |
| B | Tout juste, 5 notes entre T et 2T | 95 à 99 |
| C | 1 fausse note isolée et 1 accrochage | 95 à 99 |
| D | Ré♮ joué ré♯ 4 fois (erreur installée) | 90 à 96 |
| E | Tout juste, 2 arrêts suivis de reprises | 90 à 96 |
| F | 15 % de fausses notes, en mesure, sans arrêt | ≤ 85 |
| G | Notes justes, rythme très irrégulier (S_rythme ≈ 50) | ≈ 80 à 85 |

Classement attendu : A > B ≈ C > D ≈ E > F ≈ G. Avec la formule pondérée de l'EDB, F obtient environ 92 et passe devant D et E : c'est l'objet de PED-15. Avec la forme multiplicative proposée, F vaut 85 et le classement est respecté.

### Annexe F — Corpus de test : annotations et pièces (US-QUA-01)

**Protocole d'annotation des erreurs volontaires**

1. **Scénarisées avant l'enregistrement** : une fiche par prise donne la liste des erreurs à commettre (mesure, temps, main, note attendue, note à jouer, type selon EDB 06 §3). Le porteur annonce le numéro de la prise à voix haute avant de jouer.
2. **Après l'enregistrement**, l'annotateur note les écarts entre la fiche et ce qui a réellement été joué (erreurs involontaires comprises), en écoutant la prise et sur la forme d'onde.
3. **Un type par prise et par passage** pour garder des mesures lisibles : une prise « altérations », une prise « octaves », une prise « accords non synchronisés (MG avant MD) », une prise « arrêts et retours en arrière »…
4. **Jeu correct** : joué au métronome (pour le jeu continu) et sans métronome (pour le mode attente), avec et sans pédale.
5. **Relecture** : je relis chaque fiche et chaque annotation (US-QUA-01 CA3) ; une annotation douteuse est retirée, pas corrigée de mémoire.

**Pièces par cas particulier** (domaine public ; gravure à valider par `juriste-conformite`)

| Cas | Pièce proposée |
|---|---|
| Altération de l'armure, bécarre, altération reportée | Bach, *Menuet* BWV Anh. 114 ; Beethoven, *Für Elise* (mes. 1) |
| Anacrouse, 1re et 2e fins | Beethoven, *Für Elise* ; Brahms, *Berceuse* op. 49 n°4 |
| Reprise sans barre de début | Bach, *Menuet* BWV Anh. 114 |
| Accord arpégé | Chopin, Prélude op. 28 n°7, mes. 12 |
| Points d'orgue, accords à quatre voix, unissons | Schumann, *Choral* op. 68 n°4 |
| Notes répétées | « Au clair de la lune » (lent) ; Mozart, thème K. 265 |
| Liaisons de prolongation entre voix | Bach, Prélude en do majeur BWV 846 |
| Basse d'Alberti, autre main entre deux événements | Mozart, Sonate K. 545, 1er mouvement, mes. 1 à 4 |
| Trilles et petites notes (non évalués) | Mozart, Sonate K. 545, 1er mouvement (trilles de cadence) |
| Croisements de mains | Burgmüller, *L'Hirondelle* op. 100 n°24 ; Bach, Partita n°1 BWV 825, Gigue |
| Pédale continue, sauts de la MG, tempo lent | Satie, *Gymnopédie n°1* |
| Basses très graves (micro) | Chopin, Prélude op. 28 n°20 |
| Triolets, basse tenue, pédale | Beethoven, Sonate op. 27 n°2, 1er mouvement |
| Syncopes, sauts de la MG (catégorie populaire) | Joplin, *The Entertainer* |
| Rubato (corpus « expressif », hors taux de fausses erreurs de rythme) | Chopin, Prélude op. 28 n°4 |
| Position de do central (séparation des mains d'un MIDI) | Pièce de méthode gravée par nos soins, les deux pouces sur le do central |

## Suite donnée (PRD v0.2, `product-owner`)

Bilan : **40 intégrées, 0 rejetée, 0 en attente (les 5 points en attente en v0.2 sont tranchés par les décisions du porteur du 2026-11-07, PRD v0.3)**. Deux propositions sont modifiées par arbitrage (voir [08 §3](../08-questions-ouvertes.md)) : la *Gymnopédie n°1* de Satie (mort en 1925) est remplacée par le Prélude op. 28 n°4 de Chopin dans les démonstrations et le test d'écoute, Satie et Joplin restant au corpus privé (A1, A2) ; la reprise après une pause se fait par un décompte au début de la mesure précédente plutôt qu'à l'endroit rejoué (A6). Annexes A à F reprises dans les stories. Questions du §5 : 1 → QO-40, 2 → QO-39, 3 → QO-31, 4 → QO-24 g, 5 → QO-25.

| Remarque | Statut | Suite donnée |
|---|---|---|
| PED-01 | Intégrée (décision QO-40, 2026-11-07) | Position provisoire intégrée : US-ANA-07 CA2 ; MOD-04 |
| PED-02 | Intégrée | US-ANA-05 CA3 et CA4, US-PRA-08 CA1 ; MOD-05 |
| PED-03 | Intégrée | US-PRA-02 CA3 |
| PED-04 | Intégrée | US-ECO-07 CA4 et CA5 |
| PED-05 | Intégrée | US-PRA-05 CA4 |
| PED-06 | Intégrée | US-LEC-07 CA1 et CA3, US-RES-03 CA5 |
| PED-07 | Intégrée | US-VIS-06 CA2 |
| PED-08 | Intégrée | US-LEC-03 CA1, CA5, CA6 |
| PED-09 | Intégrée | US-IMP-02 CA5 et CA6 |
| PED-10 | Intégrée | US-ANA-07 CA3 |
| PED-11 | Intégrée | US-ANA-07 CA4 |
| PED-12 | Intégrée | US-ANA-06 CA4 et CA5 (formule retenue, arbitrage A9) |
| PED-13 | Intégrée | US-ANA-04 CA2, US-RES-02 CA2 ; MOD-09 |
| PED-14 | Intégrée | US-PRA-02 CA4, US-RES-03 CA6, US-RES-06 CA1 ; à confirmer avec QO-39 |
| PED-15 | Intégrée (décision QO-39, 2026-11-07) | Batterie de l'annexe E intégrée (US-RES-06 CA4) ; formule : MOD-01 |
| PED-16 | Intégrée | US-RES-03 CA2 et CA3 |
| PED-17 | Intégrée (décision QO-25, 2026-11-07) | US-PRA-08 ; reprise par décompte au début de la mesure précédente (arbitrage A6) |
| PED-18 | Intégrée | US-PRA-03 CA2 |
| PED-19 | Intégrée | US-ECO-06 CA1 et CA2, US-ECO-05 CA2 (arbitrage A10) |
| PED-20 | Intégrée | US-CPT-04 CA4 |
| PED-21 | Intégrée | US-QUA-01 CA3 et CA5 |
| PED-22 | Intégrée | US-BIB-06 CA2 et CA3, liste avec un remplacement (A1) |
| PED-23 | Intégrée | US-CPT-02 CA2 et CA4 |
| PED-24 | Intégrée | US-CPT-03 CA2 et CA3 |
| PED-25 | Intégrée | US-LEC-05 CA1 et CA2, US-PRA-05 CA2 |
| PED-26 | Intégrée | US-PRA-07 CA1 |
| PED-27 | Intégrée | US-PRA-06 CA2, US-PRA-08 CA2 |
| PED-28 | Intégrée | US-RES-05 CA2 |
| PED-29 | Intégrée | US-ANA-01 CA3 et CA4 |
| PED-30 | Intégrée | US-ANA-05 CA5 |
| PED-31 | Intégrée | US-IMP-02 CA4 |
| PED-32 | Intégrée | US-ECO-09 CA2 ; MOD-12 |
| PED-33 | Intégrée | US-PRA-02 CA5, US-CPT-04 CA5 (arbitrage A5) |
| PED-34 | Intégrée (décision QO-24 f, 2026-11-07) | Lexique bilingue intégré (US-CPT-04 CA8) ; anglais américain (A18) |
| PED-35 | Intégrée | US-ANA-03 CA3 |
| PED-36 | Intégrée | US-RES-08 CA2 |
| PED-37 | Intégrée | US-CPT-06 CA4 ; critère ajouté à POC-01 (note) |
| PED-38 | Intégrée | US-ANA-07 CA6, US-QUA-01 CA5 |
| PED-39 | Intégrée | US-LEC-01 CA5 (Prélude op. 28 n°4 au lieu de Satie, A2) |
| PED-40 | Intégrée (décision QO-39, 2026-11-07) | MOD-02 |
