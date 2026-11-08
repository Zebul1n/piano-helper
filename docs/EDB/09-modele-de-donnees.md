# 09 — Modèle de données (conceptuel)

Modèle **conceptuel** : il décrit les objets métier et leurs relations, pas un schéma de base de données.

## 1. Vue d'ensemble

```
Utilisateur 1───* Profil
Profil      1───* Morceau ───1 Partition ───1 FichierSource
                              │
                              ├───* Partie (instrument)
                              │       └───* Portée ───* Mesure ───* Note
                              └───* Annotation

Profil      1───* Séance ───* Passage (plage jouée, éventuellement en boucle)
Séance      *───1 Morceau
Séance      1───0..1 Enregistrement
Séance      1───* NoteJouée
Séance      1───* Écart ───0..1 Note (attendue)
                         └──0..1 NoteJouée
Séance      1───1 Résultat (scores)

Profil      1───* AppareilCaptation (piano MIDI, calibrage micro par modèle de téléphone)

[V2] Professeur 1───* Classe ───* Profil (élève)
[V2] Classe     1───* Devoir ───1 Morceau
```

## 2. Entités

### Utilisateur
Existe seulement si un compte est créé (EF-CPT-03).

| Attribut | Description |
|---|---|
| id | Identifiant |
| e-mail / fournisseur d'identité | Authentification |
| date de création | |
| abonnement | Statut, échéance [QO-01] |
| consentements | Télémétrie, partage professeur, date et version |

### Profil
Une personne qui joue. Un appareil sans compte a un profil local par défaut.

| Attribut | Description |
|---|---|
| id, nom affiché | |
| niveau déclaré | Débutant / intermédiaire / avancé |
| niveau d'exigence | Indulgent / standard / exigeant |
| source de captation préférée | MIDI / micro |
| préférences | Notation, style de retour, retour en direct par défaut, thème |
| mineur | Booléen (impacts juridiques, voir 11) |

### Morceau
L'entrée de la bibliothèque.

| Attribut | Description |
|---|---|
| id | |
| titre, compositeur, arrangeur | |
| étiquettes, niveau, statut, favori | |
| date d'ajout, dernière pratique | |
| réglages mémorisés | Zoom, mode d'affichage, derniers réglages de pratique |
| origine | Import fichier / OMR / démonstration |

### FichierSource
| Attribut | Description |
|---|---|
| format | MusicXML, MIDI, PDF, image… |
| contenu | Fichier d'origine conservé (ENF-FIA-02) |
| empreinte | Hash, pour détecter les doublons |
| rapport d'import | Éléments ignorés, avertissements |
| confiance OMR | Par zone, si applicable |

### Partition (modèle interne)
| Attribut | Description |
|---|---|
| version du modèle | Pour migrations et réimport |
| parties | Instruments ; une ou plusieurs marquées « jouées par l'utilisateur » |
| structure | Mesures, reprises, sauts, changements de tempo, de chiffrage, d'armure |
| tempo de référence | |

### Note (attendue)
| Attribut | Description |
|---|---|
| id stable | Indispensable pour relier rendu, analyse et historique |
| mesure, position dans la mesure | Position musicale (fraction exacte) |
| hauteur | Numéro MIDI + orthographe (nom, altération) |
| durée | Valeur rythmique |
| portée, voix, main | |
| liaison | Début / continuation de prolongation |
| nature | Normale, ornement, petite note, arpège |
| doigté | Si présent |

### Annotation (V2)
Doigté, texte libre, surlignage ; rattachée à une note ou une mesure ; auteur (élève / professeur).

### Séance
| Attribut | Description |
|---|---|
| id, date, durée | |
| morceau, plage de mesures | |
| mode | Attente / continu / boucle (EF-PRA-11) / boucle progressive / suivi |
| main(s) | |
| tempo | Réglé et effectif |
| source de captation | MIDI / micro + appareil |
| niveau d'exigence | Utilisé pour l'analyse (figé pour la comparabilité) |
| version de l'analyse | Version du moteur ; permet de réanalyser |
| état | Terminée / interrompue |

### Passage
Une exécution d'une plage de mesures au sein d'une séance (une séance en boucle contient plusieurs passages : une répétition = un passage, EF-PRA-11). Porte son propre résultat et tempo.

### NoteJouée
| Attribut | Description |
|---|---|
| hauteur | |
| début, fin | Temps relatif au début de la séance |
| intensité | |
| confiance | 1 en MIDI |

### Écart
| Attribut | Description |
|---|---|
| type | Juste, fausse, manquée, en trop, rejouée, avance, retard, hésitation, arrêt, accord non synchronisé… |
| sous-type | Altération, octave, touche voisine… |
| note attendue | Référence (si applicable) |
| note jouée | Référence (si applicable) |
| écart temporel | ms et fraction de temps |
| confiance | |
| sévérité | |

### Résultat
Scores global, notes, rythme, continuité ; par main ; passages prioritaires ; motifs détectés.

### Enregistrement
Audio (micro) ou flux MIDI de la séance, si activé (EF-ECO-09). Stockage local ; durée de conservation paramétrable.

### AppareilCaptation
Piano MIDI connu (nom, identifiant) ou calibrage micro. Le calibrage est rattaché au profil **et** au modèle de téléphone ; il porte le diapason, la déviation d'accord et l'inharmonicité par registre, le préréglage audio demandé, le niveau de référence et une éventuelle correction de latence (appliquée seulement si le test de cohérence des horodatages révèle un écart, EF-ECO-06).

## 3. Règles de gestion

| ID | Règle |
|---|---|
| RG-01 | La suppression d'un morceau entraîne la suppression de ses séances, écarts et enregistrements, après confirmation explicite. |
| RG-02 | Une séance référence la version de la partition utilisée. Si la partition est corrigée (EF-IMP-08), les séances antérieures restent consultables mais ne sont comparées qu'avec prudence (avertissement). |
| RG-03 | Les identifiants de notes sont stables tant que la partition n'est pas modifiée ; une correction OMR conserve les identifiants des notes non modifiées. |
| RG-04 | Les scores d'une séance sont figés avec la version de l'analyse et le niveau d'exigence utilisés ; une réanalyse crée un nouveau résultat sans écraser l'ancien. |
| RG-05 | Les données d'un profil mineur ne sont partagées avec un professeur qu'avec l'accord du responsable légal (V2). |

## 4. Volumétrie indicative

| Élément | Estimation |
|---|---|
| Morceaux par profil | 10 à 200 |
| Séances par profil et par an | 100 à 1 000 |
| Notes jouées par séance | 200 à 5 000 |
| Taille d'un enregistrement audio (compressé) | ~1 Mo / min |
| Taille d'une partition (modèle + source) | 50 Ko à 5 Mo (PDF) |

Les notes jouées et écarts de toutes les séances peuvent représenter un volume important : prévoir une stratégie de compactage (ex. conserver le détail des 30 dernières séances par morceau et seulement les agrégats au-delà). Décision QO-11 : au MVP, tout le détail est conservé ; la place réellement occupée est mesurée pendant la bêta, puis la stratégie est décidée avant la V1.
