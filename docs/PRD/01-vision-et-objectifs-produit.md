# 01 — Vision et objectifs produit

## 1. Énoncé de vision

> **Piano Helper est le compagnon de travail quotidien du pianiste apprenant : il affiche la partition qu'il travaille, la lui fait entendre, l'écoute jouer — sur un piano numérique comme sur un piano acoustique — et lui montre précisément, et seulement quand il en est sûr, ce qu'il doit corriger.**

Reprise de la vision de l'[EDB 01 §3](../EDB/01-contexte-et-objectifs.md), complétée de deux précisions issues des décisions de cadrage : le piano acoustique fait partie de la promesse dès le MVP (QO-22), et la fiabilité prime sur l'exhaustivité.

## 2. Proposition de valeur

**Pour** les pianistes apprenants qui travaillent seuls entre deux cours ou en autodidacte,
**qui** ne savent pas précisément ce qu'ils jouent mal, où, ni s'ils progressent,
**Piano Helper est** une application Android libre et gratuite
**qui** écoute leur jeu sur **leur propre partition** et localise leurs erreurs de notes et de rythme directement sur la portée,
**à la différence des** applications à catalogue imposé,
**notre produit** s'applique à n'importe quel morceau importé, fonctionne sans connexion, ne collecte aucune donnée et préfère se taire plutôt que signaler une fausse erreur.

### Valeur par persona au MVP

| Persona | Ce que le MVP lui apporte | Limite assumée au MVP |
|---|---|---|
| Claire (autodidacte, piano numérique USB) | Mode attente et jeu continu en MIDI, écoute main par main au tempo réduit | Pas de Bluetooth MIDI ; MIDI réel vérifié seulement en bêta |
| Lucas (élève, piano droit) | Mode attente au micro, compte-rendu encourageant | Jeu continu au micro seulement si POC-01 le permet ; style ludique en V1 |
| Nadia (parent) | Confidentialité claire (micro, aucune donnée transmise), suppression des données | Pas de vue parent (V2) |
| Marc (reprend, partitions PDF) | Boucle d'écoute, analyse du rythme (MIDI et, si possible, micro) | Pas d'import PDF (V1), pas de boucle progressive (V1) |
| Le porteur du projet (piano droit, téléphone Android 14) | Peut utiliser et tester lui-même l'application à son piano | Ne peut pas vérifier seul le MIDI réel ni la tablette |

## 3. Principes produit

Les principes de l'EDB ([01 §3](../EDB/01-contexte-et-objectifs.md)) s'appliquent. Le PRD les traduit en règles de décision :

| Principe | Règle de décision dans le backlog |
|---|---|
| La partition de l'utilisateur d'abord | Les formats d'import passent avant tout contenu fourni par l'application. |
| Un retour précis et localisé | Une erreur sans position dans la partition n'est pas affichée. |
| Bienveillant et utile | Le compte-rendu commence par l'essentiel et ne juge jamais la personne. |
| **Fiable avant d'être riche** | En cas d'arbitrage, la story qui réduit les fausses erreurs passe avant celle qui ajoute une détection. Aucune livraison ne dégrade le taux de fausses erreurs du banc d'évaluation. |
| Utilisable au piano | Aucune interaction pendant le jeu ; lisibilité à distance de pupitre vérifiée. |
| Local d'abord, budget nul | Aucune fonction du MVP ne dépend d'un serveur. |

## 4. Objectifs produit

Reprise des objectifs de l'[EDB 01 §4.2](../EDB/01-contexte-et-objectifs.md), précisés après les décisions de cadrage.

| ID | Objectif (EDB) | Précision PRD | Jalon de vérification |
|---|---|---|---|
| OP-1 | Valider la faisabilité technique de l'analyse (MIDI, puis micro). | Depuis QO-22, MIDI **et** micro sont validés pour le MVP : MIDI par entrées simulées (POC-05), micro au moins en mode attente (POC-01). | J1, puis J2 |
| OP-2 | Obtenir un taux de détection fiable, mesuré sur un corpus. | Le banc d'évaluation (US-QUA-02) mesure les indicateurs du §5.1 à chaque évolution ; non-régression du taux de fausses erreurs. | Continu dès I0 |
| OP-3 | Atteindre une rétention qui prouve l'usage régulier. | Mesurée en bêta sans télémétrie, à partir des rapports de test volontaires et d'un questionnaire ([QO-28](09-decisions-a-prendre.md)). | J3 |
| OP-4 | Définir un modèle économique viable [QO-01]. | Le projet est open source (GPL-3.0-or-later) et à budget nul : la question porte désormais sur la soutenabilité (dons, version payante sur un store, aucune monétisation). Hors MVP. | Avant V1 |

