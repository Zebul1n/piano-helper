# 06 — Matrice de traçabilité EDB → stories

Chaque exigence fonctionnelle (EF) et non fonctionnelle (ENF) de l'**EDB v0.7** est reliée aux stories qui la couvrent ([03](03-user-stories-mvp.md) pour le MVP, [04](04-user-stories-v1-v2.md) pour la V1 et la V2).

**Réciprocité.** Depuis la v0.2, la colonne « Stories » est **générée** à partir du champ « Exigences » des stories : une story apparaît sur la ligne d'une exigence si et seulement si elle cite cette exigence. Un contrôle par script vérifie les deux sens à chaque modification (définition de prêt R3, définition de fini D10).

**Couverture** :
- **Complète** : tout ce qui est prévu pour cette version est couvert par des stories fermes.
- **Partielle** : une partie est couverte ; l'écart est expliqué et renvoie à une décision ([09](09-decisions-a-prendre.md)) ou à une story ultérieure.
- **Proposée** : couverte au MVP seulement si le porteur accepte la décision citée (aucune depuis les décisions du 2026-11-07).
- **Reportée** : prévue en V1 ou V2, conformément à l'EDB.
- **Hors périmètre** : exclue pour l'instant (W).

## 1. Synthèse

| Indicateur | Valeur |
|---|---|
| Exigences EF de l'EDB | 108 (dont 5 EF-PRF ; EF-PRA-11 ajoutée par l'EDB v0.6 ; aucune ajoutée par l'EDB v0.7) |
| Exigences ENF de l'EDB | 54 |
| Exigences **M** (y compris « M / S », « M / C » et « M dès qu'un serveur existe ») | 76 (47 EF, 29 ENF) |
| Exigences M **sans story** | **0** |
| Exigences M **couvertes partiellement** au MVP | 4 (voir §4), plus ENF-SEC-01 sans objet au MVP |
| Exigences S ou C **proposées** au MVP | 0 (toutes décidées le 2026-11-07) |
| Exigences S ou C **couvertes au MVP** par des stories fermes | Complètes : EF-PRA-08, EF-PRA-11, EF-ECO-07, EF-ECO-10. Part MVP précisée par l'EDB v0.6 : EF-ECO-08, EF-ANA-08, EF-ANA-10, EF-RES-06, ENF-ACC-01, ENF-I18N-03, ENF-MAINT-04 (§5) |
| Exigence sans story | ENF-COMP-01 (W, portage iOS) |

## 2. Exigences fonctionnelles

### 2.1 Import (IMP)

| Exigence | Prio | Version | Stories | Couverture |
|---|:-:|:-:|---|---|
| EF-IMP-01 | M | MVP | US-IMP-01 | Complète |
| EF-IMP-02 | M | MVP | US-IMP-02 | Complète |
| EF-IMP-03 | S | V1 | US-OMR-01 | Reportée |
| EF-IMP-04 | C | V2 | US-PHO-01 | Reportée |
| EF-IMP-05 | M | MVP | US-IMP-03 | Complète |
| EF-IMP-06 | M | MVP | US-IMP-04 | Complète (étiquette à l'import en V1 selon EF-IMP-06 CA2 de l'EDB v0.6, décision [QO-24 d](09-decisions-a-prendre.md)) |
| EF-IMP-07 | S | V1 | US-OMR-02 | Reportée |
| EF-IMP-08 | S | V1 | US-OMR-03 | Reportée |
| EF-IMP-09 | C | V2 | US-PHO-02 | Reportée |
| EF-IMP-10 | C | V2 | US-PHO-03 | Reportée |
| EF-IMP-11 | S | MVP / V1 | US-IMP-01, US-CNT-01 | Reportée ; règle automatique au MVP (US-IMP-01 CA8) |
| EF-IMP-12 | M | MVP | US-IMP-05, US-BIB-07 | Complète |

### 2.2 Bibliothèque (BIB)

| Exigence | Prio | Version | Stories | Couverture |
|---|:-:|:-:|---|---|
| EF-BIB-01 | M | MVP | US-BIB-01, US-BIB-02 | Complète |
| EF-BIB-02 | S | V1 | US-CNT-02 | Reportée |
| EF-BIB-03 | M | MVP | US-BIB-03, US-BIB-04, US-BIB-05, US-BIB-07 | Complète |
| EF-BIB-04 | C | V2 | US-SYN-01 | Reportée |
| EF-BIB-05 | M | MVP | US-CPT-06, US-BIB-06 | Complète |

### 2.3 Visualisation (VIS)

| Exigence | Prio | Version | Stories | Couverture |
|---|:-:|:-:|---|---|
| EF-VIS-01 | M | MVP | US-VIS-01 | Complète |
| EF-VIS-02 | M / S | MVP / V1 | US-VIS-02, US-CNT-03 | Complète pour la part M (bande horizontale en paysage, décision [QO-27](09-decisions-a-prendre.md), reconfirmée en bêta) ; part S reportée |
| EF-VIS-03 | M | MVP | US-VIS-03 | Complète (CA2 tablette vérifié en bêta ; CA3 précisé par l'EDB v0.7 : recalcul de la mise en page en mode page seulement) |
| EF-VIS-04 | M | MVP | US-VIS-04 | Complète |
| EF-VIS-05 | M | MVP | US-VIS-05 | Complète |
| EF-VIS-06 | C | V2 | US-EXP-07 | Reportée |
| EF-VIS-07 | S | V1 | US-CNT-04 | Reportée |
| EF-VIS-08 | S | V1 | US-CNT-05 | Reportée |
| EF-VIS-09 | C | V2 | US-EXP-08 | Reportée |
| EF-VIS-10 | S | V1 | US-CNT-06 | Reportée |
| EF-VIS-11 | M | MVP | US-VIS-06 | Complète |
| EF-VIS-12 | M | MVP | US-VIS-07 | Complète |

### 2.4 Lecture (LEC)

| Exigence | Prio | Version | Stories | Couverture |
|---|:-:|:-:|---|---|
| EF-LEC-01 | M | MVP | US-LEC-01 | Complète |
| EF-LEC-02 | M | MVP | US-LEC-02 | Complète |
| EF-LEC-03 | M | MVP | US-LEC-03 | Complète |
| EF-LEC-04 | M | MVP | US-LEC-04, US-PRA-03 | Complète |
| EF-LEC-05 | M | MVP | US-LEC-05 | Complète |
| EF-LEC-06 | M | MVP | US-LEC-06 | Complète |
| EF-LEC-07 | M / S | MVP / V1 | US-LEC-07, US-CNT-07 | Complète pour la part M (reprises simples) ; part S reportée |
| EF-LEC-08 | C | V2 | US-EXP-05 | Reportée |
| EF-LEC-09 | C | V2 | US-EXP-06 | Reportée |
| EF-LEC-10 | S | V1 | US-CNT-08 | Reportée |

### 2.5 Pratique (PRA)

| Exigence | Prio | Version | Stories | Couverture |
|---|:-:|:-:|---|---|
| EF-PRA-01 | M | MVP | US-PRA-01 | Complète |
| EF-PRA-02 | M | MVP | US-ECO-11, US-ANA-06, US-PRA-02, US-PRA-03 | Complète (CA4 au micro avec écouteurs filaires, décisions [QO-23](09-decisions-a-prendre.md) et [QO-24 b](09-decisions-a-prendre.md)) |
| EF-PRA-03 | M | MVP | US-ECO-08, US-ECO-11, US-PRA-04 | **Partielle** : complète en MIDI ; au micro selon POC-01 (US-ECO-08 conditionnelle) |
| EF-PRA-04 | S | V1 | US-PRV-03 | Reportée |
| EF-PRA-05 | M | MVP | US-ECO-11, US-PRA-05, US-PRA-09 | Complète (décompte en boucle avec US-PRA-09, décision [QO-26](09-decisions-a-prendre.md)) |
| EF-PRA-06 | S | V1 | US-PRV-04 | Reportée |
| EF-PRA-07 | S | V1 | US-PRV-05 | Reportée |
| EF-PRA-08 | S | MVP | US-PRA-07, US-PRA-08 | Complète (entrée au MVP par l'EDB v0.5) |
| EF-PRA-09 | C | V2 | US-EXP-09 | Reportée |
| EF-PRA-10 | C | V2 | US-EXP-10 | Reportée |
| EF-PRA-11 | S | MVP | US-PRA-09 | Complète (exigence ajoutée par l'EDB v0.6, décision [QO-26](09-decisions-a-prendre.md)) |

### 2.6 Écoute (ECO)

| Exigence | Prio | Version | Stories | Couverture |
|---|:-:|:-:|---|---|
| EF-ECO-01 | M | MVP | US-CPT-02, US-CPT-03, US-ECO-01 | Complète |
| EF-ECO-02 | M | MVP | US-ECO-02, US-QUA-03 | Complète par simulation (décision QO-22) ; vérification réelle en bêta |
| EF-ECO-03 | S | V1 | US-PRV-02 | Reportée |
| EF-ECO-04 | M | MVP / V1 | US-ECO-07, US-ECO-08, US-PRV-01 | **Partielle** : jeu continu au micro conditionné à POC-01 |
| EF-ECO-05 | M | MVP | US-ECO-03, US-ECO-04 | Complète |
| EF-ECO-06 | M | MVP | US-CPT-06, US-ECO-05, US-ECO-06 | Complète (latence MIDI en option avancée ; au micro, test de cohérence des horodatages, correction seulement en cas d'écart : EDB v0.7) |
| EF-ECO-07 | S | MVP | US-ECO-10 | Complète (décision [QO-23](09-decisions-a-prendre.md)) |
| EF-ECO-08 | S | MVP / V1 | US-ECO-11, US-PRV-05 | Complète pour la forme minimale du MVP (CA1 à CA3, décision [QO-23](09-decisions-a-prendre.md)) ; complète en V1 |
| EF-ECO-09 | S | V1 | US-PRV-06 | Reportée |
| EF-ECO-10 | S | MVP | US-ECO-09 | Complète (décision [QO-23](09-decisions-a-prendre.md)) |

### 2.7 Analyse (ANA)

| Exigence | Prio | Version | Stories | Couverture |
|---|:-:|:-:|---|---|
| EF-ANA-01 | M | MVP | US-ECO-07, US-ANA-02 | Complète |
| EF-ANA-02 | M | MVP | US-ANA-01, US-ANA-03 | Complète |
| EF-ANA-03 | M | MVP | US-ANA-04 | Complète |
| EF-ANA-04 | M | MVP | US-ANA-05, US-PRA-09 | **Partielle** : jeu continu et boucle (EF-PRA-11) couverts ; mode suivi en V1 (EF-PRA-04, S) |
| EF-ANA-05 | S | V1 | US-ANX-01 | Reportée |
| EF-ANA-06 | M | MVP | US-ANA-06 | Complète |
| EF-ANA-07 | S | V1 | US-ANX-02 | Reportée |
| EF-ANA-08 | S | MVP / V1 | US-CPT-03, US-ANA-07, US-ANX-03 | Complète pour la part MVP (sans ornements, décision [QO-24 c](09-decisions-a-prendre.md)) ; complète en V1 |
| EF-ANA-09 | S | V1 | US-ANA-07, US-ANX-04 | Reportée ; règle de non-évaluation des ornements au MVP (US-ANA-07 CA3) |
| EF-ANA-10 | S | MVP / V1 | US-ANA-01, US-CNT-07 | Complète pour la part MVP (reprises simples, décision [QO-24 c](09-decisions-a-prendre.md)) ; complète en V1 |
| EF-ANA-11 | C | V2 | US-EXP-01 | Reportée |
| EF-ANA-12 | C | V2 | US-EXP-02 | Reportée |
| EF-ANA-13 | M | MVP | US-ANA-09 | Complète (mode attente : décision [QO-52](../DAT/14-decisions-a-prendre.md), US-ANA-09 CA2 et CA3) |
| EF-ANA-14 | C | V2 | US-EXP-03 | Reportée |

### 2.8 Restitution (RES)

| Exigence | Prio | Version | Stories | Couverture |
|---|:-:|:-:|---|---|
| EF-RES-01 | M | MVP | US-RES-01 | Complète (jeu continu ; en attente, l'erreur est toujours affichée) |
| EF-RES-02 | M | MVP | US-RES-02 | Complète |
| EF-RES-03 | M | MVP | US-PRA-07, US-RES-03 | Complète |
| EF-RES-04 | M | MVP | US-RES-04, US-RES-05 | Complète |
| EF-RES-05 | M | MVP | US-RES-06 | Complète (formule multiplicative, décision [QO-39](09-decisions-a-prendre.md) ; scores par main en V1) |
| EF-RES-06 | S | MVP / V1 | US-RES-03, US-RSX-01 | Complète pour la part MVP (jusqu'à 3 passages et lien vers la partition, décision [QO-24 a](09-decisions-a-prendre.md)) ; complète en V1 |
| EF-RES-07 | S | V1 | US-RSX-02 | Reportée |
| EF-RES-08 | S | V1 | US-RSX-03 | Reportée |
| EF-RES-09 | S | V1 | US-RSX-04 | Reportée (lien « Voir sur la partition » au MVP, US-RES-03 CA4) |
| EF-RES-10 | S | V1 | US-RSX-05 | Reportée |
| EF-RES-11 | C | V2 | US-EXP-04 | Reportée |
| EF-RES-12 | C | V1 | US-RSX-06 | Reportée (style unique sobre au MVP, décision [QO-24 g](09-decisions-a-prendre.md)) |
| EF-RES-13 | C | V2 | US-MOT-04 | Reportée |

### 2.9 Progression (PRO)

| Exigence | Prio | Version | Stories | Couverture |
|---|:-:|:-:|---|---|
| EF-PRO-01 | S | V1 | US-PRG-01 | Reportée |
| EF-PRO-02 | M | MVP | US-BIB-07, US-HIS-01, US-HIS-02 | Complète |
| EF-PRO-03 | S | V1 | US-PRG-02 | Reportée |
| EF-PRO-04 | S | V1 | US-PRG-03 | Reportée |
| EF-PRO-05 | S | V1 | US-PRG-04 | Reportée |
| EF-PRO-06 | C | V2 | US-MOT-01 | Reportée |
| EF-PRO-07 | C | V2 | US-MOT-02 | Reportée |
| EF-PRO-08 | C | V2 | US-MOT-03 | Reportée |
| EF-PRO-09 | C | V1 | US-PRG-05 | Reportée |

### 2.10 Compte et paramètres (CPT)

| Exigence | Prio | Version | Stories | Couverture |
|---|:-:|:-:|---|---|
| EF-CPT-01 | M | MVP | US-CPT-01 | Complète |
| EF-CPT-02 | M | MVP | US-CPT-02, US-CPT-03 | Complète (style de retour ajouté au profil en V1 selon l'EDB v0.6, décision [QO-24 g](09-decisions-a-prendre.md)) |
| EF-CPT-03 | S | V1 | US-CPX-01 | Reportée |
| EF-CPT-04 | S | V1 | US-CPX-02 | Reportée |
| EF-CPT-05 | M | MVP | US-CPT-04 | Complète (français et anglais ; thème clair seul au MVP selon l'EDB v0.6, décisions [QO-24 e et f](09-decisions-a-prendre.md)) |
| EF-CPT-06 | M | MVP | US-CPT-05 | Complète (partie serveur sans objet au MVP) |
| EF-CPT-07 | C | V1 | US-CPX-03 | Reportée, selon QO-01 |

### 2.11 Espace professeur (PRF)

| Exigence | Prio | Version | Stories | Couverture |
|---|:-:|:-:|---|---|
| EF-PRF-01 | W | V2 | US-PRF-01 | Reportée |
| EF-PRF-02 | W | V2 | US-PRF-02 | Reportée |
| EF-PRF-03 | W | V2 | US-PRF-03 | Reportée |
| EF-PRF-04 | W | V2 | US-PRF-04 | Reportée |
| EF-PRF-05 | W | V2 | US-PRF-05 | Reportée |

## 3. Exigences non fonctionnelles

| Exigence | Prio | Version | Stories | Couverture |
|---|:-:|:-:|---|---|
| ENF-PERF-01 | M | MVP | US-ECO-07, US-ANA-09, US-PRA-02, US-QUA-04 | Complète (mesurée depuis l'attaque de la note ; au micro, cible propre au grave si la mesure l'exige, décision [QO-55](../DAT/14-decisions-a-prendre.md)) |
| ENF-PERF-02 | M | MVP | US-ECO-08, US-ANA-09, US-RES-01, US-QUA-04 | Complète |
| ENF-PERF-03 | M | MVP | US-VIS-04, US-QUA-04 | Complète |
| ENF-PERF-04 | M | MVP | US-ECO-08, US-ANA-09, US-RES-03, US-QUA-04 | Complète en MIDI ; au micro, cible choisie après mesure en I5 (décision [QO-43](09-decisions-a-prendre.md)) |
| ENF-PERF-05 | M | MVP | US-VIS-08, US-QUA-04 | Complète |
| ENF-PERF-06 | M | MVP | US-IMP-01, US-QUA-04 | Complète |
| ENF-PERF-07 | S | V1 | US-OMR-01 | Reportée |
| ENF-PERF-08 | S | V1 | US-PUB-03 | Reportée |
| ENF-PERF-09 | S | V1 | US-PUB-03 | Reportée |
| ENF-PERF-10 | M | MVP | US-LEC-01, US-QUA-04 | Complète |
| ENF-RES-01 | S | V1 | US-PUB-03 | Reportée |
| ENF-RES-02 | S | V1 | US-PUB-03 | Reportée |
| ENF-RES-03 | S | V1 | US-PUB-03 | Reportée (dès le MVP, versions diffusées en 64 bits seulement, décision [QO-47](../DAT/14-decisions-a-prendre.md)) |
| ENF-RES-04 | M | MVP | US-VIS-08, US-QUA-04 | Complète (téléphone et émulateur à mémoire réduite ; entrée de gamme en bêta) |
| ENF-COMP-01 | W | — | — | Hors périmètre (portage iOS après la V1) |
| ENF-COMP-02 | M | MVP | US-CNF-04 | Complète |
| ENF-COMP-03 | M | MVP | US-VIS-07 | Complète |
| ENF-COMP-04 | M | MVP / V1 | US-QUA-04, US-PUB-03 | **Partielle** au MVP, conforme au phasage de l'exigence (3 appareils avant la V1) |
| ENF-COMP-05 | S | V1 | US-PUB-04 | Reportée |
| ENF-COMP-06 | C | V2 | US-EXP-12 | Reportée (au MVP, séance suspendue quand l'application perd la position d'activité principale : US-PRA-06 CA1, EDB v0.7) |
| ENF-OFF-01 | M | MVP | US-LEC-01, US-CNF-01 | Complète |
| ENF-OFF-02 | S | V1 | US-OMR-04, US-CPX-01 | Reportée |
| ENF-OFF-03 | S | V1 | US-CPX-01 | Reportée |
| ENF-FIA-01 | M | MVP | US-HIS-01 | Complète |
| ENF-FIA-02 | M | MVP | US-IMP-07 | Complète |
| ENF-FIA-03 | M | MVP | US-HIS-03 | Complète |
| ENF-FIA-04 | M | MVP | US-ECO-02, US-PRA-06 | Complète |
| ENF-FIA-05 | S | V1 | US-PUB-02 | Reportée |
| ENF-QA-01 | M | MVP | US-ECO-07, US-ECO-08, US-ANA-04, US-ANA-08, US-RES-07, US-QUA-01, US-QUA-02, US-QUA-05 | Complète (cibles micro [HYP] confirmées par POC-01) |
| ENF-QA-02 | M | MVP | US-QUA-02, US-QUA-05 | Complète |
| ENF-QA-03 | S | V1 | US-ANX-05 | Reportée |
| ENF-ACC-01 | S | MVP / V1 | US-PRA-08, US-PRV-07 | Partielle au MVP (arrêt et reprise en jouant) ; complète en V1 |
| ENF-ACC-02 | M | MVP | US-RES-02, US-RES-04 | Complète |
| ENF-ACC-03 | S | V1 | US-CNT-06 | Reportée (règles de conception dès le MVP, définition de fini D6) |
| ENF-ACC-04 | S | V1 | US-PUB-05 | Reportée (contrôle à 130 % dès le MVP, D6) |
| ENF-ACC-05 | C | V2 | US-EXP-11 | Reportée |
| ENF-ACC-06 | S | V1 | US-RSX-06 | Reportée |
| ENF-ACC-07 | M | MVP | US-VIS-03 | Complète (tablette réelle en bêta) |
| ENF-I18N-01 | M | MVP | US-CPT-04 | Complète (français et anglais, décision [QO-24 f](09-decisions-a-prendre.md)) |
| ENF-I18N-02 | M / C | MVP / V1 | US-CPT-04, US-CNT-05 | Complète pour la part M ; notation allemande reportée |
| ENF-I18N-03 | S | MVP / V1 | US-CPT-04, US-PUB-06 | Complète pour la part MVP (lexique bilingue validé par PED, US-CPT-04 CA8) ; relecture par un musicien anglophone en V1 |
| ENF-SEC-01 | M dès qu'un serveur existe | V1 | US-CPX-01 | **Sans objet au MVP** (aucun serveur, garanti par US-CNF-01) |
| ENF-SEC-02 | S | V1 | US-CPX-01 | Reportée |
| ENF-SEC-03 | S | V1 | US-CPX-01 | Reportée |
| ENF-SEC-04 | M | MVP | US-IMP-06 | Complète |
| ENF-SEC-05 | S | V1 | US-OMR-04 | Reportée |
| ENF-SEC-06 | M | MVP | US-ECO-04 | Complète |
| ENF-CONF-01 | M | MVP | US-ECO-03, US-ECO-04, US-ANA-02, US-CNF-01, US-CNF-06 | Complète |
| ENF-CONF-02 | M | MVP / V1 | US-CNF-01, US-PUB-02 | Complète (aucune télémétrie au MVP) |
| ENF-CONF-03 | M | MVP | US-CPT-01, US-CNF-01 | Complète |
| ENF-MAINT-01 | M | MVP | US-QUA-02 | Complète |
| ENF-MAINT-02 | S | V1 | US-ANX-06 | Reportée (au MVP, paramètres embarqués et versionnés ; configuration distante seulement quand un serveur existera : EDB v0.7) |
| ENF-MAINT-03 | S | V1 | US-PUB-02 | Reportée |
| ENF-MAINT-04 | C | MVP / V2 | US-CNF-05, US-EXP-13 | Complète pour la forme réduite du MVP (rapport de test, décision [QO-28](09-decisions-a-prendre.md)) ; complète en V2 |

## 4. Exigences M couvertes partiellement au MVP

**Aucune exigence M n'est sans story.**

| Exigence | Partie non couverte au MVP | Suite |
|---|---|---|
| EF-ECO-04 | Jeu continu au micro si POC-01 non concluant | US-ECO-08 (conditionnelle, « expérimental » selon la décision [QO-29](09-decisions-a-prendre.md)) ou US-PRV-01 |
| EF-PRA-03 | Jeu continu au micro, même raison | US-ECO-08 ou US-PRV-01 |
| EF-ANA-04 | Mode suivi (EF-PRA-04, S, V1) | US-PRV-03 en V1 |
| ENF-COMP-04 | 3 appareils de référence | US-PUB-03, avant la V1 (phasage de l'exigence) |

Depuis l'EDB v0.6, six exigences M ne sont plus partielles : EF-PRA-02 et EF-PRA-05 sont couvertes par des stories devenues fermes (US-ECO-11, US-PRA-09) ; EF-IMP-06, EF-CPT-02, EF-CPT-05 et ENF-I18N-01 ont été précisées par l'EDB selon les décisions QO-24 d, e, f et g.

Exigences M vérifiées seulement par simulation, sur un seul appareil ou par le seul porteur au MVP, et donc revérifiées en bêta ([registre, 05 §5](05-plan-de-release.md)) : EF-ECO-02, EF-ECO-04, EF-VIS-02 (mode d'affichage, décision QO-27), EF-VIS-03 (CA2, et protocole de lisibilité selon la décision QO-44), ENF-ACC-07, ENF-RES-04, ENF-COMP-04.

## 5. Exigences S ou C avancées au MVP par décision du porteur

Ces exigences, proposées au MVP par le PRD v0.2, y sont entrées par les décisions du 2026-11-07 ; l'EDB v0.6 précise la part nécessaire au MVP.

| Exigence | Story | Justification | Décision |
|---|---|---|---|
| EF-ECO-07 | US-ECO-10 | Une mauvaise captation produirait des retours faux sans explication | [QO-23](09-decisions-a-prendre.md) |
| EF-ECO-08 (forme minimale) | US-ECO-11 | Métronome, décompte et autre main captés par le micro | [QO-23](09-decisions-a-prendre.md) |
| EF-ECO-10 | US-ECO-09 | Pianos rarement accordés exactement à 440 Hz, accord étiré | [QO-23](09-decisions-a-prendre.md) |
| EF-PRA-11 | US-PRA-09 | Répéter un passage est le cœur du travail | [QO-26](09-decisions-a-prendre.md) |
| EF-ANA-08 (sans ornements), EF-ANA-10 (reprises simples) | US-ANA-07, US-ANA-01 | Niveaux d'exigence et ordre de jeu exact dès le MVP | [QO-24 c](09-decisions-a-prendre.md) |
| EF-RES-06 (forme minimale) | US-RES-03 | Compte-rendu actionnable (question 3 de l'EDB 04 §4) | [QO-24 a](09-decisions-a-prendre.md) |
| ENF-I18N-03 (lexique) | US-CPT-04 | Terminologie juste dès l'anglais du MVP | [QO-24 f](09-decisions-a-prendre.md) |
| ENF-MAINT-04 (forme réduite) | US-CNF-05 | Recueillir les retours de bêta sans serveur | [QO-28](09-decisions-a-prendre.md) |

## 6. Autres éléments de l'EDB tracés

| Élément EDB | Stories |
|---|---|
| Parcours P1 — premier lancement | US-CPT-01, US-CPT-02, US-CPT-06, US-ECO-03, US-ECO-05 |
| Parcours P2 — importer | US-IMP-01 à US-IMP-07 ; US-OMR-01 à US-OMR-03 |
| Parcours P3 — découvrir un morceau | US-VIS-02, US-LEC-01 à US-LEC-06 ; US-CNT-04 |
| Parcours P4 — mode attente | US-PRA-02, US-RES-02 |
| Parcours P5 — jeu continu | US-PRA-04, US-PRA-05, US-PRA-08, US-RES-01, US-RES-03, US-ECO-08 ; US-RSX-01 à US-RSX-05 |
| Parcours P6 — passage difficile | US-PRV-04 |
| Parcours P7 — progression | US-PRG-01 à US-PRG-03 ; US-MOT-01 |
| Parcours P8 — suivi parental | US-MOT-02 |
| Parcours P9 — professeur | US-PRF-01 à US-PRF-04 |
| [06 §4](../EDB/06-analyse-du-jeu-et-erreurs.md) — tolérances et cas particuliers | US-ANA-05, US-ANA-06, US-ANA-07 |
| [06 §6](../EDB/06-analyse-du-jeu-et-erreurs.md) — confiance et filtrage | US-ANA-08, US-RES-07 |
| [06 §7](../EDB/06-analyse-du-jeu-et-erreurs.md) — hiérarchisation | US-RES-03 ; US-RSX-01, US-RSX-03, US-MOT-04 |
| [06 §9](../EDB/06-analyse-du-jeu-et-erreurs.md) — protocole d'évaluation | US-QUA-01, US-QUA-02, US-QUA-05 |
| [11 §3](../EDB/11-juridique-et-conformite.md) — licences, inventaire, écran des licences | US-CNF-02, US-CNF-03, US-CNF-04 |
| RGPD-01 | US-CNF-06 |
| RGPD-02 | US-CNF-05 ([EDB 11 §2.4](../EDB/11-juridique-et-conformite.md), rapport de test) |
| RGPD-03 | US-CPT-05, US-PRG-05 |
| RGPD-08 | US-PUB-01 |
| RG-01 | US-BIB-04 |
| RG-02 | US-IMP-07, US-OMR-03 |
| RG-03 | US-VIS-01, US-OMR-03 |
| RG-04 | US-ANA-07, US-HIS-01 |
| RG-05 | US-PRF-05 |
| [13 §4](../EDB/13-jalons-et-demarche.md) — banc d'évaluation | US-QUA-02 |
| [13 §5](../EDB/13-jalons-et-demarche.md) — signalement de fausses erreurs en bêta | US-RES-08, US-CNF-05 |
