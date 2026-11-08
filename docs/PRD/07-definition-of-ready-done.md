# 07 — Définition de prêt et définition de fini

Ces définitions sont adaptées à un **porteur unique**, qui décide, assisté d'**agents IA** qui rédigent, développent et relisent. Le risque R-14 (des agents peuvent produire des erreurs plausibles) impose deux règles : **une affirmation n'est acceptée qu'avec sa preuve** (résultat de test, mesure, extrait de licence, capture), et **un critère perceptif** (écoute, lisibilité à distance, justesse d'un enregistrement, compréhension d'un texte) **est vérifié par un humain**, jamais par un agent seul.

## 1. Définition de prêt (Definition of Ready)

Une story entre dans un incrément quand **toutes** les conditions suivantes sont remplies.

### 1.1 Contenu

| # | Condition | Vérifié par |
|---|---|---|
| R1 | Énoncé « En tant que… je veux… afin de… », persona identifié. | `product-owner` |
| R2 | Critères d'acceptation testables, chacun étiqueté par sa méthode de vérification ([03 §1.2](03-user-stories-mvp.md)) et, pour une story à cheval, par son incrément. | `qa-testeur` |
| R3 | Exigences EDB citées, existantes dans l'EDB en vigueur, et **identiques** dans la story et dans la [matrice](06-matrice-de-tracabilite.md) (contrôle automatique). | `product-owner` |
| R4 | Statut **Ferme**, ou condition levée : POC concluant (story conditionnelle), décision du porteur prise (story proposée). | Porteur |
| R5 | Aucune décision du [fichier 09](09-decisions-a-prendre.md) bloquante pour la story n'est en suspens. | `product-owner` |
| R6 | Story finissable dans un incrément ; sinon découpée. | `product-owner`, développeur |
| R14 | **Aucun critère ne contient de valeur « à fixer »** : chaque valeur [HYP] ou [PO] est figée, avec sa source, à l'entrée de la story ; tout changement ultérieur est tracé. | `qa-testeur` |

### 1.2 Dépendances

| # | Condition | Vérifié par |
|---|---|---|
| R7 | Dépendances finies ou planifiées dans le même incrément ; aucune dépendance vers un incrément plus tardif sans critère rattaché à cet incrément. | `product-owner` |
| R8 | POC dont dépend la story conclu ; rapport relu par `qa-testeur` et `architecte`. | `product-owner` |
| R9 | Décisions d'architecture nécessaires consignées (ADR) par `architecte`. | `architecte` |

### 1.3 Avis des spécialistes

| # | Condition | Quand | Vérifié par |
|---|---|---|---|
| R10 | Règles musicales relues (tolérances, vocabulaire, exemples). | Analyse, restitution, lecture, démonstrations | `pedagogue-piano` |
| R11 | Spécification d'écran disponible et relue, avec : stories couvertes, données affichées, actions et navigation, états (nominal, vide, chargement, erreur, hors ligne), disposition téléphone portrait et paysage et tablette, ce qui se lit au pupitre, textes FR et EN, accessibilité. | Story avec un écran ou un texte visible | `designer-ux` |
| R12 | Point juridique examiné (composant ou contenu nouveau, données personnelles, texte légal). | Selon la story | `juriste-conformite` |
| R13 | Données de test identifiées (sous-corpus, fichiers, scénarios). | Moteur d'analyse, import | `qa-testeur`, `expert-audio-mir` |

## 2. Définition de fini (Definition of Done)

### 2.1 Pour une story

| # | Condition | Preuve attendue |
|---|---|---|
| D1 | Tous les critères rattachés à l'incrément sont vérifiés selon leur étiquette. | Liste des CA avec résultat et preuve. |
| D2 | Tests automatisés écrits et au vert ; tests du noyau d'analyse sur le corpus pour toute story du moteur. | Rapport de l'intégration continue. |
| D3 | **Non-régression du banc** selon la règle de US-QUA-02 CA3 (même version du corpus ; MIDI : nombre de fausses erreurs, total et par fichier, à tolérance nulle ; micro : reproductibilité et règle en effectifs, par mode et par sous-corpus ; nouvelles fausses erreurs listées). | Rapport du banc. |
| D4 | Cibles de performance mesurées sur le **téléphone de référence** selon les protocoles de US-QUA-04 (95e centile pour les latences ; médiane et maximum pour les durées). | Rapport de mesure. |
| D5 | Vérification manuelle **selon la nature de la story** (écran, captation, données) : téléphone et émulateur, profils téléphone et tablette, portrait et paysage, mode avion ; la fiche de test indique les vérifications non applicables. | Fiche de test. |
| D6 | Accessibilité et conception : aucun résultat communiqué par la seule couleur ; commandes de séance ≥ 64 dp, dans les coins, espacées d'au moins 16 dp ; autres cibles ≥ 48 dp ; textes en sp, sans hauteur fixe des conteneurs de texte ; couleurs en jetons conformes au contraste AA ; descriptions des icônes pour TalkBack ; écrans principaux vérifiés avec une taille de police à 130 % ; textes lus au pupitre ≥ 28 sp. | Revue `qa-testeur` ; simulation des daltonismes. |
| D7 | **Licences vérifiées** : tout composant (licence compatible GPL-3.0-or-later) ou contenu (licence libre sans NC ni ND) ajouté est inscrit à l'inventaire, licence lue à la source ; `reuse lint` et le contrôle des dépendances au vert ; l'inventaire couvre le contenu natif des AAR, que le contrôle des dépendances ne voit pas ; aucun code ou contenu tiers recopié par un agent sans être inscrit avec sa source. | Inventaire, validation JUR. |
| D8 | Confidentialité, dans les variantes bêta et publique : pas de permission INTERNET ; sauvegarde Android désactivée (décision QO-36) ; l'application n'écrit aucun son dans un fichier ; micro ouvert seulement en séance et au réglage du micro ; aucun outil de test présent. | Contrôles automatiques et `qa-testeur`. |
| D9 | **Relecture croisée** : code par `qa-testeur` ; règles musicales par `pedagogue-piano` ; écrans et textes par `designer-ux` (avec la liste de contrôle du ton, [03 §1.3](03-user-stories-mvp.md)) ; points juridiques par `juriste-conformite`. Remarques bloquantes traitées. | Comptes rendus de relecture. |
| D10 | Documentation à jour : statut de la story, matrice (réciprocité contrôlée par script), ADR, notes de version. | Diff de la documentation. |
| D11 | Données préservées : migration depuis chaque version diffusée (dès US-HIS-03). | Test de migration. |
| D12 | **Acceptation par le porteur** pour toute story visible, après démonstration sur son téléphone, et à son piano pour les stories micro. | Accord consigné. |