Objectifs utilisateur OU-1 à OU-5 : voir [EDB 01 §4.1](../EDB/01-contexte-et-objectifs.md). Le MVP vise OU-1, OU-2, OU-3 ; OU-4 partiellement (boucle d'écoute, et boucle de pratique au même tempo, décision [QO-26](09-decisions-a-prendre.md)) ; OU-5 partiellement (historique, sans tableau de bord).

## 5. Métriques produit

Les cibles marquées [HYP] viennent de l'EDB et seront confirmées par les POC. Les cibles marquées **[PO]** sont des propositions du Product Owner, à valider par le porteur.

### 5.1 Qualité de l'analyse

Mesurées par le banc d'évaluation sur le corpus ([06 §9](../EDB/06-analyse-du-jeu-et-erreurs.md)), puis sur des séances réelles.

| Métrique | Définition opérationnelle | Cible MIDI | Cible micro | Mesure |
|---|---|:-:|:-:|---|
| Taux de fausses erreurs | Sur des interprétations correctes : erreurs **affichées** (confiance ≥ seuil) / notes jouées | ≤ 1 % | ≤ 5 % [HYP] | Banc, à chaque évolution ; bloquant en cas de hausse |
| Fidélité de la réception MIDI | Notes reçues / notes émises par le simulateur (la transcription n'existe pas en MIDI) | 100 % | — | Banc |
| Précision des notes détectées | Notes détectées réellement jouées / notes détectées | — | ≥ 90 % [HYP] | Banc |
| Rappel des notes | Notes jouées détectées / notes jouées (tolérance de début 50 ms) | — | ≥ 85 % [HYP] | Banc |
| Précision des erreurs | Erreurs signalées qui correspondent à une erreur annotée (même type, même note attendue) / erreurs signalées | ≥ 95 % **[PO]** | ≥ 85 % **[PO]** | Banc, sur les jeux fautifs |
| Rappel des erreurs | Erreurs annotées signalées / erreurs annotées | ≥ 90 % **[PO]** | mesuré, sans seuil au MVP | Banc |
| Validations indues (mode attente) | Avances du curseur sans que l'événement attendu ait été joué / événements | ≤ 1 % **[PO]** | ≤ 1 % **[PO]** | Banc, événement par événement |
| Non-validations (mode attente) | Événements joués juste non validés en ≤ 150 ms / événements joués juste | — | ≤ 5 % [HYP] | Banc et séances capturées (US-QUA-05) |
| Précision de localisation | Erreurs rattachées à la bonne note attendue / erreurs signalées | ≥ 98 % | ≥ 90 % [HYP] | Banc |
| Exactitude de l'alignement | Notes jouées associées à la bonne note attendue / notes jouées | ≥ 98 % | ≥ 90 % [HYP] | Banc (POC-05) |
| Taux d'écarts incertains par séance | Écarts sous le seuil de confiance / écarts détectés | — | ≤ 20 % sur une séance en conditions normales [HYP] | Banc, rapports de test |
| Taux de signalements « ce n'était pas une erreur » | Signalements / erreurs affichées, en bêta | ≤ 2 % **[PO]** | ≤ 5 % **[PO]** | Rapports de test et signalements de US-RES-08 (décision [QO-28](09-decisions-a-prendre.md)) |

Chaque taux est publié avec son effectif et son intervalle de confiance à 95 %. Une cible n'est déclarée atteinte que si la borne haute de l'intervalle la respecte (exemple : 4 % observés sur 1 000 notes ne démontrent pas « ≤ 5 % » ; il faut un corpus plus grand). Les jeux de données expressifs (rubato) ne servent pas au taux de fausses erreurs de rythme.

### 5.2 Performance perçue

| Métrique | Cible | Source |
|---|---|---|
| Latence du mode attente | ≤ 100 ms MIDI ; ≤ 150 ms micro [HYP] | ENF-PERF-01 |
| Latence du retour en direct | ≤ 200 ms | ENF-PERF-02 |
| Affichage du compte-rendu | ≤ 2 s pour un passage de 5 min | ENF-PERF-04, [01 §5.2](../EDB/01-contexte-et-objectifs.md) |
| Import et affichage d'une partition | ≤ 2 min de bout en bout (OU-1) ; ≤ 5 s de traitement pour 10 pages | ENF-PERF-06 |
| Premier compte-rendu après installation | ≤ 5 min (parcours P1) | [03](../EDB/03-parcours-utilisateurs.md), US-CPT-06 |

### 5.3 Usage et satisfaction

| Métrique | Cible | Quand | Comment, sans serveur |
|---|---|---|---|
| Réussite de l'import des formats natifs | ≥ 95 % | Continu | Corpus d'import (US-IMP-01) ; rapports de test |
| Séances par testeur actif et par semaine | ≥ 3 | Bêta | Rapports de test exportés |
| Testeurs qui pratiquent ≥ 3 fois par semaine pendant 3 semaines | ≥ 50 % des testeurs **inscrits** **[PO]**, les non-répondants comptés comme inactifs | Bêta (critère J3) | Rapports de test |
| Retour à J+7 | ≥ 60 % des testeurs inscrits **[PO]** (l'EDB laisse cette cible « à définir ») | Bêta | Rapports de test |
| Compte-rendu jugé compris et actionnable | ≥ 80 % de réponses positives **[PO]** | Bêta | Questionnaire |
| Note sur le store | ≥ 4,3 / 5 | Après publication (V1) | Store, selon le canal public choisi avant la V1 ([QO-20](../EDB/12-risques-hypotheses-questions.md)) |

### 5.4 Critères de réussite du MVP

Le MVP réussit s'il répond « oui » aux questions de l'[EDB 04 §4](../EDB/04-perimetre-fonctionnel.md), reformulées dans l'EDB v0.5 pour inclure le micro (la question QO-33 est close). Le PRD précise « dans chacune des deux sources » pour la question 2 :

1. Un utilisateur peut-il importer une partition MusicXML, l'écouter et la jouer en étant corrigé, au micro sur un piano acoustique comme en MIDI, sans aide extérieure ?
2. Les erreurs signalées sont-elles justes et bien localisées, dans chacune des deux sources ?
3. Le compte-rendu est-il compris et actionnable ?
4. Les testeurs reviennent-ils pratiquer plusieurs fois par semaine pendant au moins trois semaines ?

## 6. Non-objectifs

### 6.1 Repris de l'EDB ([01 §6](../EDB/01-contexte-et-objectifs.md))

Autres instruments ; cours et méthode pédagogique ; catalogue de partitions vendu ; analyse de la posture et des doigtés par caméra ; évaluation de l'interprétation au-delà de mesures objectives ; édition de partition ; jeu à plusieurs en ligne ; version web ou ordinateur.

### 6.2 Propres au MVP

| Non-objectif du MVP | Raison | Quand |
|---|---|---|
| iOS | Décision QO-02 ; budget nul incompatible avec la publication iOS | Après V1 |
| Compte, sauvegarde en ligne, synchronisation | Local d'abord, aucun serveur | V1 / V2 |
| Télémétrie, analyse d'audience | Budget nul, confidentialité, ENF-CONF-02 | V1 avec consentement |
| Publication sur un store | Diffusion d'APK de test seulement | V1 ([QO-20](../EDB/12-risques-hypotheses-questions.md)) |
| Import PDF et photo | Risque OMR (POC-03) | V1 / V2 |
| Bluetooth MIDI | Pas de matériel de test ; S dans l'EDB | V1 |
| Mode suivi, boucle progressive, accompagnement | S dans l'EDB | V1 |
| Évaluation des ornements, des nuances, de la pédale, de la durée des notes | Risque de fausses erreurs | V1 / V2 |
| Tableau de bord de progression, carte de chaleur | S dans l'EDB | V1 |
| Style de retour ludique | C dans l'EDB ([QO-24](09-decisions-a-prendre.md) g) | V1 |
| Monétisation | Hors MVP (OP-4) | Avant V1 |
