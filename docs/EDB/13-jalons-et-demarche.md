# 13 — Jalons et démarche

## 1. Phasage

```
 Phase 0        Phase 1          Phase 2         Phase 3        Phase 4       Phase 5
 Cadrage   ──►  POC        ──►   MVP       ──►   Bêta      ──►  V1       ──►  V2
 (EDB,          (risques         (micro +        (testeurs,     (PDF,          (photo,
  questions)     techniques)      MIDI)           appareils)     publication)   professeur)
      │              │                │               │              │
      ▼              ▼                ▼               ▼              ▼
   Jalon J0       Jalon J1         Jalon J2        Jalon J3       Jalon J4
   EDB validé     Go / No-go       MVP utilisable  Go publication V1 publiée
                  technique        par testeurs
```

Les durées dépendent de l'équipe et du budget [QO-14] ; elles seront estimées à la sortie de la phase 1.

## 2. Phase 0 — Cadrage

| Activité | Livrable |
|---|---|
| Relecture et validation de l'EDB | EDB v1.0 |
| Réponses aux questions bloquantes (QO-02, QO-09, QO-14) | Décisions consignées |
| Entretiens utilisateurs (10 à 15 apprenants, 5 professeurs) pour tester H-01, H-02, H-05, H-06 | Synthèse des entretiens |
| Revue rapide de la concurrence (fonctions, prix, avis des utilisateurs) | Fiche de positionnement |
| Constitution du corpus de test initial (partitions + enregistrements) | Corpus v0 |

**Jalon J0 :** EDB validé, questions bloquantes tranchées.

## 3. Phase 1 — Preuves de concept (POC)

Chaque POC a un objectif, un critère de réussite mesurable et un livrable. Les POC sont indépendants et peuvent être menés en parallèle.

### POC-01 — Transcription au micro

