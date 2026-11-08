# 14 — Glossaire

## Vocabulaire musical

| Terme | Définition |
|---|---|
| **Accord** | Plusieurs notes jouées simultanément. |
| **Accord arpégé** | Accord dont les notes sont jouées successivement, rapidement, de bas en haut (signe ondulé). |
| **Altération** | Signe modifiant la hauteur d'une note : dièse ♯ (+½ ton), bémol ♭ (−½ ton), bécarre ♮ (annule). |
| **Altération accidentelle** | Altération placée devant une note, valable jusqu'à la fin de la mesure. |
| **Appogiature / petite note** | Note d'ornement écrite en petit, jouée avant la note principale. |
| **Armure** | Ensemble des altérations placées en début de portée, valables pour toute la pièce (sauf changement). |
| **Chiffrage (indication de mesure)** | Fraction en début de pièce indiquant le nombre et la valeur des temps par mesure (ex. 3/4). |
| **Clé** | Signe en début de portée qui fixe la hauteur des notes : clé de sol (main droite en général), clé de fa (main gauche en général). |
| **Coda, D.C., D.S.** | Indications de saut : *Da Capo* (reprendre au début), *Dal Segno* (reprendre au signe), *Coda* (aller à la conclusion). |
| **Diapason** | Fréquence de référence de l'accord (en général La = 440 Hz). |
| **Doigté** | Numéro (1 à 5) indiquant le doigt à utiliser. |
| **Grand portée (système)** | Deux portées liées par une accolade (clé de sol et clé de fa), utilisées pour le piano. |
| **Legato / staccato** | Jeu lié / détaché. |
| **Liaison de prolongation** | Courbe reliant deux notes de même hauteur : la seconde n'est pas rejouée, sa durée s'ajoute. |
| **Liaison d'expression (de phrasé)** | Courbe reliant des notes différentes, indiquant un jeu lié. |
| **Main droite (MD) / main gauche (MG)** | Abréviations courantes. |
| **Mesure** | Unité de découpage de la partition, délimitée par des barres verticales. |
| **Métronome** | Appareil ou fonction battant la pulsation à un tempo donné (en battements par minute). |
| **Mordant, gruppetto, trille** | Ornements : alternance rapide de la note avec ses voisines. |
| **Notation latine / anglo-saxonne** | Do ré mi fa sol la si / C D E F G A B. |
| **Nuances** | Indications d'intensité : pp, p, mp, mf, f, ff ; crescendo, decrescendo. |
| **Pédale de sustain (forte)** | Pédale de droite du piano, qui laisse résonner les notes après le relâchement des touches. |
| **Point d'orgue** | Signe qui suspend la mesure sur une note ou un silence, prolongé au gré de l'interprète ; le rythme n'y est pas évalué au MVP. |
| **Portée** | Ensemble de cinq lignes sur lesquelles s'écrivent les notes. |
| **Reprise** | Signe indiquant de rejouer un passage. Première / deuxième fin (« volta ») : fins différentes selon le passage. |
| **Rubato** | Liberté expressive de tempo. |
| **Tempo** | Vitesse d'exécution, en battements par minute (BPM) ou par une indication (Allegro, Andante…). |
| **Tessiture** | Étendue des notes utilisées. |
| **Voix** | Ligne mélodique indépendante au sein d'une même portée. |

## Vocabulaire technique

| Terme | Définition |
|---|---|
| **Alignement** | Mise en correspondance de chaque note jouée avec la note attendue de la partition. |
| **Bande horizontale** | Mode d'affichage de la partition sur une seule ligne de longueur illimitée, qui avance par paliers ; mode du MVP en paysage (décision QO-27). |
| **BLE MIDI** | MIDI transmis par Bluetooth Low Energy. |
| **Boucle de pratique** | Répétition d'un passage plusieurs fois d'affilée en jeu continu, au même tempo, avec un score par répétition (EF-PRA-11). À distinguer de la boucle d'écoute (EF-LEC-06) et de la boucle progressive (EF-PRA-06). |
| **Calibrage** | Procédure qui mesure la latence, le niveau du signal et le diapason pour ajuster l'analyse. |
| **Confiance** | Probabilité estimée qu'une détection (note ou erreur) soit correcte. |
| **Déroulé attendu** | Liste ordonnée des notes à jouer, dérivée de la partition après application des reprises, sauts et choix de mains. |
| **DTW (Dynamic Time Warping)** | Algorithme d'alignement de deux séquences temporelles qui se déroulent à des vitesses différentes. |
| **Fausse erreur (faux positif)** | Erreur signalée alors que le jeu était correct. |
| **Gigue** | Variation irrégulière de la latence. |
| **Latence** | Délai entre un événement (appui sur une touche) et sa prise en compte (affichage). |
| **MEI** | *Music Encoding Initiative* : format XML de notation musicale, issu du monde académique. |
| **MIDI** | Protocole et format de fichier décrivant des événements musicaux (note activée, désactivée, vélocité…), sans son. |
| **MusicXML** | Format XML standard d'échange de partitions entre logiciels. |
| **OMR (Optical Music Recognition)** | Reconnaissance optique de partition : conversion d'une image ou d'un PDF en partition structurée. |
| **OLTW (On-Line Time Warping)** | Variante de DTW utilisable en temps réel. |
| **Pause (de séance)** | État d'une séance en jeu continu après un silence prolongé : le curseur reste en place et la séance reprend quand l'utilisateur rejoue (EF-PRA-08). |
| **Piano-roll** | Représentation des notes sous forme de barres horizontales sur un axe temps × hauteur. |
| **POC** | Preuve de concept : prototype minimal destiné à vérifier une faisabilité. |
| **Quantification** | Arrondi des instants et durées d'un MIDI joué aux valeurs rythmiques de la notation. |
| **Rappel / précision** | Rappel : part des éléments réels détectés. Précision : part des éléments détectés qui sont réels. |
| **SBOM** | Nomenclature logicielle : inventaire des composants tiers et de leurs licences. |
| **Score following (suivi de partition)** | Suivi en temps réel de la position d'un musicien dans la partition. |
| **SoundFont** | Format de banque d'échantillons sonores pour la synthèse. |
| **Transcription** | Conversion d'un signal audio en événements « note ». |
| **Transcription informée par la partition** | Transcription qui exploite la connaissance des notes attendues pour être plus fiable. |
| **Vélocité** | En MIDI, force de frappe d'une touche (1 à 127). |
