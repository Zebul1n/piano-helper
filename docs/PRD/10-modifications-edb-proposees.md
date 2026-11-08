# 10 — Modifications de l'EDB proposées

Les relecteurs du PRD v0.1 ont proposé ces modifications de l'EDB v0.5. Le porteur ayant pris toutes les décisions dont elles dépendaient le 2026-11-07 ([09](09-decisions-a-prendre.md)), **les 45 modifications sont appliquées dans l'[EDB v0.6](../EDB/README.md)** (colonne « État », avec l'endroit où elles figurent). Les numéros de section cités dans la colonne « Endroit » sont ceux de l'EDB v0.5 ; une nouvelle section a été créée pour MOD-43 (11 §2.4). Les valeurs marquées [PO] dans les stories sont notées [HYP] dans l'EDB, qui ne connaît que ce marqueur.

Origine : identifiants des remarques des [revues](revues/) (PED, UX, QA, JUR).

## 1. Analyse du jeu et scores (EDB 06)

| ID | Endroit | Modification proposée | Origine | Décision | État |
|---|---|---|---|---|---|
| MOD-01 | 06 §5.4 | Score global multiplicatif : S_global = S_notes × (0,55 + 0,30 × S_rythme/100 + 0,15 × S_continuité/100) | PED-15 | [QO-39](09-decisions-a-prendre.md), décidée | **Appliquée** dans l'EDB v0.6 (06 §5.4) |
| MOD-02 | 06 §5.3 | Pénalités de continuité rapportées à la longueur jouée (par tranche de 16 mesures, au moins une tranche) | PED-40 | [QO-39](09-decisions-a-prendre.md), décidée | **Appliquée** dans l'EDB v0.6 (06 §5.3) |
| MOD-03 | 06 §5.1 et §5.4 | En mode attente, note « juste » = trouvée du premier coup ; une seule erreur par événement ; nombre d'essais conservé | PED-14, QA-04 | Précision du PRD confirmée par la décision QO-39 | **Appliquée** dans l'EDB v0.6 (06 §3.1, §5.1, §5.4 ; EF-PRA-02 CA6) |
| MOD-04 | 06 §4.3 (nouvelles lignes) | Point d'orgue et césure : le curseur attend l'attaque suivante, la référence temporelle repart de là, rythme non évalué sur cet événement ; rit., rall., allarg., accel. : rythme non évalué jusqu'au prochain « a tempo » | PED-01 | [QO-40](09-decisions-a-prendre.md), décidée | **Appliquée** dans l'EDB v0.6 (06 §4.3) |
| MOD-05 | 06 §3.2 et §4.2 | Hésitation et arrêt mesurés comme le retard de l'attaque sur son instant attendu ; recalage de la référence temporelle et du curseur après chaque hésitation, arrêt ou retour en arrière ; au micro, « silence » = aucune nouvelle attaque ; la note qui suit une hésitation n'est pas comptée en plus « en retard » | PED-02, PED-30 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (06 §3.2, §4.2) |
| MOD-06 | 06 §4.1 | Seuil d'arrêt = max(seuil du niveau ; 2 temps au tempo de la séance) ; pause après seuil + 3 s ; fin après 30 s de pause ; aucune pause avant 2 min en mode attente | PED §3.1, UX-09 | [QO-25](09-decisions-a-prendre.md), décidée | **Appliquée** dans l'EDB v0.6 (06 §4.1 ; EF-PRA-08) |
| MOD-07 | 06 §4.3, accord arpégé | Fenêtre = max(fenêtre du niveau ; 60 ms × (nombre de notes − 1)), plafonnée à la moitié de la durée écrite ; référence rythmique = première note ; ordre non sanctionné ; accord plaqué accepté ; accord de plus d'une octave dans une main traité comme arpégé | PED-12 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (06 §4.3 ; EF-ANA-06) |
| MOD-08 | 06 §4.3, appogiatures et ornements | Note principale jugée avec une tolérance élargie à la moitié de sa durée après une petite note ; petite note absente hors du score ; acciaccature écrasée acceptée ; événement orné validé par la note principale ou sa voisine diatonique ; ornement omis non fautif | PED-10, PED-11 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (06 §4.3 ; EF-ANA-09) |
| MOD-09 | 06 §3.1, sous-types | Sous-types d'erreur d'altération : omise (armure, accidentelle), bécarre omis, altération reportée après la barre, altération ajoutée ; formulations sans supposer la cause | PED-13 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (06 §3.1) |
| MOD-10 | 06 §4.3 (nouvelles lignes) | Unisson entre voix ou mains = une seule attaque attendue ; liaison « laissez vibrer » ignorée ; indications d'octave (8va, 8vb, 15ma) appliquées aux hauteurs ; trémolo écrit non évalué ; notes « cue » non attendues | PED-29, CL-04 à CL-06 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (06 §4.3) |
| MOD-11 | 06 §2.2 et §4.1, au micro | Tolérance et fenêtre d'accord effectives = max(valeur du niveau après plafond ; 2σ de l'incertitude de détection) ; niveau exigeant au micro seulement si 2σ ≤ 50 ms ; marque individuelle de rythme à 1,5 × la tolérance | PED §3.3 | [QO-32](09-decisions-a-prendre.md), décidée | **Appliquée** dans l'EDB v0.6 (06 §2.2, §4.1 ; EF-ANA-04, EF-ANA-06) |
| MOD-12 | 06 §4.3, piano désaccordé | Tolérance plus large dans les deux octaves extrêmes (accord étiré) ; plage de diapason étendue vers 430 Hz si POC-01 le permet | PED-32, CL-30 | Précision appliquée par le PRD (valeurs avec `expert-audio-mir`) | **Appliquée** dans l'EDB v0.6 (06 §4.3 ; EF-ECO-10) |
| MOD-13 | 06 §7 | Gravité : arrêt ≥ fausse ; hésitation = rythme ; poids proposés manquée = fausse = arrêt = 3, rythme = hésitation = 2, en trop = 1, rejouée = 0,5 ; un point positif de la séance affiché avant les passages à retravailler | PED-16, QA-43 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (06 §7) |
| MOD-14 | 06 §9.1 | Taille minimale du corpus (≥ 3 000 notes MIDI correctes sur ≥ 20 pièces ; ≥ 1 000 notes micro par mode ; ≥ 200 notes par sous-ensemble) ; erreurs scénarisées avant l'enregistrement ; corpus public et corpus privé ; données expressives exclues du taux de fausses erreurs de rythme ; pas de pièce « swing » ; bruit ambiant de sources libres, sans voix ni programme protégé | QA-05, QA-07, PED-21, PED-38, JUR-09 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (06 §9.1) |
| MOD-15 | 06 §9.2 et §9.3 | Non-régression : nombre de fausses erreurs en MIDI, au total et par fichier, à tolérance nulle ; micro : +0,1 point au plus, exécution déterministe ; même version du corpus ; intervalles de confiance publiés ; nouvelles métriques : validations indues et non-validations du mode attente, précision et rappel des erreurs | QA-01, QA-04, QA-05, QA-08 | Précision appliquée par le PRD (tolérance micro à valider avec `expert-audio-mir`) | **Appliquée** dans l'EDB v0.6 (06 §9.2, §9.3) |

