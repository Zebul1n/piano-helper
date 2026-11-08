# 04 — Périmètre fonctionnel

## 1. Carte des modules

```
                         ┌──────────────────────┐
                         │   Compte & paramètres │ (CPT)
                         └──────────┬───────────┘
                                    │
 ┌──────────────┐    ┌──────────────▼───────────┐
 │ Import (IMP) │───►│     Bibliothèque (BIB)   │
 └──────────────┘    └──────────────┬───────────┘
                                    │ ouvrir
                     ┌──────────────▼───────────┐
                     │  Visualisation (VIS)     │◄─────────────┐
                     └──────┬────────────┬──────┘              │
                            │            │                     │
                  ┌─────────▼───┐   ┌────▼──────────────┐      │
                  │ Lecture LEC │   │ Pratique (PRA)    │      │
                  └─────────────┘   └────┬──────────────┘      │
                                         │                     │
                              ┌──────────▼─────────┐           │
                              │ Écoute (ECO)       │           │
                              │ MIDI  |  micro     │           │
                              └──────────┬─────────┘           │
                                         │ événements notes    │
                              ┌──────────▼─────────┐           │
                              │ Analyse (ANA)      │           │
                              │ alignement, erreurs│           │
                              └──────────┬─────────┘           │
                                         │                     │
                              ┌──────────▼─────────┐           │
                              │ Restitution (RES)  │───────────┘
                              └──────────┬─────────┘  erreurs affichées
                                         │            sur la partition
                              ┌──────────▼─────────┐
                              │ Progression (PRO)  │
                              └────────────────────┘

                  ┌──────────────────────────────────┐
                  │ Espace professeur (PRF) — V2     │
                  └──────────────────────────────────┘
```

## 2. Découpage en versions