| | |
|---|---|
| Objectif | Mesurer la qualité atteignable de détection des notes jouées sur un piano acoustique via le micro d'un téléphone / d'une tablette, en exploitant la connaissance de la partition. |
| Périmètre | 2 ou 3 modèles / approches (voir [07 §7](07-contraintes-et-pistes-techniques.md#7-transcription-audio--notes-micro)) ; 10 pièces du corpus ; le piano droit et le téléphone Android 14 du porteur ; jeux de données publics et enregistrements de testeurs pour d'autres pianos et appareils, dès qu'ils sont disponibles. |
| Critères de réussite | Taux de fausses erreurs ≤ 5 % sur des pièces de niveau débutant à intermédiaire ; analyse différée d'une minute de jeu en ≤ 10 s sur appareil ; latence du mode attente ≤ 150 ms, mesurée depuis l'attaque acoustique (ENF-PERF-01). En mode attente : validations indues ≤ 1 % et non-validations ≤ 5 % (ENF-QA-01), mesurées sur un sous-corpus d'au moins 400 tentatives fausses. Un critère est atteint si la valeur mesurée est ≤ cible **et** si la borne haute de l'intervalle de Wilson à 95 % est ≤ 1,5 × cible. Délai de décision et taux publiés par octave. |
| Livrable | Rapport de mesures, recommandation d'approche, estimation de l'effort pour industrialiser. |

### POC-02 — Latence et captation sur appareils

| | |
|---|---|
| Objectif | Mesurer la latence entrée → affichage (micro ; MIDI depuis l'appareil MIDI virtuel, le MIDI USB et Bluetooth réels étant reportés en bêta faute de piano numérique) et la stabilité de la captation sur les appareils de référence ; vérifier la cohérence des horodatages (clic du haut-parleur capté par le micro). |
| Critères de réussite | Cibles ENF-PERF-01 à 03 atteintes sur le téléphone du porteur ; mesure étendue aux appareils des testeurs avant la V1. |
| Livrable | Tableau de latences par appareil ; recommandations (versions minimales, API). |

### POC-03 — Reconnaissance de partition (OMR)

| | |
|---|---|
| Objectif | Évaluer la qualité de 2 ou 3 solutions OMR sur des PDF de piano (générés et scannés). |
| Périmètre | 20 PDF de difficulté croissante. |
| Critères de réussite | Sur PDF générés : ≥ 95 % de notes correctes (hauteur et durée) sur les pièces de niveau débutant et intermédiaire [HYP]. Temps de correction manuelle d'une page acceptable (< 5 min). |
| Livrable | Comparatif qualité / coût / licence / exécution (serveur ou appareil). |

### POC-04 — Rendu de partition et curseur

| | |
|---|---|
| Objectif | Valider une solution de rendu : fidélité, performances, curseur synchronisé, surimpression des erreurs sur des notes identifiées, intégration dans la technologie d'interface envisagée. |
| Critères de réussite | Corpus de 20 MusicXML rendus sans défaut bloquant ; ENF-PERF-05 et 08 atteints ; coloration d'une note par identifiant en ≤ 16 ms. |
| Livrable | Prototype, recommandation de bibliothèque et de technologie d'interface. |

### POC-05 — Alignement et classification des erreurs

| | |
|---|---|
| Objectif | Valider l'algorithme d'alignement jeu ↔ partition et de classification des erreurs, en temps réel et en différé, à partir d'entrées MIDI. |
| Périmètre | Corpus de jeux corrects, fautifs, hésitants, avec retours en arrière. |
| Critères de réussite | Exactitude d'alignement ≥ 98 % ; taux de fausses erreurs ≤ 1 % en MIDI ; resynchronisation en ≤ 2 mesures après un retour en arrière. |
| Livrable | Bibliothèque prototype testable sur ordinateur, avec banc d'évaluation automatisé. |

**Jalon J1 — Go / No-go technique.**

| Résultat | Décision |
|---|---|
| POC-01 (mode attente), POC-04 et POC-05 concluants | Lancement du MVP (micro + MIDI). |
| POC-01 concluant aussi en jeu continu | Jeu continu au micro inclus dans le MVP, marqué « expérimental », sans retour en direct par défaut, revérifié en bêta sur d'autres appareils (décision QO-29, prise au vu du rapport de POC-01 relu par `qa-testeur` et `architecte`). |
| POC-01 concluant en mode attente seulement | MVP avec le micro en mode attente ; jeu continu au micro reporté en V1, retravaillé. |
| POC-01 non concluant même en mode attente | Décision QO-42 : si les morceaux à une main atteignent les objectifs, micro limité à ces morceaux, marqué « expérimental » ; sinon, retravailler la reconnaissance avant de continuer le MVP. Dans tous les cas, la partie du MVP qui ne dépend pas du micro (profil, historique, import MIDI) continue. |
| POC-03 non concluant | Import PDF reporté ou limité aux PDF générés, avec correction manuelle mise en avant. |
| POC-02 non concluant sur une partie d'Android | Liste d'appareils compatibles. |

## 4. Phase 2 — MVP

Périmètre : colonnes « MVP » de [04](04-perimetre-fonctionnel.md#3-fonctionnalités-par-version).

Décision QO-30 : la partie « voir et entendre sa partition » du MVP (import, affichage, lecture) démarre dès que POC-04 est concluant, sans attendre J1 : elle ne dépend pas de l'analyse.

| Livrable | |
|---|---|
| Application Android installable (APK de test) | |
| Import MusicXML et MIDI, bibliothèque, morceaux de démonstration | |
| Visualisation, lecture, tempo, mains séparées, boucle, métronome | |
| Captation micro (mode attente, et jeu continu si POC-01 concluant), captation MIDI USB (testée par simulation), calibrage | |
| Modes attente et jeu continu | |
| Analyse notes + rythme, retour en direct, compte-rendu, historique | |
| Banc d'évaluation automatique de l'analyse | |

**Jalon J2 :** MVP utilisable de bout en bout par un testeur sans assistance.

## 5. Phase 3 — Bêta

| Activité | Détail |
|---|---|
| Recrutement | 30 à 100 testeurs, mix de personas (adultes autodidactes, élèves et parents, pianistes qui reprennent, professeurs) |
| Mesures | Qualité perçue des erreurs (signalement « ce n'était pas une erreur » dans l'application), usage (séances / semaine, modes utilisés), rétention à 3 semaines. Sans serveur ni mesure d'audience : rapport de test anonymisé exporté volontairement et questionnaire court (décision QO-28, [11 §2.4](11-juridique-et-conformite.md#24-rapport-de-test-de-la-bêta)) ; les testeurs qui n'envoient rien sont comptés comme inactifs |
| Micro en bêta | Mesure de la qualité au micro sur d'autres appareils et d'autres pianos ; testeurs équipés d'un piano numérique pour valider le MIDI réel |
| Vérifications reportées | Ce que le porteur ne peut pas vérifier seul est revérifié avec des testeurs, notamment le mode d'affichage du MVP : le test au pupitre est fait par le porteur seul, et sa conclusion est reconfirmée en bêta (décision QO-27) |
| Itérations | Tolérances, restitution, ergonomie du mode jeu |

**Jalon J3 — Go publication :** réponses positives aux 4 questions de [04 §4](04-perimetre-fonctionnel.md#4-ce-que-le-mvp-doit-démontrer) ; indicateurs de qualité atteints ; prérequis juridiques couverts ([11 §5](11-juridique-et-conformite.md#5-documents-à-produire-avant-publication)).

## 6. Phase 4 — V1

Périmètre : colonnes « V1 » de [04](04-perimetre-fonctionnel.md#3-fonctionnalités-par-version) — micro, PDF, Bluetooth MIDI, boucle progressive, mode suivi, progression, comptes, multi-profils, modèle économique.

**Jalon J4 :** publication sur les stores.

## 7. Phase 5 — V2

Photo, espace professeur, synchronisation, annotations, vue parent, gamification, dynamique et pédale, piano-roll.

## 8. Prochaines étapes immédiates

1. Relire cet EDB et l'amender (corrections, priorités, valeurs cibles).
2. ~~Trancher QO-02, QO-09, QO-14, QO-19, QO-21, QO-22~~ (fait le 2026-11-07). ~~Trancher les décisions issues du PRD (QO-10, QO-11, QO-20, QO-23 à QO-32, QO-34 à QO-43)~~ (fait le 2026-11-07, voir [12 §0](12-risques-hypotheses-questions.md#0-décisions-prises)). ~~Trancher les décisions issues du DAT (QO-44 à QO-53, QO-55)~~ (fait le 2026-11-08) ; QO-54 reste ouverte, à trancher avec le canal public (QO-20) avant la V1.
3. ~~Rédiger le PRD (`docs/PRD/`)~~ (v0.4) ; ~~rédiger le DAT (`docs/DAT/`)~~ (v0.3) ; rédiger les spécifications UX, avec l'équipe d'agents.
4. Mener les entretiens utilisateurs (sans budget : entourage, forums et communautés de pianistes, écoles de musique locales).
5. Constituer le corpus de test (partitions + enregistrements MIDI/audio simultanés).
6. Lancer en priorité POC-01 (micro) et POC-05 (alignement), qui portent les risques les plus forts.
