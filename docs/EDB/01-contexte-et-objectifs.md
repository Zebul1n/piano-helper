# 01 — Contexte et objectifs

## 1. Contexte

Apprendre le piano repose sur un cycle simple : lire une partition, la jouer, se corriger, recommencer. Le point faible de ce cycle est la **correction** :

- l'élève en cours particulier voit son professeur une fois par semaine, alors qu'il travaille seul six jours sur sept ;
- l'autodidacte n'a personne pour lui signaler une note fausse, un rythme approximatif ou un doigté qui dérive ;
- l'oreille du débutant n'est pas encore assez formée pour repérer ses propres erreurs ;
- un défaut répété des dizaines de fois s'ancre dans la mémoire musculaire et devient difficile à corriger.

Des applications existent sur ce marché (catalogues de morceaux avec retour en temps réel, applications de type « jeu de rythme » pour piano). Elles imposent en général **leur propre catalogue** et leur propre pédagogie. L'idée de Piano Helper est de partir de **la partition que l'élève travaille réellement** (celle donnée par son professeur, achetée, téléchargée) et de lui apporter un retour objectif sur son jeu.

## 2. Problème adressé

> Quand je travaille seul une partition, je ne sais pas précisément ce que je joue mal, ni où, ni si je progresse.

Ce problème se décompose en quatre besoins :

| # | Besoin | Formulation utilisateur |
|---|---|---|
| B1 | Accéder à sa partition sur mobile | « Je veux avoir ma partition sur mon téléphone ou ma tablette, posée sur le pupitre. » |
| B2 | Savoir comment le morceau doit sonner | « Je veux entendre le morceau, à vitesse réduite, main par main. » |
| B3 | Être corrigé objectivement | « Je veux savoir quelles notes j'ai ratées, où j'ai accéléré, ce que j'ai oublié. » |
| B4 | Mesurer sa progression | « Je veux voir que je m'améliore d'une séance à l'autre, et savoir quels passages travailler. » |

## 3. Vision produit

> **Piano Helper est le compagnon de travail quotidien du pianiste apprenant : il affiche la partition qu'il travaille, la lui fait entendre, l'écoute jouer et lui montre précisément ce qu'il doit corriger.**

Principes directeurs :

1. **La partition de l'utilisateur d'abord.** L'application ne remplace ni le professeur ni la méthode : elle s'applique à n'importe quel morceau importé.
2. **Un retour précis et localisé.** Une erreur est toujours rattachée à un endroit de la partition (mesure, temps, note, main).
3. **Bienveillant et utile.** Le retour hiérarchise les erreurs et propose quoi travailler, au lieu d'afficher une liste brute.
4. **Fiable avant d'être riche.** Une fausse erreur signalée à tort détruit la confiance. Mieux vaut détecter moins de choses mais les détecter juste.
5. **Utilisable au piano.** Écran lisible à 60–80 cm, interactions minimales pendant le jeu, mains libres autant que possible.

## 4. Objectifs

### 4.1 Objectifs utilisateur

| ID | Objectif |
|---|---|
| OU-1 | Importer une partition et la voir correctement affichée en moins de 2 minutes. |
| OU-2 | Entendre n'importe quel passage de la partition, à tempo réglable, main gauche, main droite ou les deux. |
| OU-3 | Jouer un passage et obtenir un retour qui localise les erreurs de notes et de rythme. |
| OU-4 | Identifier les passages les plus difficiles et les retravailler en boucle. |
| OU-5 | Suivre sa progression sur un morceau au fil des séances. |

### 4.2 Objectifs produit

| ID | Objectif |
|---|---|
| OP-1 | Valider la faisabilité technique de l'analyse du jeu : au micro (piano acoustique) et en MIDI (piano numérique, d'abord par simulation). |
| OP-2 | Obtenir un taux de détection fiable, mesuré sur un corpus de test (voir indicateurs). |
| OP-3 | Atteindre une rétention qui prouve l'usage régulier (l'outil est utilisé dans la routine de travail). |
| OP-4 | Définir un modèle économique viable [QO-01]. |

## 5. Indicateurs de succès

Les valeurs cibles sont des propositions initiales, à affiner après les POC.

### 5.1 Qualité de l'analyse

| Indicateur | Cible MIDI | Cible micro (piano acoustique) |
|---|---|---|
| Précision des notes détectées (notes signalées justes réellement justes) | ≥ 99 % | ≥ 90 % [HYP] |
| Rappel des notes (notes jouées effectivement détectées) | ≥ 99 % | ≥ 85 % [HYP] |
| Taux de fausses erreurs (erreur signalée alors que le jeu était correct) | ≤ 1 % | ≤ 5 % [HYP] |
| Précision de localisation (erreur rattachée à la bonne note de la partition) | ≥ 98 % | ≥ 90 % [HYP] |

### 5.2 Usage

| Indicateur | Cible indicative |
|---|---|
| Taux de réussite de l'import (partition importée et affichée sans erreur bloquante) | ≥ 95 % pour les formats natifs (MusicXML, MIDI) |
| Délai entre fin de jeu et affichage du compte-rendu | ≤ 2 s |
| Utilisateurs actifs revenant à J+7 | à définir |
| Séances de pratique par utilisateur actif et par semaine | ≥ 3 |
| Note de satisfaction des testeurs (et sur les stores en cas de publication) | ≥ 4,3 / 5 |

## 6. Hors périmètre (pour l'instant)

Les sujets suivants sont **explicitement exclus** de la première version. Ils pourront être reconsidérés.

| Sujet | Raison |
|---|---|
| Autres instruments que le piano | Le piano est polyphonique et a ses propres contraintes ; élargir dilue l'effort. |
| Cours vidéo, méthode pédagogique complète | L'application est un outil de travail, pas un cours. |
| Catalogue de partitions vendu par l'application | Problèmes de droits et de modèle économique ; l'utilisateur apporte ses partitions. À réétudier [QO-05]. |
| Analyse de la posture, des doigtés réels, de la pédale par la caméra | Complexité très élevée, valeur incertaine. |
| Évaluation de l'interprétation (phrasé, expressivité, musicalité) | Subjectif ; risque de retour non fiable. Seules des mesures objectives (nuances au sens volume) sont envisagées. |
| Composition, édition de partition | Ce n'est pas un éditeur de partition. Seules des annotations légères sont prévues. |
| Jeu à plusieurs, en ligne, en temps réel | Hors besoin principal. |
| Version web ou ordinateur | Mobile et tablette d'abord. Une version tablette est en revanche une cible de premier plan. |