**Vérifications reportées.** Un critère qui exige un matériel absent (piano numérique, tablette, autres appareils) est vérifié par simulation, avec preuve jointe, et inscrit au [registre des vérifications reportées](05-plan-de-release.md) avec son critère chiffré de bêta. La story est finie ; un échec en bêta ouvre une anomalie bloquante pour J3 ; les notes de version listent ces critères.

### 2.2 Pour un incrément

| # | Condition |
|---|---|
| DI1 | Tous les critères rattachés à l'incrément sont vérifiés ; ceux rattachés à un incrément ultérieur sont suivis dans leur story. |
| DI2 | APK construit par l'intégration continue depuis une étiquette du dépôt public, installé par-dessus l'incrément précédent sans perte ; il contient les mentions de licence à jour et sa page de diffusion le lien vers ses sources. |
| DI3 | Démonstration au porteur de ce qui est indiqué « Le porteur peut tester » dans le [plan](05-plan-de-release.md). |
| DI4 | Banc au vert sur le corpus public (intégration continue) et sur le corpus privé (rejoué par le porteur) ; corpus micro rejoué sur le téléphone ; rapports archivés. |
| DI5 | Aucune anomalie bloquante ouverte. |
| DI6 | Liste courte de recette des incréments précédents rejouée sur l'APK (30 minutes au plus) ; résultat archivé. |

### 2.3 Pour le MVP (jalon J2)

| # | Condition |
|---|---|
| DM1 | Incréments I0 à I6 finis (I5 s'il existe). |
| DM2 | Toutes les exigences M couvertes au sens de la [matrice](06-matrice-de-tracabilite.md), écarts partiels acceptés par le porteur. |
| DM3 | Cibles ENF-QA-01 atteintes sur le corpus (MIDI ≤ 1 %, micro ≤ 5 % [HYP]) et sur les séances réelles capturées (US-QUA-05). |
| DM4 | Critères du jalon J2 ([05 §3](05-plan-de-release.md)) satisfaits, dont 3 testeurs sans assistance. |
| DM5 | Inventaire des licences validé ; écran « À propos » et notice de confidentialité relus par JUR et validés par le porteur. |
| DM6 | Aucune anomalie bloquante ouverte ; anomalies majeures ouvertes acceptées une à une par le porteur. |
| DM7 | Campagne de mesures US-QUA-04 complète, sans écart non accepté. |

## 3. Gravité des anomalies

| Gravité | Définition | Conséquence |
|---|---|---|
| **Bloquante** | Perte ou corruption de données ; plantage reproductible ; fausse erreur systématique ; accès au micro hors séance ; donnée transmise hors de l'appareil ; manquement de licence ; cible de qualité (ENF-QA-01) dépassée | Empêche la fin de l'incrément et J2 |
| **Majeure** | Fonction inutilisable dans un cas courant ; cible de performance manquée ; texte trompeur ; défaut d'accessibilité sur un écran principal | Acceptée seulement par décision consignée du porteur |
| **Mineure** | Gêne sans contournement nécessaire ; défaut cosmétique | Planifiée |

## 4. Règles propres au travail avec des agents

1. **Pas de preuve, pas de « fini »** : un agent qui annonce un test réussi joint sa sortie ; une licence est citée avec son texte et sa source. Un agent ne recopie pas de code ou de contenu tiers sans l'inscrire à l'inventaire.
2. **Deux regards** : l'agent qui produit n'est jamais le seul à relire ; `qa-testeur` relit tout code.
3. **Le banc arbitre** les désaccords sur la qualité de l'analyse.
4. **Les sens restent humains** : écoute, lisibilité, annotation d'enregistrements, compréhension par un utilisateur : jugement du porteur ou de testeurs ([PORTEUR]).
5. **Le porteur décide** : périmètre, licence, distribution, publication, et toute question du [fichier 09](09-decisions-a-prendre.md) ; les validations juridiques à fort enjeu sont consignées par le porteur, et les points [avocat] tranchés par un avis humain qualifié avant la V1.
6. **Traçabilité** : une nouvelle exigence découverte en cours de route devient une question pour le porteur, pas une story ajoutée en silence. Les contributions externes sont acceptées sous DCO (signature `Signed-off-by`).
