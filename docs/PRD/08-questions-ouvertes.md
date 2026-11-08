# 08 — Questions ouvertes : suivi et arbitrages

Depuis la version 0.2, les décisions qui reviennent au porteur sont rassemblées, dédoublonnées et classées par urgence dans le **[fichier 09 — Décisions du porteur](09-decisions-a-prendre.md)**. Le porteur a tranché QO-10 à QO-43 le 2026-11-07 (PRD v0.3), puis QO-44 et les questions du DAT, sauf QO-54, le 2026-11-08 (PRD v0.4). Ce fichier garde le suivi des questions ouvertes par le PRD v0.1 et consigne les **arbitrages du Product Owner** entre relecteurs, qui ne relèvent pas du porteur.

## 1. Suivi des questions QO-23 à QO-33

| ID | Question (v0.1) | État en v0.2 | État en v0.3 | Où |
|---|---|---|---|---|
| QO-23 | Exigences S à avancer au MVP à cause du micro | Partiellement résolue : EF-PRA-08 entrée au MVP par l'EDB v0.5 ; reste EF-ECO-07, EF-ECO-08, EF-ECO-10 | **Décidée** (A : les trois, formes minimales) | [09, urgence 3](09-decisions-a-prendre.md) |
| QO-24 | Écarts de priorité M / S-C (points a à g) | Ouverte ; positions provisoires précisées par les relecteurs ; absorbe QO-12 (point g) et QO-16 (point f) de l'EDB | **Décidée** (positions recommandées) | [09, urgence 3](09-decisions-a-prendre.md) |
| QO-25 | Délai de silence et seuil d'arrêt | Ouverte ; proposition chiffrée de PED reprise | **Décidée** (recommandation) | [09, urgence 3](09-decisions-a-prendre.md) |
| QO-26 | Boucle simple en pratique | Ouverte | **Décidée** (oui ; EF-PRA-11) | [09, urgence 5](09-decisions-a-prendre.md) |
| QO-27 | Mode d'affichage du MVP | Ouverte ; recommandation modifiée (bande horizontale, à confirmer par un test) | **Décidée** (test du porteur seul ; bande horizontale en attendant ; reconfirmée en bêta) | [09, urgence 2](09-decisions-a-prendre.md) |
| QO-28 | Retours de bêta sans serveur | Ouverte ; conditions juridiques ajoutées | **Décidée** (A) | [09, urgence 5](09-decisions-a-prendre.md) |
| QO-29 | Statut du jeu continu au micro | Ouverte | **Décidée** (A : « expérimental ») | [09, urgence 4](09-decisions-a-prendre.md) |
| QO-30 | Démarrer I1 avant J1 | Ouverte | **Décidée** (A) | [09, urgence 2](09-decisions-a-prendre.md) |
| QO-31 | Morceaux de démonstration | Ouverte ; recommandation inversée (gravure maison d'abord), règle juridique appliquée | **Décidée** (C avec A en priorité, CC0, saisie par un agent) | [09, urgence 2](09-decisions-a-prendre.md) |
| QO-32 | Tolérance rythmique au micro | Ouverte ; règle chiffrée de PED reprise | **Décidée** (recommandation) | [09, urgence 4](09-decisions-a-prendre.md) |
| QO-33 | Critères J3 et mise à jour de l'EDB | **Close** : EDB v0.5, 04 §4 | Close | — |

Questions ouvertes en v0.2 : QO-34 à QO-43, toutes **décidées** le 2026-11-07 ([fichier 09](09-decisions-a-prendre.md)). Question nouvelle en v0.3 : **QO-44** (tests qui demandent plusieurs personnes avant la bêta), **décidée le 2026-11-08** (option A, [09 §2.3](09-decisions-a-prendre.md)).

### Suivi des questions QO-44 à QO-55

| ID | Question | Origine | État en v0.4 | Où |
|---|---|---|---|---|
| QO-44 | Tests qui demandent plusieurs personnes avant la bêta | PRD v0.3 | **Décidée** le 2026-11-08 (A) | [09 §2.3](09-decisions-a-prendre.md) |
| QO-45 | Hébergement du code et des vérifications | DAT v0.1 | **Décidée** le 2026-11-08 (GitHub) | [DAT 14](../DAT/14-decisions-a-prendre.md) |
| QO-46 | Identifiant technique définitif | DAT v0.1 | **Décidée** le 2026-11-08 (`io.github.zebul1n.pianohelper`, compte GitHub Zebul1n) | [DAT 14](../DAT/14-decisions-a-prendre.md) |
| QO-47 | Processeurs des versions diffusées | DAT v0.1 | **Décidée** le 2026-11-08 (64 bits) | [DAT 14](../DAT/14-decisions-a-prendre.md) |
| QO-48 | Outils du poste du porteur | DAT v0.1 | **Décidée** le 2026-11-08 (WSL 2, Android Studio) | [DAT 14](../DAT/14-decisions-a-prendre.md) |
| QO-49 | Corpus de piano public | DAT v0.1 | **Décidée** le 2026-11-08 (dépôt séparé, CC0) | [DAT 14](../DAT/14-decisions-a-prendre.md) |
| QO-50 | Moteur d'IA tout fait de Microsoft | DAT v0.1 | **Décidée** le 2026-11-08 (exclu des versions diffusées) | [DAT 14](../DAT/14-decisions-a-prendre.md) |
| QO-51 | Permission de diffusion sur les boutiques d'applications | DAT v0.2 | **Décidée** le 2026-11-08 (B ; à reposer avant la première contribution extérieure) | [DAT 14](../DAT/14-decisions-a-prendre.md) |
| QO-52 | Compte-rendu du mode attente | DAT v0.2 | **Décidée** le 2026-11-08 (A) | [DAT 14](../DAT/14-decisions-a-prendre.md) |
| QO-53 | Allègement des polices musicales | DAT v0.2 | **Décidée** le 2026-11-08 (A) | [DAT 14](../DAT/14-decisions-a-prendre.md) |
| QO-54 | Diffusion de la V1 hors de Google Play | DAT v0.2 | **Ouverte**, avec QO-20, avant la V1 | [DAT 14](../DAT/14-decisions-a-prendre.md) |
| QO-55 | Cible de latence propre au grave | DAT v0.2 | **Décidée** le 2026-11-08 (A, si la mesure l'exige) | [DAT 14](../DAT/14-decisions-a-prendre.md) |

## 2. Décisions appliquées dans le PRD

Depuis la v0.3, les décisions du porteur remplacent les positions provisoires : les 6 stories « Proposées » sont fermes, et les critères citent la décision (« décision QO-nn »). Les précisions des relecteurs sur l'EDB (fichier [10](10-modifications-edb-proposees.md)) sont toutes appliquées dans l'EDB v0.6. Depuis la v0.4, QO-44 est décidée (US-VIS-03 CA3, US-CPT-06 CA6, registre de [05 §5](05-plan-de-release.md)) ; les décisions QO-45 à QO-55 et les modifications MDAT-20 à MDAT-42 du DAT ([fichier 15](../DAT/15-modifications-edb-prd-proposees.md)) sont appliquées dans les stories.

## 3. Arbitrages entre relecteurs

Ces arbitrages relèvent du Product Owner : ils ne changent ni le périmètre, ni une décision structurante. Le porteur peut les revoir à tout moment.

| # | Sujet | Positions | Décision du PO | Motif |
|---|---|---|---|---|
| A1 | Morceau de démonstration n°9 | PED propose la *Gymnopédie n°1* de Satie (mort en 1925) ; JUR exige un compositeur mort avant 1900 pour tout contenu intégré | **Règle JUR appliquée.** Remplaçant : Chopin, Prélude op. 28 n°4 en mi mineur (extrait) : même niveau intermédiaire, tempo lent, pédale continue, très demandé par les adultes, cas difficile au micro (accords répétés sous la pédale). Alternative : Schumann, *Rêverie* op. 15 n°7. À confirmer par PED | La règle « avant 1900 » évite tout calcul de prorogation ; le n°9 visait la pédale et l'attrait pour les adultes, que le Prélude op. 28 n°4 apporte |
| A2 | Satie et Joplin dans le corpus de test (annexe F de PED) et dans le test d'écoute de la banque de sons | PED les cite ; JUR : corpus publié en domaine public sans risque | Même règle pour tout ce qui est **publié dans le dépôt ou l'APK** ; Satie et Joplin restent dans le **corpus privé** (hors dépôt), après vérification par JUR ; test d'écoute avec le Prélude op. 28 n°4 | Une règle unique, simple à contrôler |
| A3 | Écran d'explication du micro | JUR (§4.1) et UX (§3.4) proposent deux textes | **Fusion** (US-ECO-03 §8.1) : structure et annonce du mot « enregistrer » d'Android (UX) ; « gardé en mémoire le temps de la séance, puis effacé » et mention des voix captées (JUR), plus exact que « oublié dès qu'il a été analysé » tant que l'ADR sur l'audio n'est pas écrit | Exactitude juridique et transparence |
| A4 | Après un refus du micro | UX : boutons [Autoriser le micro] [Continuer sans micro] ; JUR : ne pas insister, ne proposer les réglages que si l'utilisateur redemande | **Règle JUR** : un seul bouton « Continuer sans micro » ; le micro reste choisissable dans les réglages, ce qui couvre le besoin de UX | Recommandation Android et CNIL |
| A5 | Son d'erreur au micro | PED : toujours désactivé au micro ; UX : soumis à la règle des écouteurs | **Indisponible au micro** au MVP, désactivé par défaut partout | Il se déclenche au moment même où l'élève rejoue : risque élevé de fausses détections |
| A6 | Reprise après une pause en jeu continu | PED : reprendre là où l'on rejoue (suivi) ; UX : jouer une note relance le décompte ; QA : contradiction avec le micro fermé en pause | **Jouer une note relance le décompte et la reprise au début de la mesure précédente** (PED-27) ; micro ouvert 30 s au plus pendant la pause, voyant allumé | Le suivi du joueur est une fonction V1 (mode suivi) ; cette solution tient les mains sur le clavier sans la devancer |
| A7 | Cible de lisibilité | UX : portée ≥ 7,6 mm à 60 cm ; QA : interligne ≥ 2,0 mm « à valider par UX » | **7,6 mm** (interligne 1,9 mm) | Valeur argumentée par l'angle de vision ; QA laissait la valeur à UX |
| A8 | Lecture en avance | UX : au moins une mesure entière et 4 temps ; PED : deux mesures | **Critère UX** dans US-VIS-05 ; le nombre de mesures visibles est mesuré au test de QO-27 et la cible de PED sert d'objectif | Deux mesures ne sont pas garanties en paysage sur un téléphone sans réduire la portée sous le seuil de lisibilité |
| A9 | Fenêtre d'un accord arpégé | PED : max(fenêtre ; 60 ms × (n − 1)), plafonnée ; QA : fenêtre × n | **Formule PED** | Expertise musicale ; plafond qui évite des fenêtres absurdes |
| A10 | Latence MIDI | EDB : mesure de latence MIDI ; PED : pas de compensation par défaut ; UX : pas de test au premier lancement ; QA : valeurs du test | **Test conservé en option avancée** (EF-ECO-06 couverte), absent du premier lancement, compensation nulle par défaut et bornée à 30 ms, valeurs de QA | Concilie l'exigence M et le biais du jeu au clic |
| A11 | Panel du premier lancement | UX : profils variés ; QA : 3 non-initiés, chacun sous 5 min, observateur muet | **Fusion** des deux critères (US-CPT-06 CA6) | Complémentaires |
| A12 | Corpus non commercial et contrôle bloquant | JUR : données NC hors du contrôle bloquant ; QA : corpus public et corpus privé ; PED : données expressives hors taux de fausses erreurs de rythme | **Bloquant** = corpus public (intégration continue) + corpus privé maison (rejoué par le porteur) ; **données NC et expressives** = mesures complémentaires | Les trois positions sont compatibles |
| A13 | Séance sans aucune note jouée | UX demande au PO | **Non enregistrée** dans l'historique ; message seulement | Une séance vide n'apporte rien et fausserait la rétention |
| A14 | Rotation pendant une séance | QA (CL-39) demande une règle | **Orientation verrouillée** pendant une séance **sur téléphone** ; sur tablette, la séance survit à la rotation (MDAT-30 du DAT, Android 16) | Évite toute perte d'événement pendant la recréation de l'écran |
| A15 | Deux mains jouées alors qu'une seule est travaillée | QA (CL-23) demande une règle | Les notes jouées qui correspondent à la main non travaillée **ne sont pas « en trop »** (à valider par PED) | Sinon fausses erreurs garanties, surtout au micro |
| A16 | Navigation du MVP | UX : pas de barre de navigation | **Adopté** : bibliothèque comme accueil, réglages par une icône ; un appui sur un morceau ouvre la partition ; carte « Reprendre » | Deux destinations seulement au MVP |
| A17 | Numérotation des nouveaux écrans | UX propose E24 à E33 | **Adoptée** | — |
| A18 | Langue anglaise | PED : anglais américain | **Adopté** ; l'anglais au MVP est confirmé par la décision [QO-24 f](09-decisions-a-prendre.md) | Choix de rédaction sans impact sur le périmètre |
| A19 | Ton | PED : impersonnel ; UX : impersonnel et vouvoiement si nécessaire ; JUR : textes au vouvoiement | **Impersonnel par défaut, vouvoiement si nécessaire**, confirmé par la décision [QO-24 g](09-decisions-a-prendre.md) | Positions convergentes |
| A20 | US-CNF-02 trop tardive | JUR : mentions légales dès I0 | **Découpage** : US-CNF-02 (mentions et licences, I0) et nouvelle US-CNF-06 (notice de confidentialité et « À propos » complet, I6) | Obligation de licence à chaque redistribution |

**Arbitrage à faire** à l'entrée de US-IMP-03 (I1), avec l'avis de `designer-ux` : déclarer ou non le type `application/octet-stream`, pour que les `.mxl` reçus sans type précis puissent être ouverts avec Piano Helper, au prix d'une application proposée pour tout fichier binaire (MDAT-34 du [DAT](../DAT/15-modifications-edb-prd-proposees.md)). Il sera consigné ici sous le numéro A21.
