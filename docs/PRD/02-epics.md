# 02 — Epics

Chaque epic porte un identifiant `EP-nn` et un **code court** utilisé dans l'identifiant de ses stories (`US-<CODE>-nn`). Les epics sont découpées pour qu'une epic corresponde à **une seule version cible**. Un même module de l'EDB peut donc apparaître dans plusieurs epics (ex. le module IMP dans EP-02 pour le MVP et EP-13 pour la V1).

Légende des versions : **MVP** (jalon J2), **V1** (jalon J4), **V2**. Voir [04 — Périmètre fonctionnel](../EDB/04-perimetre-fonctionnel.md) de l'EDB.

## 1. Vue d'ensemble

| Epic | Code | Titre | Version | Modules EDB | Stories |
|---|---|---|---|---|---|
| EP-01 | CPT | Démarrage, profil et paramètres | MVP | CPT | 6 |
| EP-02 | IMP | Import de fichiers numériques | MVP | IMP | 7 |
| EP-03 | BIB | Bibliothèque | MVP | BIB | 7 |
| EP-04 | VIS | Visualisation de la partition | MVP | VIS | 8 |
| EP-05 | LEC | Lecture audio de la partition | MVP | LEC | 7 |
| EP-06 | ECO | Captation : MIDI USB, micro, calibrage | MVP | ECO | 11 |
| EP-07 | ANA | Moteur d'analyse du jeu | MVP | ANA | 9 |
| EP-08 | PRA | Pratique : modes attente et jeu continu | MVP | PRA | 9 |
| EP-09 | RES | Restitution des erreurs | MVP | RES | 8 |
| EP-10 | HIS | Historique des séances | MVP | PRO | 3 |
| EP-11 | QUA | Outillage qualité : corpus, banc d'évaluation, MIDI simulé | MVP | transverse | 5 |
| EP-12 | CNF | Confidentialité, licences et diffusion de test | MVP | transverse | 6 |
| EP-13 | OMR | Import PDF par reconnaissance et correction | V1 | IMP | 4 |
| EP-14 | CNT | Confort de lecture et organisation | V1 | IMP, BIB, VIS, LEC | 8 |
| EP-15 | PRV | Pratique et captation avancées | V1 | PRA, ECO | 7 |
| EP-16 | ANX | Analyse étendue | V1 | ANA | 6 |
| EP-17 | RSX | Restitution étendue | V1 | RES | 6 |
| EP-18 | PRG | Progression | V1 | PRO | 5 |
| EP-19 | CPX | Comptes, profils et sauvegarde | V1 | CPT | 3 |
| EP-20 | PUB | Publication publique | V1 | transverse | 6 |
| EP-21 | PHO | Import photo et autres formats | V2 | IMP | 3 |
| EP-22 | PRF | Espace professeur | V2 | PRF | 5 |
| EP-23 | SYN | Synchronisation multi-appareils | V2 | BIB | 1 |
| EP-24 | MOT | Motivation et suivi parental | V2 | PRO, RES | 4 |
| EP-25 | EXP | Expression et outils experts | V2 | ANA, RES, LEC, VIS, PRA | 13 |

Les stories du MVP sont détaillées dans [03 — User stories MVP](03-user-stories-mvp.md) ; celles de la V1 et de la V2 sont résumées dans [04 — User stories V1 et V2](04-user-stories-v1-v2.md).

## 2. Epics du MVP

### EP-01 — Démarrage, profil et paramètres (CPT) — MVP

**Objectif.** Permettre de démarrer sans compte, de déclarer son niveau et son piano, de régler l'application et d'effacer ses données ; amener un nouvel utilisateur à un premier compte-rendu en moins de 5 minutes (parcours P1).