## 2. Exigences fonctionnelles (EDB 05)

| ID | Exigence | Modification proposée | Origine | Décision | État |
|---|---|---|---|---|---|
| MOD-16 | EF-RES-01 | Le retour en direct (aucun, discret, complet) ne s'applique qu'au jeu continu ; en mode attente, l'erreur est toujours affichée | UX-20 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (EF-RES-01) |
| MOD-17 | EF-PRA-05 | Décompte d'au moins 3 pulsations (2 mesures par défaut en 2/4, 6/8, 3/8) ; démarrage par défaut en jouant une note ; règle de l'anacrouse | PED-05, PED-25, UX-08 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (EF-PRA-05 CA1 à CA4) |
| MOD-18 | EF-PRA-08 | Pause (et non fin) après le silence ; reprise en jouant ; fin après 30 s de pause | PED-17, UX-09 | [QO-25](09-decisions-a-prendre.md), décidée | **Appliquée** dans l'EDB v0.6 (EF-PRA-08 CA1 à CA5) |
| MOD-19 | EF-PRA-02 | Note en trop tolérée seulement si elle est à 1 ou 2 demi-tons d'une note attendue et seule par main ; son d'erreur désactivé par défaut et indisponible au micro | PED-03, PED-33 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (EF-PRA-02 CA1, CA5 ; EF-ECO-08) |
| MOD-20 | EF-LEC-03 | Tempo par défaut : indication métronomique, sinon indication textuelle, sinon chiffrage (tables de PED) ; tempo estimé affiché comme tel ; tempo de référence du morceau réglable ; subdivision automatique sous 40 battements par minute | PED-08, PED-25 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (EF-LEC-03 CA2, CA3 ; EF-LEC-05 CA1) |
| MOD-21 | EF-LEC-07 | Définition des reprises simples (barre sans barre de début, retour à l'anacrouse, fins 1.-3. / 4., option « ignorer » qui saute les premières fins, sélection qui coupe une reprise) | PED-06 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (EF-LEC-07) |
| MOD-22 | EF-ECO-06 | En MIDI USB, pas de compensation de latence par défaut, test au clic optionnel et borné à 30 ms ; au micro, latence mesurée sur un son émis et capté, pas sur le jeu au clic | PED-19 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (EF-ECO-06 CA2, CA3) |
| MOD-23 | EF-ECO-10 | Accord étiré pris en compte ; plage vers 430 Hz si possible | PED-32 | Voir MOD-12 | **Appliquée** dans l'EDB v0.6 (EF-ECO-10 CA1, CA2 (avec MOD-12)) |
| MOD-24 | EF-BIB-05 | Critères : compositeur mort avant 1900 ou air traditionnel, édition ancienne, pas d'arrangement moderne, licence inscrite dans le fichier, fiche d'inventaire | JUR-11, PED-22 | Arbitrage du PO appliqué (voir [08 §3](08-questions-ouvertes.md)) | **Appliquée** dans l'EDB v0.6 (EF-BIB-05 CA1 à CA3 ; 11 §1.2) |
| MOD-25 | EF-VIS-03 CA2 | Cible exprimée en hauteur physique de portée : ≥ 8,9 mm à 70 cm sur tablette (≥ 7,6 mm à 60 cm sur téléphone) | UX-02 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (EF-VIS-03 CA2) |
| MOD-26 | EF-VIS-05 | Critère indépendant du mode : au moins une mesure entière et 4 temps visibles au-delà du curseur ; lecture en avance aux reprises | UX-01, UX-04 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (EF-VIS-05 CA1, CA3) |
| MOD-27 | EF-IMP-02 | Orthographe des notes d'un MIDI (règles de PED, annexe C) ; indication d'une anacrouse à l'aperçu ; séparation des mains par proximité | PED-09, PED-31 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (EF-IMP-02 CA4 à CA6) |

## 3. Exigences non fonctionnelles (EDB 08)

| ID | Exigence | Modification proposée | Origine | Décision | État |
|---|---|---|---|---|---|
| MOD-28 | ENF-ACC-01 | Commandes de séance ≥ 64 dp, dans les coins, hors de la partition ; appuis sur la partition ignorés en séance | UX-10 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (ENF-ACC-01) |
| MOD-29 | ENF-ACC-03, ENF-ACC-04 | Règles de conception (textes en sp, jetons conformes AA, contrôle à 130 %) dès le MVP ; conformité complète en V1 | UX-34 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (ENF-ACC-03, ENF-ACC-04) |
| MOD-30 | ENF-I18N-03 | Lexique bilingue des termes affichés validé par PED dès le MVP (anglais américain) | PED-34, UX-29 | [QO-24 f](09-decisions-a-prendre.md), décidée | **Appliquée** dans l'EDB v0.6 (ENF-I18N-03 ; ENF-I18N-01) |
| MOD-31 | ENF-QA-01 | Ajouter les cibles : validations indues ≤ 1 % [PO], non-validations ≤ 5 % [HYP] en mode attente ; précision des erreurs ≥ 95 % MIDI, ≥ 85 % micro [PO] | QA-04, QA-08 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (ENF-QA-01 ; 06 §9.2) |
| MOD-32 | ENF-PERF-04 | Au micro, délai plus long avec progression si l'analyse ne tient pas 2 s | QA-10 | [QO-43](09-decisions-a-prendre.md), décidée (valeur après mesure en I5) | **Appliquée** dans l'EDB v0.6 (ENF-PERF-04) |
| MOD-33 | ENF-PERF (toutes) | Statistique de décision : 95e centile pour les latences ; médiane et maximum ≤ 1,5 × cible pour les durées | QA-03 | Précision appliquée par le PRD [PO] | **Appliquée** dans l'EDB v0.6 (08 §1 (statistique de décision)) |
| MOD-34 | ENF-CONF-01, ENF-CONF-03 | Sauvegarde automatique d'Android désactivée ; aucune permission réseau dans l'APK | JUR-02, QA-28 | [QO-36](09-decisions-a-prendre.md), décidée | **Appliquée** dans l'EDB v0.6 (ENF-CONF-01, ENF-CONF-03 ; EF-CPT-06) |

## 4. Écrans et ergonomie (EDB 10)

| ID | Endroit | Modification proposée | Origine | Décision | État |
|---|---|---|---|---|---|
| MOD-35 | 10 §5 | Nouveau code des résultats : trois familles de couleur (hauteur vermillon `#C23B00`, rythme bleu `#005BB5`, absence gris `#5F6368`, incertain `#80868B`), symboles placés hors de la tête de note, notes justes sans marque, curseur d'une teinte hors palette | UX-26, UX-27 | Précision appliquée par le PRD (validation PED des symboles) | **Appliquée** dans l'EDB v0.6 (10 §5 ; 06 §3.1) |
| MOD-36 | 10 §3 | Nouveaux écrans E24 à E33 (mise en place sur le pupitre, séparation des mains d'un MIDI, connexion MIDI, latence MIDI, séance suspendue, légende, À propos, suppression des données, démonstrations masquées, export du rapport de test) | UX-32 | Numérotation adoptée par le PO | **Appliquée** dans l'EDB v0.6 (10 §3) |
| MOD-37 | 10 §2 | Navigation du MVP sans barre de navigation : bibliothèque comme accueil, réglages par une icône ; barre de navigation en V1 avec « Progression » ; un appui sur un morceau ouvre la partition ; carte « Reprendre » | UX-30, UX-31 | Arbitrage du PO appliqué | **Appliquée** dans l'EDB v0.6 (10 §2) |
| MOD-38 | 10 §4 (E12, E13, E14) | Maquettes alignées sur le MVP (affichage progressif de E12, E13 sans clavier ni « ? », variantes de E14) | UX-19, UX-21, UX-23 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (10 §3 et §4 (E12, E13, E14)) |
| MOD-39 | 10 §6 | Ton du MVP : tournures impersonnelles, vouvoiement si nécessaire, règles de rédaction et formulations des erreurs | PED annexe D, UX §3.3 | [QO-24 g](09-decisions-a-prendre.md), décidée | **Appliquée** dans l'EDB v0.6 (10 §6 ; 10 §1) |
| MOD-40 | 10 §1 | Écran allumé tant qu'une partition est affichée au premier plan ; plein écran en lecture et en pratique | UX-05, UX-07 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (10 §1) |

## 5. Juridique (EDB 11) et questions (EDB 12)

| ID | Endroit | Modification proposée | Origine | Décision | État |
|---|---|---|---|---|---|
| MOD-41 | 11 §3 | Critère des contenus : licence libre sans NC ni ND (et non « compatible GPL ») ; stratégie de licence par catégorie (revue JUR §3.2) ; REUSE et nomenclature SPDX | JUR-13, JUR-15 | Précision appliquée par le PRD | **Appliquée** dans l'EDB v0.6 (11 §3) |
| MOD-42 | 11 §1.2 | Règle « mort avant 1900 » pour tout morceau intégré ou publié dans le dépôt | JUR-11 | Arbitrage du PO appliqué | **Appliquée** dans l'EDB v0.6 (11 §1.2) |
| MOD-43 | 11 §2 | Rapport de test : contenu anonymisé, notice, durée de conservation (fin de bêta + 6 mois), testeurs mineurs | JUR-07 | [QO-28](09-decisions-a-prendre.md), décidée | **Appliquée** dans l'EDB v0.6 (11 §2.4 (nouvelle section) ; ENF-MAINT-04) |
| MOD-44 | 11 §4 et 12 QO-20 | Vérification des développeurs Android (2027) ; option « distribution limitée » ; nom technique et clé pérennes | JUR-12, JUR-19 | [QO-20, QO-35](09-decisions-a-prendre.md), décidées | **Appliquée** dans l'EDB v0.6 (11 §4 ; 07 §14.2 ; 12 §0 et §1) |
| MOD-45 | 12 §1 | Ajouter les questions QO-34 à QO-43 | — | Décisions du 2026-11-07 | **Appliquée** dans l'EDB v0.6 (12 §0 et §1) |
