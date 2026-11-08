# 10 — Écrans et ergonomie

## 1. Principes d'ergonomie

1. **La partition occupe l'écran.** Pendant la lecture et le jeu, l'application passe en plein écran (barres système rappelées par un balayage, encoche de la caméra hors de la partition) ; les commandes se replient quelques secondes après le début de la lecture et réapparaissent par un appui ; seule la partition reste visible. L'écran reste allumé tant qu'une partition est affichée au premier plan (lecture, jeu, partition annotée) et pendant le réglage du micro.
2. **Mains sur le clavier.** Aucune action n'est requise pendant le jeu. Démarrage par décompte, arrêt par détection du silence.
3. **Lisible à distance.** Tailles de police et contrastes pensés pour une lecture à 60-80 cm.
4. **Retour hiérarchisé.** Le compte-rendu commence par l'essentiel (score, 1 à 3 passages à retravailler), le détail est accessible ensuite.
5. **Encourageant.** Formulations positives, mise en avant des progrès. Jamais de jugement sur la personne.
6. **Deux tonalités.** Ludique (enfants, débutants) ou sobre (adultes, niveau avancé), au choix, à partir de la V1. Au MVP, un style unique, sobre et encourageant (§6, décision QO-24 g).
7. **Téléphone et tablette.** Le téléphone est validé en premier (seul appareil physique disponible) ; la tablette est vérifiée sur émulateur, puis par des testeurs équipés.

## 2. Navigation

**MVP** (proposition de `designer-ux` retenue par le PRD) : pas de barre de navigation. La **bibliothèque est l'écran d'accueil** ; les réglages s'ouvrent par une icône dans la barre du haut. Un appui sur un morceau ouvre directement la partition (E11) ; la fiche du morceau (E10) s'ouvre par une icône ou un menu. Une carte « Reprendre » en haut de la bibliothèque rouvre le dernier morceau avec les derniers réglages de séance. Le compte-rendu propose « Voir sur la partition » pour chaque passage à retravailler (EF-RES-03).

**V1** : barre de navigation à trois destinations, avec « Progression », selon le schéma ci-dessous ; « Travailler ce passage » (EF-RES-09).

```
┌──────────────────────────────────────────────────────────┐
│  Onglets principaux                                      │
│  [Bibliothèque]   [Progression]   [Réglages]             │
└──────────────────────────────────────────────────────────┘
   │
   ├─ Bibliothèque ─► Fiche morceau ─► Partition (lecture)
   │                                     │
   │                                     ├─► Préparation de séance ─► Jeu ─► Compte-rendu
   │                                     │                                    │
   │                                     │       ◄── Travailler ce passage ───┘
   │                                     └─► Historique du morceau
   │
   ├─ Importer ─► Source ─► (Vérification OMR) ─► Aperçu ─► Bibliothèque
   │
   ├─ Progression ─► Tableau de bord ─► Détail morceau / séance
   │
   └─ Réglages ─► Profil, Captation (MIDI, micro, calibrage), Affichage,
                  Son, Retour, Compte, Données et confidentialité
```

## 3. Inventaire des écrans

