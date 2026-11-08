# 06 — Analyse du jeu et détection des erreurs

Ce document décrit **ce que l'analyse doit produire** et **les règles métier** qui la gouvernent. C'est le cœur du produit et sa principale source de risque.

## 1. Chaîne de traitement

```
 Partition importée                        Jeu de l'utilisateur
        │                                          │
        ▼                                          ▼
 ┌───────────────────┐                ┌──────────────────────────┐
 │ Déroulé attendu   │                │ Captation                │
 │ (reprises, sauts, │                │ MIDI : événements bruts  │
 │  mains, tempo)    │                │ Micro : signal audio     │
 └────────┬──────────┘                └────────────┬─────────────┘
          │                                        │
          │                           ┌────────────▼─────────────┐
          │                           │ Transcription (micro)    │
          │                           │ → événements « note »    │
          │                           └────────────┬─────────────┘
          │                                        │
          └──────────────┬─────────────────────────┘
                         ▼
               ┌───────────────────┐
               │ Alignement        │  associe chaque note jouée
               │ jeu ↔ partition   │  à une note attendue
               └─────────┬─────────┘
                         ▼
               ┌───────────────────┐
               │ Classification    │  justes, fausses, manquées,
               │ des écarts        │  en trop, avance, retard...
               └─────────┬─────────┘
                         ▼
               ┌───────────────────┐
               │ Filtrage par      │  on ne montre que ce dont
               │ confiance         │  on est sûr
               └─────────┬─────────┘
                         ▼
               ┌───────────────────┐
               │ Agrégation        │  scores, passages prioritaires,
               │                   │  motifs récurrents
               └───────────────────┘
```

### Point clé : une analyse « informée par la partition »

L'application ne cherche pas à transcrire n'importe quelle musique à l'aveugle : elle **sait ce qui devrait être joué**. Au micro, cela change profondément la difficulté : plutôt que « quelles notes sont présentes dans ce signal ? », la question devient « la note attendue est-elle présente, et y a-t-il autre chose ? ». Cette approche doit être exploitée pour améliorer la fiabilité [POC-01].

## 2. Données d'entrée

### 2.1 Déroulé attendu (issu de la partition)

La partition est convertie en une suite ordonnée d'**événements attendus**, chacun regroupant les notes qui commencent au même instant musical :

| Champ | Description |
|---|---|
| Position | Mesure, temps, fraction de temps (position musicale) |
| Instant théorique | En temps (s), d'après le tempo de la séance |
| Notes | Liste des hauteurs (numéro MIDI) |
| Main | Gauche / droite, par note |
| Durée écrite | Par note |
| Nature | Normale, liée (prolongation, **ne doit pas être rejouée**), ornement, appogiature, accord arpégé |
| Voix | Pour distinguer les notes tenues des notes en mouvement dans une même main |

Ce déroulé tient compte :
- des **reprises** et sauts selon le réglage de la séance ;
- de la **main** sélectionnée (en main seule, l'autre main n'est pas attendue) ;
- de la **plage** de mesures sélectionnée.

### 2.2 Jeu de l'utilisateur

Suite d'**événements joués** :

| Champ | MIDI | Micro |
|---|---|---|
| Hauteur | Exacte | Estimée, avec confiance |
| Début | Exact (à la latence près) | Estimé (± 20 à 50 ms [HYP]) ; écart type σ de l'erreur de détection mesuré par POC-01, puis au calibrage (utilisé en §4.1) |
| Fin | Exacte | Estimée, peu fiable (résonance, pédale) |
| Intensité | Vélocité 1-127 | Estimée, relative |
| Pédale | Exacte | Non disponible |
| Confiance | 100 % | 0 à 1 |

## 3. Typologie des erreurs

### 3.1 Erreurs de notes