**Exigences.** EF-CPT-01, EF-CPT-02, EF-CPT-05, EF-CPT-06 ; EF-BIB-05 (démonstration guidée) ; EF-ANA-08 (partiel : niveau d'exigence) ; ENF-I18N-01, ENF-I18N-02, ENF-I18N-03 (lexique bilingue). Écrans E01, E02, E19.

### EP-02 — Import de fichiers numériques (IMP) — MVP

**Objectif.** Importer de façon fiable et sûre des partitions MusicXML et MIDI, depuis le sélecteur de fichiers ou une autre application, avec aperçu et rapport d'import.

**Exigences.** EF-IMP-01, EF-IMP-02, EF-IMP-05, EF-IMP-06, EF-IMP-12 ; ENF-PERF-06, ENF-SEC-04, ENF-FIA-02. Écrans E06, E07.

### EP-03 — Bibliothèque (BIB) — MVP

**Objectif.** Retrouver, gérer et exporter ses morceaux ; disposer dès l'installation de morceaux de démonstration libres de droits.

**Exigences.** EF-BIB-01, EF-BIB-03, EF-BIB-05 ; RG-01. Écrans E05, E10.

### EP-04 — Visualisation de la partition (VIS) — MVP

**Objectif.** Afficher la partition fidèlement, lisiblement à distance de pupitre, avec un curseur synchronisé et des tournes anticipées, sur téléphone et tablette.

**Exigences.** EF-VIS-01, EF-VIS-02 (un mode), EF-VIS-03, EF-VIS-04, EF-VIS-05, EF-VIS-11, EF-VIS-12 ; ENF-PERF-03, ENF-PERF-05, ENF-RES-04, ENF-COMP-03, ENF-ACC-07. Écran E11. Dépend de **POC-04**.

### EP-05 — Lecture audio de la partition (LEC) — MVP

**Objectif.** Faire entendre la partition avec un son de piano local, à tempo réglable, main par main, en boucle, avec métronome et reprises simples.

**Exigences.** EF-LEC-01 à EF-LEC-06, EF-LEC-07 (reprises simples) ; ENF-PERF-10, ENF-OFF-01. Écran E11.

### EP-06 — Captation : MIDI USB, micro, calibrage (ECO) — MVP

**Objectif.** Écouter le pianiste par MIDI USB (testé par simulation) et par le micro (mode attente obligatoire, jeu continu si POC-01 concluant), avec autorisation transparente et calibrage.

**Exigences.** EF-ECO-01, EF-ECO-02, EF-ECO-04, EF-ECO-05, EF-ECO-06 ; avancées au MVP en forme minimale par la décision [QO-23](09-decisions-a-prendre.md) : EF-ECO-07, EF-ECO-08 (partiel), EF-ECO-10 ; ENF-PERF-01, ENF-SEC-06, ENF-CONF-01. Écrans E03, E04. Dépend de **POC-01** et **POC-02**.

### EP-07 — Moteur d'analyse du jeu (ANA) — MVP

**Objectif.** Comparer le jeu à la partition : déroulé attendu, alignement robuste, classification des erreurs de notes, de rythme et d'accords, filtrage par confiance, analyse temps réel et différée.

**Exigences.** EF-ANA-01, EF-ANA-02, EF-ANA-03, EF-ANA-04, EF-ANA-06, EF-ANA-13 ; partiellement EF-ANA-08 et EF-ANA-10 ; règles de [06](../EDB/06-analyse-du-jeu-et-erreurs.md) §2 à §6 et §8 ; ENF-QA-01. Dépend de **POC-05** (et de POC-01 pour le micro).

### EP-08 — Pratique : modes attente et jeu continu (PRA) — MVP

**Objectif.** Préparer une séance et pratiquer en mode attente ou en jeu continu, mains libres, avec décompte, fin de séance et reprise après interruption.

**Exigences.** EF-PRA-01, EF-PRA-02, EF-PRA-03, EF-PRA-05, EF-PRA-08 (S, entrée au MVP par l'EDB v0.5), EF-PRA-11 (S, boucle de pratique, décision [QO-26](09-decisions-a-prendre.md)) ; ENF-PERF-01, ENF-FIA-04, ENF-ACC-01 (partiel). Écrans E12, E13.

### EP-09 — Restitution des erreurs (RES) — MVP

**Objectif.** Montrer les erreurs pendant et après le jeu de façon juste, localisée, hiérarchisée et accessible ; calculer les scores.

**Exigences.** EF-RES-01 à EF-RES-05 ; [06](../EDB/06-analyse-du-jeu-et-erreurs.md) §5, §6.5 et §7 ; ENF-PERF-02, ENF-PERF-04, ENF-ACC-02. Écrans E13, E14, E15.

### EP-10 — Historique des séances (HIS) — MVP

**Objectif.** Enregistrer chaque séance sans perte, la rendre consultable et préserver les données lors des mises à jour.

**Exigences.** EF-PRO-02 ; ENF-FIA-01, ENF-FIA-03 ; RG-04. Écran E10.

### EP-11 — Outillage qualité (QUA) — MVP

**Objectif.** Mesurer la qualité de l'analyse au lieu de l'estimer : corpus de test, banc d'évaluation automatique avec non-régression, entrées MIDI simulées, protocoles et mesures sur l'appareil de référence, capture de séances réelles dans la variante interne (US-QUA-05).

**Exigences.** ENF-QA-01, ENF-QA-02, ENF-MAINT-01, ENF-COMP-04 ; [06](../EDB/06-analyse-du-jeu-et-erreurs.md) §9 ; risques R-01, R-15, R-16. Livrable explicite de la phase MVP ([13 §4](../EDB/13-jalons-et-demarche.md)).

### EP-12 — Confidentialité, licences et diffusion de test (CNF) — MVP

**Objectif.** Garantir un fonctionnement local sans transmission de données (pas de permission réseau, sauvegarde Android désactivée, décision QO-36), tenir l'inventaire des licences (code compatible GPL-3.0-or-later, contenus libres sans NC ni ND), livrer les mentions légales dans chaque APK dès I0, diffuser chaque APK avec ses sources et publier une notice de confidentialité (US-CNF-06).

**Exigences.** ENF-OFF-01, ENF-CONF-01, ENF-CONF-02, ENF-CONF-03, ENF-COMP-02 ; [11 §3](../EDB/11-juridique-et-conformite.md) (inventaire, écran des licences), RGPD-01, RGPD-02 ; ENF-MAINT-04 en forme réduite (rapport de test, décision [QO-28](09-decisions-a-prendre.md)).

## 3. Epics de la V1

### EP-13 — Import PDF par reconnaissance et correction (OMR) — V1

**Exigences.** EF-IMP-03, EF-IMP-07, EF-IMP-08 ; ENF-PERF-07, ENF-SEC-05, ENF-OFF-02 ; RG-02, RG-03. Écran E08. Dépend de **POC-03** et de [QO-04](../EDB/12-risques-hypotheses-questions.md).

### EP-14 — Confort de lecture et organisation (CNT) — V1

**Exigences.** EF-IMP-11, EF-BIB-02, EF-VIS-02 (les deux modes), EF-VIS-07, EF-VIS-08, EF-VIS-10, EF-LEC-07 (complet), EF-LEC-10, EF-ANA-10 (complet) ; ENF-ACC-03, ENF-I18N-02 (notation allemande).

### EP-15 — Pratique et captation avancées (PRV) — V1

**Exigences.** EF-ECO-03, EF-ECO-04 (tous modes), EF-ECO-08 (complet), EF-ECO-09, EF-PRA-04, EF-PRA-06, EF-PRA-07 ; ENF-ACC-01. Écran E20.

### EP-16 — Analyse étendue (ANX) — V1

**Exigences.** EF-ANA-05, EF-ANA-07, EF-ANA-08 (complet), EF-ANA-09 ; ENF-QA-03, ENF-MAINT-02.

### EP-17 — Restitution étendue (RSX) — V1

**Exigences.** EF-RES-06 à EF-RES-10, EF-RES-12 ; ENF-ACC-06. Écran E16.

### EP-18 — Progression (PRG) — V1

**Exigences.** EF-PRO-01, EF-PRO-03, EF-PRO-04, EF-PRO-05, EF-PRO-09 ; RGPD-03. Écrans E17, E18.

### EP-19 — Comptes, profils et sauvegarde (CPX) — V1

**Exigences.** EF-CPT-03, EF-CPT-04, EF-CPT-07 ; ENF-SEC-01, ENF-SEC-02, ENF-SEC-03, ENF-OFF-03. Écran E21. Dépend de [QO-01, QO-03, QO-07](../EDB/12-risques-hypotheses-questions.md).

### EP-20 — Publication publique (PUB) — V1

**Exigences.** ENF-COMP-04 (complet), ENF-COMP-05, ENF-FIA-05, ENF-MAINT-03, ENF-CONF-02 (télémétrie), ENF-PERF-08, ENF-PERF-09, ENF-RES-01, ENF-RES-02, ENF-RES-03, ENF-ACC-04, ENF-I18N-03 (relecture par un musicien) ; documents de [11 §5](../EDB/11-juridique-et-conformite.md) ; [QO-20](../EDB/12-risques-hypotheses-questions.md).

## 4. Epics de la V2

| Epic | Exigences |
|---|---|
| EP-21 — Import photo et autres formats (PHO) | EF-IMP-04, EF-IMP-09, EF-IMP-10 ; avis juridique sur la reproduction de partitions ([11 §1.2](../EDB/11-juridique-et-conformite.md)) |
| EP-22 — Espace professeur (PRF) | EF-PRF-01 à EF-PRF-05 ; RG-05 |
| EP-23 — Synchronisation multi-appareils (SYN) | EF-BIB-04 |
| EP-24 — Motivation et suivi parental (MOT) | EF-PRO-06, EF-PRO-07, EF-PRO-08, EF-RES-13 |
| EP-25 — Expression et outils experts (EXP) | EF-ANA-11, EF-ANA-12, EF-ANA-14, EF-RES-11, EF-LEC-08, EF-LEC-09, EF-VIS-06, EF-VIS-09, EF-PRA-09, EF-PRA-10 ; ENF-ACC-05, ENF-COMP-06, ENF-MAINT-04 (complet) |

**Exigences « C » sans version dans l'EDB.** EF-VIS-06, EF-LEC-09, EF-PRA-09, EF-RES-12, EF-RES-13 et EF-PRO-09 n'ont pas de version cible explicite dans l'EDB. Le PRD les range en V1 (EF-RES-12, EF-PRO-09 : utiles à la bêta publique et au RGPD) ou en V2 (les autres). Ce rangement est une proposition, révisable à chaque revue de backlog.

**Hors versions.** Le portage iOS (ENF-COMP-01, priorité W) n'a pas d'epic : il sera traité après la V1, quand le budget le permettra ([07 §14.1](../EDB/07-contraintes-et-pistes-techniques.md)).