| Version | Objectif | Public |
|---|---|---|
| **POC** | Lever les risques techniques majeurs (captation micro, alignement, rendu, OMR). | Équipe |
| **MVP** | Boucle de valeur complète : fichier numérique (MusicXML, MIDI) + captation micro (piano acoustique) et MIDI (piano numérique). | Porteur du projet, puis testeurs (alpha / bêta fermée, APK diffusés sur la page de publication du dépôt, décision QO-20) |
| **V1** | Micro dans tous les modes, import PDF. Publication publique sur Android (canal choisi avant la V1, [QO-20]). | Grand public |
| **V2** | Photo, professeur, synchronisation, motivation avancée. | Grand public, professeurs |
| **Portage iOS** | Après V1, quand le budget le permet (voir [07 §14](07-contraintes-et-pistes-techniques.md#14-conséquences-des-décisions-de-cadrage)). | Grand public |

Toutes les versions jusqu'à V2 ciblent **Android uniquement** (décision QO-02).

### Justification du découpage

Le chemin **MusicXML/MIDI + piano numérique en MIDI** élimine les deux risques techniques les plus forts (reconnaissance de partition et transcription audio polyphonique). Il permet de valider très tôt la valeur de l'analyse et de la restitution, qui sont le cœur du produit. Le PDF arrive ensuite, une fois son POC concluant. Le micro, initialement prévu en V1, a été avancé au MVP (voir ci-dessous).

**Priorités et MVP.** Une exigence **S** ou **C** dont une partie est nécessaire au MVP (souvent à cause du micro) porte dans [05](05-exigences-fonctionnelles.md) la mention « (MVP …) » avec la forme retenue ; sa priorité MoSCoW est inchangée. Ainsi, aucune exigence **M** ne dépend d'une fonction prévue après le MVP (décisions du 2026-11-07, voir [12 §0](12-risques-hypotheses-questions.md#0-décisions-prises)).

**Ajustement décidé (QO-22, 2026-11-07).** Le porteur du projet ne dispose que d'un piano droit acoustique. Pour qu'il puisse utiliser et tester l'application lui-même, la captation micro fait partie **du MVP** : le mode attente au micro est obligatoire ; le jeu continu au micro est inclus si les seuils de qualité de POC-01 sont atteints. Le chemin MIDI reste développé : il sert à tester le moteur d'analyse avec des entrées exactes (simulées), et sera vérifié en conditions réelles par des testeurs équipés d'un piano numérique.

## 3. Fonctionnalités par version

| Module | Fonctionnalité | MVP | V1 | V2 | Priorité |
|---|---|:-:|:-:|:-:|:-:|
| IMP | Import MusicXML (.musicxml, .xml, .mxl) | ● | | | M |
| IMP | Import MIDI (.mid) | ● | | | M |
| IMP | Ouverture depuis d'autres applications (partage système) | ● | | | M |
| IMP | Rapport d'import, aperçu, métadonnées | ● | | | M |
| IMP | Choix de la partie piano dans une partition multi-instruments | | ● | | S |
| IMP | Import PDF avec reconnaissance (OMR) | | ● | | S |
| IMP | Vérification et correction du résultat OMR | | ● | | S |
| IMP | Import par photo | | | ● | C |
| IMP | Formats MuseScore, MEI, ABC | | | ● | C |
| BIB | Liste, recherche, tri, suppression | ● | | | M |
| BIB | Morceaux de démonstration intégrés | ● | | | M |
| BIB | Étiquettes, niveau, favoris | | ● | | S |
| BIB | Synchronisation multi-appareils | | | ● | C |
| VIS | Rendu fidèle de la partition | ● | | | M |
| VIS | Curseur synchronisé, tourne de page | ● | | | M |
| VIS | Zoom, aller à la mesure, portrait / paysage | ● | | | M |
| VIS | Défilement continu et mode page | ● (un des deux : bande horizontale en paysage, QO-27) | ● (les deux) | | M / S |
| VIS | Clavier virtuel synchronisé | | ● | | S |
| VIS | Nom des notes affiché | | ● | | S |
| VIS | Mode sombre / contraste élevé | | ● | | S |
| VIS | Annotations personnelles | | | ● | C |
| LEC | Lecture son de piano, curseur | ● | | | M |
| LEC | Tempo, mains séparées, boucle, métronome | ● | | | M |
| LEC | Reprises et sauts (D.C., D.S., coda) | ● (reprises simples) | ● (complet) | | M / S |
| LEC | Nuances et pédale à la lecture | | | ● | C |
| PRA | Mode attente | ● | | | M |
| PRA | Mode jeu continu au tempo | ● | | | M |
| PRA | Décompte d'entrée | ● | | | M |
| PRA | Détection d'arrêt, pause et reprise en jouant, fin de séance | ● | | | S (nécessaire au MVP) |
| PRA | Boucle de pratique en jeu continu, au même tempo (EF-PRA-11) | ● | | | S (décision QO-26) |
| PRA | Accompagnement automatique de l'autre main (en jeu continu ; au micro sans écouteurs) | | ● | | S |
| PRA | Boucle progressive | | ● | | S |
| PRA | Mode suivi (l'application suit le tempo du joueur) | | ● | | S |
| PRA | Mode performance sans aide | | | ● | C |
| ECO | MIDI USB | ● | | | M |
| ECO | MIDI Bluetooth | | ● | | S |
| ECO | Micro (piano acoustique) | ● (attente ; continu si POC-01 OK) | ● (tous modes) | | M |
| ECO | Calibrage, gestion des autorisations | ● | | | M |
| ECO | Indicateur de signal, tolérance de diapason (formes minimales) | ● | | | S (nécessaire au micro, QO-23) |
| ECO | Sons de l'application non pris pour du jeu : métronome et décompte visuels, écouteurs filaires (forme minimale) | ● | ● (complet) | | S (nécessaire au micro, QO-23) |
| ECO | Enregistrement audio de la séance | | ● | | S |
| ANA | Notes justes, fausses, manquées, en trop | ● | | | M |
| ANA | Rythme (avance / retard) | ● | | | M |
| ANA | Accords | ● | | | M |
| ANA | Tempo global et stabilité | | ● | | S |
| ANA | Attribution main gauche / main droite | | ● | | S |
| ANA | Trois niveaux d'exigence (sans les ornements) | ● | | | S (QO-24 c) |
| ANA | Reprises simples suivies par l'analyse | ● | | | S (QO-24 c) |
| ANA | Ornements, tolérances des ornements par niveau | | ● | | S |
| ANA | Dynamique, pédale, durée des notes | | | ● | C |
| RES | Retour en direct paramétrable | ● | | | M |
| RES | Compte-rendu, partition colorée, scores | ● | | | M |
| RES | Passages à retravailler (jusqu'à 3, lien « Voir sur la partition ») | ● | | | S (QO-24 a) |
| RES | Passages prioritaires avec action suggérée, travailler ce passage | | ● | | S |
| RES | Style de retour ludique ou sobre (MVP : style unique sobre) | | ● | | C (QO-24 g) |
| RES | Réécoute de son jeu | | ● | | S |
| RES | Comparaison avec la séance précédente | | ● | | S |
| RES | Piano-roll comparatif | | | ● | C |
| PRO | Historique des séances | ● | | | M |
| PRO | Tableau de bord, évolution, carte de chaleur | | ● | | S |
| PRO | Série de jours, temps de pratique | | ● | | S |
| PRO | Vue parent, badges, objectifs suggérés | | | ● | C |
| CPT | Usage sans compte, profil, paramètres (français et anglais, thème clair) | ● | | | M |
| CPT | Suppression des données | ● | | | M |
| CPT | Compte et sauvegarde en ligne | | ● | | S |
| CPT | Multi-profils | | ● | | S |
| PRF | Espace professeur complet | | | ● | W (now) |

## 4. Ce que le MVP doit démontrer

À la fin du MVP, on doit pouvoir répondre « oui » aux questions suivantes :

1. Un utilisateur avec un piano acoustique (micro) ou numérique (MIDI) peut-il importer une partition MusicXML, l'écouter et la jouer en étant corrigé, sans aide extérieure ?
2. Les erreurs signalées sont-elles **justes** (pas de fausses erreurs perçues) et **bien localisées** ?
3. Le compte-rendu est-il **compris** et **actionnable** (l'utilisateur sait quoi retravailler) ?
4. Les testeurs reviennent-ils pratiquer plusieurs fois par semaine pendant au moins trois semaines ?