| # | Écran | Version | Exigences principales |
|---|---|---|---|
| E01 | Accueil / premier lancement (3 étapes) | MVP | P1 |
| E02 | Questionnaire profil (niveau, type de piano) | MVP | EF-CPT-02 |
| E03 | Configuration captation (MIDI / micro) + autorisation | MVP | EF-ECO-01, EF-ECO-05 |
| E04 | Calibrage | MVP | EF-ECO-06 |
| E05 | Bibliothèque | MVP | EF-BIB-01 à 03 |
| E06 | Import : choix de la source | MVP | EF-IMP-01, 02, 05 |
| E07 | Import : aperçu et métadonnées, rapport | MVP | EF-IMP-06, 12 |
| E08 | Import : vérification et correction OMR | V1 | EF-IMP-07, 08 |
| E09 | Import : capture photo | V2 | EF-IMP-04 |
| E10 | Fiche morceau | MVP | Métadonnées, meilleur score, dernières séances |
| E11 | Partition — mode lecture | MVP | VIS, LEC |
| E12 | Préparation de séance (feuille modale) | MVP | EF-PRA-01 |
| E13 | Partition — mode jeu | MVP | EF-PRA-02, 03, EF-RES-01, 02 |
| E14 | Compte-rendu de séance | MVP (V1 : comparaison, liste des erreurs, réécoute, travailler ce passage) | EF-RES-03 à 06 (MVP) ; EF-RES-07 à 10 (V1) |
| E15 | Détail d'une erreur / d'une note | MVP | EF-RES-04 CA2 |
| E16 | Réécoute de la séance | V1 | EF-RES-10 |
| E17 | Tableau de bord de progression | V1 | EF-PRO-01, 05 |
| E18 | Progression d'un morceau (courbe, carte de chaleur) | V1 | EF-PRO-03, 04 |
| E19 | Réglages | MVP | EF-CPT-05, 06 |
| E20 | Appairage Bluetooth MIDI | V1 | EF-ECO-03 |
| E21 | Gestion des profils | V1 | EF-CPT-04 |
| E22 | Vue parent | V2 | EF-PRO-07 |
| E23 | Espace professeur (classes, devoirs) | V2 | PRF |
| E24 | Mise en place du téléphone sur le pupitre | MVP | EF-ECO-06, P1 |
| E25 | Séparation des mains d'un MIDI à une piste | MVP | EF-IMP-02 CA2 |
| E26 | Connexion MIDI : état, choix de l'appareil | MVP | EF-ECO-01, EF-ECO-02 |
| E27 | Calibrage de la latence MIDI (option avancée) | MVP | EF-ECO-06 |
| E28 | Séance en pause ou interrompue | MVP | EF-PRA-08, ENF-FIA-04 |
| E29 | Légende des résultats | MVP | EF-RES-04, ENF-ACC-02 |
| E30 | À propos, licences, confidentialité | MVP | [11 §3](11-juridique-et-conformite.md#3-licences-logicielles-et-contenus), RGPD-01 |
| E31 | Supprimer toutes mes données | MVP | EF-CPT-06 |
| E32 | Morceaux de démonstration masqués | MVP | EF-BIB-03, EF-BIB-05 |
| E33 | Export du rapport de test (bêta) | MVP | ENF-MAINT-04, [11 §2.4](11-juridique-et-conformite.md#24-rapport-de-test-de-la-bêta) |

Les écrans E24 à E33 ont été ajoutés par le PRD (proposition de `designer-ux`) ; leurs spécifications sont livrées dans `docs/UX/`.

## 4. Maquettes fil de fer

Les maquettes sont indicatives : elles fixent le contenu et la hiérarchie, pas le design graphique. E05, E11 et E18 représentent la cible V1 ; E12, E13 et E14 représentent le **contenu du MVP**, avec la liste des ajouts de la V1. Le détail de chaque écran est défini dans le PRD et les spécifications UX.

### E05 — Bibliothèque (téléphone)

```
┌─────────────────────────────────┐
│ Bibliothèque          [+ Import]│
│ [ Rechercher...              🔍]│
│ Trier : Dernière pratique ▾     │
├─────────────────────────────────┤
│ ♪ Gymnopédie n°1                │
│   E. Satie · pratiqué hier · 78 │
├─────────────────────────────────┤
│ ♪ Für Elise                     │
│   L. v. Beethoven · il y a 3 j  │
│                          · 64   │
├─────────────────────────────────┤
│ ♪ Prélude en do majeur BWV 846  │
│   J. S. Bach · jamais pratiqué  │
├─────────────────────────────────┤
│                                 │
├─────────────────────────────────┤
│ [Bibliothèque] [Progrès] [Régl.]│
└─────────────────────────────────┘
```

### E11 — Partition, mode lecture (tablette, paysage)

```
┌───────────────────────────────────────────────────────────────┐
│ ← Für Elise                       Mesure 9/103   [Aa] [⚙]     │
├───────────────────────────────────────────────────────────────┤
│                                                               │
│   𝄞 ──────────────────────────────────────────────────────   │
│      ♪ ♪ ♪ ♪ ♪ │ ♪  ♪  ♪ │▌♪ ♪ ♪ │ ♪  ♪  ♪ │                   │
│   𝄢 ──────────────────▌──────────────────────────────────    │
│                       ▲ curseur                               │
│   𝄞 ──────────────────────────────────────────────────────   │
│   𝄢 ──────────────────────────────────────────────────────   │
│                                                               │
├───────────────────────────────────────────────────────────────┤
│ |◄  ▶  ■   Tempo [ 70 % ]  MD [●] MG [●]  🔁 9-12  𝅘𝅥 Métron. │
│                                                [ PRATIQUER ]  │
└───────────────────────────────────────────────────────────────┘
```

### E12 — Préparation de séance (MVP, téléphone)

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
│   Répéter (jeu continu)         │
├─────────────────────────────────┤
│ Micro · réglé ✓                 │
│ Exigence : indulgente           │
│ ⚠ Import incomplet (voir)       │  ← si anomalie
├─────────────────────────────────┤
│          [ COMMENCER ]          │  ← fixé en bas
└─────────────────────────────────┘
```

Affichage progressif ; une option indisponible est grisée avec sa raison ; le jeu continu au micro porte la mention « expérimental » (EF-PRA-03). V1 : modes « Boucle progressive » et « Suivi ».

### E13 — Mode jeu (MVP, retour complet)

```
┌───────────────────────────────────────────────────────────────┐
│ ● Micro actif              Mesure 11        [❚❚]   [■]        │
├───────────────────────────────────────────────────────────────┤
│   𝄞 ───────────────────────────────────────────────────────  │
│      ♪  ♪  ♪✕ ♪  │ ♪  ◌  ♪ │▌♪  ♪  ♪ │  ♪  ♪  ♪ │             │
│                        ▲   ▲                                  │
│   𝄢 ───────────────────▌───────────────────────────────────  │
│                                                               │
└───────────────────────────────────────────────────────────────┘
```

Barre du haut : voyant « Micro actif », numéro de mesure, Pause, Terminer (commandes ≥ 64 dp, dans les coins). Marques seulement sur les notes déjà passées, selon le code du §5 ; pas de « ? » pendant le jeu ; la légende (E29) est accessible depuis la partition annotée. V1 : clavier virtuel optionnel (EF-VIS-07).

### E14 — Compte-rendu (MVP, téléphone, jeu continu)

```
┌─────────────────────────────────┐
│ Séance terminée              ✕  │
├─────────────────────────────────┤
│            82 / 100             │  ← lisible à 60 cm
│  Meilleur score sur ce morceau  │
│ Notes 88 · Rythme 74 · Contin.90│
├─────────────────────────────────┤
│ Sans erreur : mes. 1 à 8        │  ← point positif réel
│ À retravailler                  │
│ 1. Mes. 17-18 · notes fausses  ›│  ← Voir sur la partition
│ 2. Mes. 31-32 · en avance      ›│
├─────────────────────────────────┤
│ ▸ Détail : 5 fausses · 2 non    │
│   jouées · 1 en trop · 3 en     │
│   avance · 4 min 12 · 2 mains · │
│   70 % · standard · micro       │
├─────────────────────────────────┤
│ [Voir la partition annotée]     │
│ [Recommencer]       [Terminer]  │
└─────────────────────────────────┘
```

Variantes du MVP :

| Variante | Différence |
|---|---|
| Mode attente | Score de notes seul ; « 14 notes sur 16 trouvées du premier coup » ; notes le plus souvent corrigées |
| Boucle (EF-PRA-11) | Score de chaque répétition |
| Captation douteuse | Bandeau en tête avec la cause et le conseil ; score « indicatif » en gris, exclu du meilleur score ; « Refaire le réglage du micro » |
| Séance interrompue | « Séance interrompue à la mesure 12 » ; résultats limités à la partie jouée |
| Aucune note jouée | Pas de score ; message ; séance non enregistrée |
| Analyse en cours | « Analyse en cours… » avec progression au-delà de 2 s |
| Échec de l'analyse | Message ; séance enregistrée « non analysée », analyse relançable |

V1 : comparaison avec la séance précédente et progrès (EF-RES-08), scores par main (EF-ANA-07), « Travailler ce passage » (EF-RES-09), liste des erreurs (EF-RES-07), réécoute (EF-RES-10), style ludique (EF-RES-12).

### E18 — Carte de chaleur d'un morceau

```
┌───────────────────────────────────────────────┐
│ Für Elise · 12 séances                        │
│ Score   ▁▂▃▃▄▅▅▆▆▇▇█   64 → 82                │
│ Tempo   60 % ────────► 85 %                   │
├───────────────────────────────────────────────┤
│ Mesures où les erreurs se concentrent         │
│  1-8   ░░░░░░░░                               │
│  9-16  ░░▒▒░░░░                               │
│ 17-24  ▓▓█▓░░░░   ← à travailler              │
│ 25-32  ░░░░▒▒▓▓                               │
└───────────────────────────────────────────────┘
```

## 5. Code couleur et symboles des résultats

Le code ne doit pas reposer uniquement sur la couleur (ENF-ACC-02). Principe : la **couleur indique la famille** d'erreur, le **symbole indique le type** ; les notes justes ne portent aucune marque ; une tête de note ne change jamais de forme ; les symboles sont placés **hors de la tête de note**.

| Famille | Couleur (thème clair) | Contraste sur blanc |
|---|---|:-:|
| Hauteur (fausse, en trop, rejouée) | Vermillon `#C23B00` | 5,4:1 |
| Rythme (en avance, en retard, accord non synchronisé) | Bleu `#005BB5` | 6,6:1 |
| Absence et continuité (manquée, hésitation, arrêt) | Gris `#5F6368` | 6,1:1 |
| Incertain (option « détections incertaines » seulement) | Gris moyen `#80868B` | 3,7:1 |
| Curseur | Bande translucide d'une teinte hors des familles ci-dessus | — |

| Résultat | Marque, toujours hors de la tête de note |
|---|---|
| Juste | Aucune ; en mode attente, l'avance du curseur suffit |
| Fausse | Tête pleine, en vermillon, à la hauteur jouée, avec une pastille « ✕ » ; halo discret autour de la note attendue |
| En trop | Petite tête, en vermillon, avec une pastille « + » |
| Rejouée | Pastille « ↻ » en vermillon (sévérité faible) |
| Manquée | Anneau en pointillé gris autour de la tête attendue |
| En avance / en retard | Flèche bleue ← avant la note / → après la note |
| Accord non synchronisé | Pastille « ≠ » bleue au-dessus de l'accord |
| Hésitation / arrêt | Pastille « ‖ » grise entre les deux événements |
| Incertain | « ? » et contour en pointillé, gris moyen |

Les pastilles mesurent au moins 1,5 interligne ; elles se placent au-dessus de la portée pour la main droite et au-dessous pour la main gauche, sans masquer altérations ni doigtés. Les couleurs de la zone de partition sont des jetons fixes (pas de couleurs dynamiques du système), déclinés pour le thème sombre en V1. Vérification avec la simulation des daltonismes d'Android ; symboles à valider par `pedagogue-piano`.

## 6. Contenus et ton

**MVP : un style unique, sobre et encourageant** (décisions QO-24 g et QO-12). Formulations **impersonnelles** par défaut (« Passage joué sans erreur ») ; **vouvoiement** quand il faut s'adresser à l'utilisateur (autorisations, confirmations) ; jamais de tutoiement au MVP ; en anglais, « you ». Le style ludique, avec tutoiement, arrive en V1 (EF-RES-12).

Règles de rédaction (liste de contrôle à la relecture) :
1. Les faits, pas de jugement : des chiffres et des lieux (« Mesures 9-12 »), jamais d'adjectif sur la personne.
2. Commencer par ce qui va bien, seulement si c'est vrai ; pas de félicitation automatique.
3. Les erreurs sont « à retravailler », jamais des « fautes », des notes « ratées » ou un jeu « mauvais » ; pas de « Faux ! », « Raté », « Erreur ! ».
4. Quand la captation est douteuse, c'est l'application qui entend mal, pas l'utilisateur qui joue mal.
5. Au plus un point d'exclamation par écran ; pas d'emoji ; pas d'animation de récompense au MVP.
6. Phrases courtes : 12 mots au plus pour ce qui se lit au pupitre.
7. Le vocabulaire est celui du lexique bilingue (ENF-I18N-03).

Formulation des erreurs : décrire ce qui a été joué et ce qui était attendu, et donner l'action ; ne pas supposer la cause, sauf pour l'altération et l'octave ; « plus grave / plus aigu » doublé d'un repère sur le clavier (« à gauche / à droite »).

| Situation | MVP (sobre et encourageant) | V1, style ludique |
|---|---|---|
| Bon score | « 86 / 100 — meilleur score sur ce morceau. » | « Bravo ! 3 étoiles ! » + animation |
| Progression (V1) | « Mesures 9-12 : 0 erreur (−5) » | « Tu as corrigé les mesures 9-12 ! » |
| Point positif | « Sans erreur : mesures 1 à 8. » | — |
| Erreur récurrente | « Fa♯ de l'armure oublié (4 fois) » | « Attention au fa♯ caché dans l'armure 🔍 » |
| Indication du mode attente | « Fa♯ : le dièse est à l'armure. » ; « Bonne note, une octave plus grave (vers la gauche). » | — |
| Score bas | « 48 / 100. À retravailler en priorité : mesures 5 à 8. » | — |
| Captation douteuse | « L'application vous a mal entendu : score indicatif. Rapprochez le téléphone du piano ou réduisez le bruit, puis refaites le réglage du micro. » | « Je t'entends mal… rapproche la tablette ? » |
| Pause | « En pause — reprenez quand vous voulez. » | — |