| Type | Définition | Exemple | Affichage (code de [10 §5](10-ecrans-et-ergonomie.md#5-code-couleur-et-symboles-des-résultats)) |
|---|---|---|---|
| **Juste** | Note attendue jouée à la bonne hauteur. | | Aucune marque |
| **Fausse** | À la place d'une note attendue, une autre hauteur est jouée. | Fa au lieu de Fa♯ | Note jouée affichée à sa hauteur (famille « hauteur ») avec « ✕ » ; halo autour de la note attendue |
| **Manquée** | Note attendue non jouée. | Une note d'accord oubliée | Anneau en pointillé autour de la note attendue |
| **En trop** | Note jouée qui ne correspond à aucune note attendue. | Accrochage d'une touche voisine | Petite tête à sa hauteur, à sa position temporelle, avec « + » |
| **Rejouée** | Note liée (prolongation) rejouée au lieu d'être tenue. | | « ↻ » (sévérité faible) |

En **mode attente**, une note attendue est « juste » seulement si elle est trouvée **du premier coup** ; sinon elle est « fausse », avec une seule erreur par événement quel que soit le nombre d'essais. Les essais faux ne sont pas comptés en plus « en trop » ; le nombre d'essais est conservé.

Sous-catégories utiles au diagnostic (EF-RES-13) pour une note fausse. Les libellés affichés décrivent ce qui a été joué et ce qui était attendu, **sans supposer la cause**, sauf pour l'altération et l'octave.

| Sous-type | Détection |
|---|---|
| Altération omise | Écart d'un demi-ton ; la note attendue porte une altération de l'armure ou une accidentelle (y compris une accidentelle valable jusqu'à la fin de la mesure), et la note jouée ne l'a pas. |
| Bécarre omis | La note attendue porte un bécarre qui annule une altération (armure ou accidentelle précédente), et la note jouée garde l'altération annulée. |
| Altération reportée après la barre | La note jouée garde une accidentelle de la mesure précédente, qui ne vaut plus. |
| Altération ajoutée | La note jouée porte une altération absente de la note attendue (ni à l'armure, ni accidentelle). |
| Erreur d'octave | Même nom de note, octave différente. |
| Touche voisine | Écart d'un ou deux demi-tons, sans lien avec une altération. |
| Confusion de clé | Écart cohérent avec une lecture dans la mauvaise clé (ex. tierce ou sixte selon les clés) répété sur plusieurs notes. |
| Erreur de lecture | Autres cas. |

### 3.2 Erreurs de rythme

| Type | Définition |
|---|---|
| **En avance / en retard** | Début de la note décalé par rapport à l'instant attendu, au-delà de la tolérance. |
| **Hésitation** | Retard anormal de l'attaque d'un événement sur son instant attendu (au-delà du seuil d'hésitation du niveau, §4.1), sans atteindre le seuil d'arrêt. |
| **Arrêt** | Retard de l'attaque sur son instant attendu supérieur au seuil d'arrêt (§4.1), suivi d'une reprise. Toute interruption au-delà de ce seuil suivie d'une reprise compte pour un arrêt, quelle que soit sa durée, y compris après une pause de la séance (EF-PRA-08). |
| **Reprise en arrière** | L'utilisateur revient en arrière dans la partition pour rejouer un passage. Ce n'est pas une erreur de notes : les notes rejouées ne doivent pas être comptées comme « en trop ». |
| **Accord non synchronisé** | Écart entre la première et la dernière note d'un accord supérieur à la tolérance de simultanéité. Distinguer main gauche / main droite décalées (défaut fréquent). Au micro, signalé seulement au-delà de W_eff (§4.1) et seulement pour un décalage **entre les mains** : une asynchronie de moins de 30 ms n'y est pas mesurable de façon fiable. |
| **Durée incorrecte** (V2) | Note relâchée nettement trop tôt ou tenue trop longtemps. MIDI uniquement. |

Règles de mesure de l'hésitation et de l'arrêt :
- le retard se mesure **par rapport à l'instant attendu** de l'attaque, après la durée écrite de l'événement précédent (notes tenues et silences compris) : une note longue tenue ou un silence écrit n'est ni une hésitation ni un arrêt, quel que soit le tempo ;
- au micro, « silence » signifie **aucune nouvelle attaque**, et non absence de son (un piano résonne plusieurs secondes avec la pédale) ;
- après chaque hésitation, arrêt ou retour en arrière, **la référence temporelle et le curseur se recalent** sur la position du joueur : les notes suivantes sont jugées par rapport à cette nouvelle origine ;
- la note qui suit une hésitation ou un arrêt n'est pas comptée en plus « en retard ».

### 3.3 Erreurs de tempo (S)

| Type | Définition |
|---|---|
| Tempo global | Tempo moyen effectif vs tempo de la séance. |
| Instabilité | Variance du tempo local. |
| Accélération / ralentissement non écrits | Dérive du tempo local sur plusieurs mesures, hors indications (rit., accel., rubato marqué). |
| Précipitation sur les passages difficiles / ralentissement sur les passages difficiles | Corrélation entre la densité de notes et le tempo local. Diagnostic utile (EF-RES-13). |

### 3.4 Autres (V2)

| Type | Source |
|---|---|
| Nuances non respectées (en relatif) | MIDI fiable ; micro approximatif |
| Pédale absente ou mal placée | MIDI uniquement |
| Déséquilibre entre les mains (une main couvre l'autre) | MIDI |

## 4. Règles de tolérance

Toutes les valeurs ci-dessous sont des **hypothèses initiales** à valider en test utilisateur [HYP].

### 4.1 Niveaux d'exigence

| Paramètre | Indulgent | Standard | Exigeant |
|---|---|---|---|
| Tolérance rythmique T (écart de début acceptable) | 150 ms | 90 ms | 50 ms |
| Plafond de T en fraction de la durée de la note | 50 % | 35 % | 25 % |
| Fenêtre de simultanéité d'un accord | 120 ms | 80 ms | 40 ms |
| Seuil d'hésitation (facteur × durée attendue) | 2,5 | 1,8 | 1,4 |
| Seuil d'arrêt (retard de l'attaque, §3.2) | max(4 s ; 2 temps) | max(3 s ; 2 temps) | max(2 s ; 2 temps) |
| Délai de pause en jeu continu (seuil d'arrêt de base + 3 s, EF-PRA-08) | 7 s | 6 s | 5 s |
| Fin automatique de la séance après une pause | 30 s (réglable de 15 à 60 s) | 30 s | 30 s |
| Ornements (V1, EF-ANA-09 ; non évalués au MVP, §4.3) | Toute réalisation acceptée | Nombre de notes ± 2 | Nombre de notes ± 1 |

« 2 temps » s'entend au tempo de la séance : sans ce plancher, à un tempo très lent, un retard d'un seul temps serait déjà un arrêt. En **mode attente**, hésitation et arrêt ne sont pas évalués, et la séance ne se met pas en pause avant 2 minutes sans note (décision QO-25).

Le niveau par défaut dépend du niveau déclaré dans le profil (débutant → indulgent). Le niveau se choisit aussi à chaque séance. Au MVP, les trois niveaux règlent le rythme, la simultanéité, l'hésitation et l'arrêt (décision QO-24 c).

**Au micro (décision QO-32, formule précisée le 2026-11-08 avec `expert-audio-mir`).** L'incertitude de détection des attaques (§2.2) est du même ordre que les tolérances les plus fines. Pour éviter les fausses erreurs de rythme :
- l'incertitude est estimée **par registre** (au moins trois zones) et pour l'étage d'analyse qui produit le compte-rendu : biais corrigé ; σ robuste = 1,4826 × écart absolu médian, part due à l'annotation retirée ; u = 97,5e centile de l'écart ;
- tolérance effective **T_eff = max(T du niveau après application du plafond en fraction de durée ; 2σ ; u)** ; fenêtre d'accord effective **W_eff = max(fenêtre du niveau ; 2√2·σ)** ;
- le niveau « exigeant » n'est proposé au micro que si max(2σ ; u) ≤ 50 ms **en médium** ; sinon il est grisé avec une explication (« Le micro ne permet pas de mesurer le rythme aussi finement ; le niveau exigeant est disponible avec un piano numérique relié. ») ;
- une note n'est marquée « en avance » ou « en retard » sur la partition que si |Δ| ≥ 1,5 × T_eff ; entre T_eff et ce seuil, l'écart n'agit que sur le score de rythme.

### 4.2 Référence temporelle

| Mode | Référence pour le rythme |
|---|---|
| Attente | Aucune (rythme non évalué) |
| Continu, boucle (EF-PRA-11) | Grille du tempo imposé (métronome / curseur), décalage global du joueur compensé (un joueur constamment 30 ms en retard n'a pas toutes ses notes « en retard ») ; recalage de la référence et du curseur après chaque hésitation, arrêt ou retour en arrière (§3.2) |
| Suivi | Tempo local estimé sur une fenêtre glissante. Les écarts sont mesurés par rapport à ce tempo local ; la dérive de tempo est rapportée séparément (3.3). |

### 4.3 Cas particuliers

| Cas | Règle |
|---|---|
| Notes liées (prolongation) | Non attendues comme nouveau début. Si rejouées : signalées « rejouée » en sévérité faible. Une liaison « laissez vibrer » (l.v.), sans note d'arrivée, est ignorée. |
| Unisson entre voix ou entre mains | Deux voix ou les deux mains sur la même touche au même instant : une seule attaque attendue. |
| Indications d'octave (8va, 8vb, 15ma) | Appliquées aux hauteurs attendues. |
| Notes « cue » et notes cachées | Non attendues. |
| Trémolo écrit | Non évalué au MVP. |
| Notes répétées rapides | L'alignement doit associer chaque répétition à la bonne occurrence. Au micro, risque élevé de fusion de deux attaques : confiance réduite. |
| Appogiatures, petites notes | Après une petite note, la note principale est jugée avec une tolérance rythmique élargie jusqu'à la moitié de sa durée. Une petite note absente est signalée sur la partition annotée (« manquée », mineure), **hors du score**. En mode attente, une petite note jouée en même temps que la note principale (acciaccature écrasée) est acceptée. Petites notes avant le premier temps : pas d'erreur de rythme. |
| Trilles et ornements | **MVP** : non évalués. Un événement orné est validé (attente) ou juste (continu) si l'on entend, dans sa durée écrite, la note principale ou sa voisine diatonique supérieure ou inférieure ; les autres notes de l'ornement, y compris les terminaisons écrites, ne sont ni fausses ni en trop ; un ornement omis n'est pas une erreur. **V1** (EF-ANA-09) : évaluation globale de l'ornement (présent, hauteurs correctes, nombre de notes plausible), pas note par note. |
| Accord arpégé (signe d'arpège, y compris sur les deux portées) | Fenêtre = max(fenêtre du niveau ; 60 ms × (nombre de notes − 1)), plafonnée à la moitié de la durée écrite de l'accord. Référence rythmique : la première note entendue ; les autres ne sont pas évaluées en rythme. Ordre des notes non sanctionné (arpèges descendants, deux mains ensemble). Accord arpégé joué plaqué : juste. Un accord de plus d'une octave dans une main reçoit automatiquement la fenêtre d'arpège. |
| Point d'orgue, césure | Le curseur attend l'attaque suivante ; la référence temporelle repart de cette attaque ; l'événement qui suit n'est pas évalué en rythme (décision QO-40). |
| Rit., rall., allarg., accel. | Rythme non évalué jusqu'au prochain « a tempo » ou changement de tempo ; les hauteurs restent évaluées ; le curseur suit le joueur par l'alignement (décision QO-40). |
| Dernier événement du passage | Seule l'attaque est évaluée. |
| Notes croisées entre les mains | Attribution selon la partition (portée / indication m.g. / m.d.). |
| Pédale de sustain au micro | Les notes résonnent : la fin des notes n'est pas évaluée ; la résonance ne doit pas créer de notes « en trop ». |
| Piano désaccordé | Compensation du diapason global mesuré au calibrage ; tolérance supplémentaire de ± 30 cents [HYP] par note, plus large dans les deux octaves extrêmes pour tenir compte de l'accord étiré (valeur fixée avec `expert-audio-mir`) ; plage de diapason étendue vers 430 Hz si POC-01 le permet. |
| Notes très graves et très aiguës (micro) | Détection moins fiable (micro du téléphone, harmoniques) : confiance réduite, seuil de signalement relevé ; seuil renforcé pour signaler une erreur d'octave dès **sol 2** et en dessous ; bornes fixées par octave après POC-01. |

## 5. Calcul des scores

Les formules sont des **propositions** ; l'important est qu'elles soient stables, compréhensibles et qu'elles progressent quand l'utilisateur progresse.

### 5.1 Score de notes

```
S_notes = 100 × N_justes / (N_attendues + 0,5 × N_en_trop)
```

- Une note fausse compte comme non juste (elle est attendue mais pas réussie).
- Les notes en trop pénalisent à moitié (les accrochages de touches voisines sont fréquents et moins graves qu'une note manquée).
- Les notes rejouées après une reprise en arrière ne sont pas comptées.
- Les écarts incertains (§6) et les petites notes absentes (§4.3) ne sont pas comptés.
- **Mode attente** : N_justes compte les notes trouvées **du premier coup** (§3.1) ; les essais faux ne sont pas comptés comme notes en trop. Le compte-rendu affiche par exemple « 38 notes sur 42 trouvées du premier coup ».

### 5.2 Score de rythme

Pour chaque note juste, un score individuel :

```
s_i = 1                     si |Δ_i| ≤ T
s_i = 1 − (|Δ_i| − T) / 2T  si T < |Δ_i| < 3T
s_i = 0                     si |Δ_i| ≥ 3T

S_rythme = 100 × moyenne(s_i)
```

Δ_i : écart de début par rapport à la référence temporelle (4.2). T : tolérance du niveau d'exigence.

### 5.3 Score de continuité

```
N_tranches   = max(1 ; N_mesures_jouées / 16)
S_continuité = 100 − (5 × N_hésitations + 15 × N_arrêts) / N_tranches   (borné à 0)
```

Les pénalités sont rapportées à la longueur jouée, par tranche de 16 mesures avec au moins une tranche : 3 hésitations sur 4 mesures pèsent plus que 3 hésitations sur 3 minutes (décision QO-39).

### 5.4 Score global

| Mode | Formule |
|---|---|
| Attente | `S_global = S_notes` (notes trouvées du premier coup, §5.1) |
| Continu, boucle, suivi | `S_global = S_notes × (0,55 + 0,30 × S_rythme / 100 + 0,15 × S_continuité / 100)` |

Formule multiplicative (décision QO-39) : le score global ne dépasse jamais le score de notes. Exemples : 15 % de fausses notes, rythme et continuité parfaits → 85 (la somme pondérée de la v0.5 donnait 92) ; notes parfaites, rythme à 50, continuité parfaite → 85. Les coefficients restent des paramètres ; la formule est vérifiée sur une batterie de séances types dont le classement est fixé par `pedagogue-piano`.

### 5.5 Scores par main

Les mêmes calculs sont faits séparément pour chaque main quand l'attribution est disponible (EF-ANA-07).

## 6. Confiance et filtrage

Principe : **une fausse erreur coûte plus cher qu'une erreur non signalée.**

1. Chaque écart détecté porte un **indice de confiance** (0 à 1), qui combine la confiance de la transcription (micro) et la qualité de l'alignement local.
2. En dessous d'un seuil (par défaut 0,7 [HYP]), l'écart n'est **pas affiché comme une erreur**. Il peut être affiché comme « incertain » en mode détaillé.
3. Les écarts incertains **n'impactent pas** le score.
4. En MIDI, la confiance de transcription vaut 1 ; seule la qualité d'alignement intervient.
5. Si la proportion d'écarts incertains dépasse un seuil sur une séance (ex. 20 %), l'application l'indique et suggère une cause (bruit ambiant, niveau trop faible, appareil trop loin) plutôt que d'afficher un score peu fiable.

## 7. Restitution : hiérarchisation

Un compte-rendu qui affiche 40 erreurs décourage. Règles :

1. **Regrouper** les erreurs par passage (groupes de 1 à 4 mesures).
2. **Classer** les passages par gravité : nombre d'erreurs pondéré par le type. Ordre : arrêt ≥ fausse = manquée > rythme = hésitation > en trop > rejouée. Poids proposés [HYP] : manquée = fausse = arrêt = 3 ; rythme = hésitation = 2 ; en trop = 1 ; rejouée = 0,5. À égalité, le passage le plus tôt dans la partition.
3. **Commencer par un point positif réel** de la séance, s'il existe (« Mesures 1 à 4 : aucune erreur »), avant les passages à retravailler ; pas de félicitation automatique.
4. **Mettre en avant** les 1 à 3 passages prioritaires, avec un type d'erreur dominant (MVP, EF-RES-06) et une action suggérée (V1, EF-RES-09).
5. **Signaler les motifs** répétés plutôt que chaque occurrence : « Fa♯ de l'armure oublié (6 fois) » plutôt que six erreurs isolées.
6. **Valoriser** ce qui progresse : passages qui étaient en erreur à la séance précédente et ne le sont plus (V1, EF-RES-08).

## 8. Contraintes temps réel

| Usage | Exigence de latence (de l'attaque de la note au retour visuel ; au micro, l'attaque est le choc du marteau sur la corde, en MIDI l'émission du message de note) |
|---|---|
| Mode attente (validation d'une note, avance du curseur) | ≤ 100 ms en MIDI, ≤ 150 ms au micro [HYP] ; au micro, cible propre au grave si la mesure l'exige (décision QO-55, ENF-PERF-01) |
| Retour en direct (coloration d'une note) | ≤ 200 ms |
| Mode suivi (position du curseur) | ≤ 250 ms ; anticipation de la tourne de page |
| Compte-rendu | ≤ 2 s après la fin |

Au-delà de 150 à 200 ms, le retour est perçu comme décalé et gêne le jeu.

L'analyse temps réel peut être **provisoire** et corrigée par l'analyse différée (EF-ANA-13) ; en mode attente, l'analyse différée ne crée jamais d'erreur, elle peut seulement rendre une décision incertaine (décision QO-52). L'utilisateur ne doit pas voir d'incohérence choquante entre les deux : si une erreur affichée en direct est annulée a posteriori, elle disparaît du compte-rendu sans être mise en avant.

## 9. Protocole d'évaluation de la qualité

La qualité de l'analyse doit être **mesurée**, pas estimée.

### 9.1 Corpus de test

| Composante | Constitution |
|---|---|
| Partitions | 30 à 50 pièces de difficulté variée (débutant à avancé), styles variés (classique, pop, ragtime écrit en croches égales), avec cas particuliers (ornements, petites notes, points d'orgue, reprises, accords arpégés, notes répétées, croisements de mains). |
| Interprétations de référence | Jeu correct, enregistré simultanément en MIDI (vérité terrain) et en audio. Méthode simple : piano numérique joué sur ses haut-parleurs, captation MIDI et micro en parallèle. Pour l'acoustique : piano hybride ou à capteurs, ou annotation manuelle. |
| Interprétations avec erreurs | Jeu volontairement fautif (erreurs annotées) et erreurs injectées artificiellement dans les MIDI de référence (notes modifiées, supprimées, ajoutées, décalées). |
| Conditions acoustiques | Plusieurs pianos (droit, à queue, numérique sur haut-parleurs), plusieurs pièces, bruit ambiant (télévision, conversation), plusieurs appareils (Android, entrée et haut de gamme), positions du téléphone (pupitre, sur le piano, à côté). |

Des jeux de données publics de piano enregistré avec vérité MIDI existent (ex. MAESTRO) ; vérifier leurs licences avant tout usage. Ceux sous licence non commerciale sont utilisables aux conditions de la décision QO-37 (règles ci-dessous) ; ils ne servent jamais à entraîner un modèle (voir [11](11-juridique-et-conformite.md)).

**Sans piano numérique** (matériel actuel : piano droit acoustique, voir [07 §14.3](07-contraintes-et-pistes-techniques.md#143-matériel-de-test-disponible)), la vérité terrain des enregistrements maison s'obtient ainsi :
- **jeu correct d'une partition connue**, à tempo lent et régulier, éventuellement avec métronome : la partition elle-même sert de vérité pour les hauteurs, l'alignement temporel étant vérifié à l'oreille et sur la forme d'onde ;
- **erreurs volontaires annotées** au moment de l'enregistrement (liste des mesures et des erreurs commises) ;
- **jeux de données publics** avec vérité MIDI pour les mesures de transcription à grande échelle ;
- plus tard, enregistrements de testeurs bénévoles disposant d'un piano numérique (audio et MIDI simultanés).

Règles de constitution du corpus :

| Règle | Contenu |
|---|---|
| Taille minimale | ≥ 3 000 notes MIDI de jeu correct sur ≥ 20 pièces ; au micro, ≥ 1 000 notes de jeu correct par mode (attente, et jeu continu s'il est dans le MVP) ; ≥ 200 notes par sous-ensemble ciblé par un critère de mesure. |
| Annotation | Erreurs **scénarisées avant l'enregistrement** (fiche par prise, un type d'erreur par prise), écarts réels notés après ; jeu correct **au métronome** pour servir de référence en jeu continu ; annotations vérifiées à la réécoute par le porteur et leur cohérence avec la partition par `pedagogue-piano`. |
| Corpus public et corpus privé | **Public** (dépôt séparé `piano-helper-corpus` : partitions, annotations et MIDI versionnés, fichiers audio joints à chaque version de ce dépôt avec leurs empreintes, licence CC0, décision QO-49 ; rejoué par l'intégration continue) : contenus libres uniquement, compositeurs morts avant 1900 (voir [11 §1.2](11-juridique-et-conformite.md#12-points-dattention)). **Privé** (hors du dépôt) : enregistrements non publiables, œuvres plus récentes, jeux de données sous licence non commerciale. Le contrôle bloquant (§9.3) repose sur le corpus public et le corpus privé maison. |
| Jeux de données non commerciaux (décision QO-37) | Jamais copiés dans le dépôt ni dans l'application ; téléchargés par un script versionné (URL, somme de contrôle, licence) ; jamais utilisés pour entraîner un modèle ; mesures complémentaires seulement, jamais bloquantes. |
| Données expressives | Les interprétations libres (rubato, jeux de données publics) servent aux métriques de transcription et d'alignement, **pas** au taux de fausses erreurs de rythme. |
| Styles | Pas de pièce « swing » (croches inégales non écrites) au MVP ; pour le répertoire populaire, du ragtime écrit en croches égales. |
| Bruit ambiant | Sources libres (CC0) ou enregistrements maison, sans voix identifiable ni programme protégé (télévision, radio). |
| Gamme chromatique | Gamme chromatique enregistrée sur toute l'étendue (la 0 à do 8), en deux nuances, pour mesurer le comportement par registre. |
| Tentatives fausses | Sous-corpus de tentatives fausses, au moins 400 par mode, pour mesurer les validations indues (ENF-QA-01). |
| Vérité terrain semi-automatique | Un modèle de référence, exécuté sur ordinateur, est aligné sur la partition ; ses désaccords avec la partition sont vérifiés à l'écoute ; un sous-corpus annoté à la main mesure l'écart entre les deux méthodes. L'usage des sorties de ce modèle comme annotations est soumis à l'avis de `juriste-conformite`. |
| Format audio | Audio normalisé à 48 kHz. |

### 9.2 Métriques

| Métrique | Description |
|---|---|
| Précision / rappel / F-mesure de transcription | Notes détectées vs vérité terrain (tolérance de début 50 ms) |
| Précision / rappel des erreurs | Erreurs signalées vs erreurs réelles annotées ; une erreur est « bien signalée » si elle a le même type et la même note attendue |
| Taux de fausses erreurs | Sur les interprétations correctes : erreurs signalées / notes jouées |
| Validations indues (mode attente) | Événements validés (curseur avancé) sans que l'événement attendu ait été joué, évalués événement par événement |
| Non-validations (mode attente) | Événements joués justes mais non validés dans le délai d'ENF-PERF-01 |
| Exactitude de l'alignement | Proportion de notes jouées associées à la bonne note attendue |
| Latence | Mesurée sur appareil réel, de l'événement à l'affichage |

Chaque taux est publié avec son effectif et son intervalle de confiance à 95 %, par source, niveau d'exigence, catégorie de pièce et type d'erreur.

### 9.3 Règle de non-régression

Toute évolution de la chaîne d'analyse est évaluée sur le corpus, en comparant à la dernière version acceptée **sur la même version du corpus** (si le corpus change, la référence est d'abord recalculée). Aucune version ne doit dégrader le taux de fausses erreurs :
- en **MIDI**, le **nombre** de fausses erreurs ne doit augmenter ni au total ni dans un fichier (tolérance nulle) ;
- au **micro**, l'exécution doit être reproductible (deux exécutions identiques donnent le même résultat, au sens de l'[ADR-0022 du DAT](../DAT/adr/0022-reproductibilite-ordinateur-telephone.md)) et la règle porte sur des **effectifs** : par mode, sur N notes, la hausse nette du nombre de fausses erreurs est ≤ ⌊0,001 × N⌋ ; dans chaque sous-corpus (grave, aigu, pédale, notes répétées, bruit, téléphone à 1,5 m, morceau transposé) de N_s notes, elle est ≤ max(2 ; ⌊0,005 × N_s⌋) ; la même règle s'applique aux validations indues ;
- aucun taux ne doit dépasser la cible d'ENF-QA-01 ;
- toute nouvelle fausse erreur, même tolérée, est listée ; une hausse acceptée en échange d'un autre gain exige une décision consignée du porteur.
