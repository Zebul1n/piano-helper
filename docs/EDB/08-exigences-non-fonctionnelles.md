# 08 — Exigences non fonctionnelles

Les valeurs cibles marquées [HYP] seront confirmées après les POC.

## 1. Performance (PERF)

**Statistique de décision.** Une cible de **latence** est tenue si le **95e centile** des mesures est inférieur ou égal à la cible ; une cible de **durée** est tenue si la **médiane** est inférieure ou égale à la cible et le **maximum** inférieur ou égal à 1,5 fois la cible [HYP]. Les mesures suivent des protocoles écrits à l'avance (conditions, nombre de mesures, instrumentation), sur l'appareil réel.

| ID | Exigence | Cible | Prio |
|---|---|---|---|
| ENF-PERF-01 | Latence du retour en mode attente, de l'**attaque de la note** (au micro : choc du marteau sur la corde ; en MIDI : émission du message de note) à l'avance du curseur | MIDI ≤ 100 ms ; micro ≤ 150 ms [HYP]. Décision QO-55 : si, au micro, seul le grave dépasse la cible alors que le médium la tient, une cible propre au grave est retenue (valeur fixée au vu des mesures de POC-01), 150 ms ailleurs | M |
| ENF-PERF-02 | Latence du retour en direct (coloration d'une note) | ≤ 200 ms | M |
| ENF-PERF-03 | Synchronisation son / curseur en lecture | Décalage ≤ 30 ms | M |
| ENF-PERF-04 | Affichage du compte-rendu après la fin du jeu | ≤ 2 s pour un passage de 5 min ; « Analyse en cours… » avec progression au-delà de 2 s. Au micro en jeu continu, la cible est confirmée après mesure en I5 (décision QO-43) : si elle ne peut pas être tenue, le porteur choisit entre garder 2 s et accepter jusqu'à 10 s avec progression affichée | M |
| ENF-PERF-05 | Ouverture d'une partition de 10 pages déjà importée | ≤ 1,5 s jusqu'à l'affichage de la première page | M |
| ENF-PERF-06 | Import d'un MusicXML de 10 pages | ≤ 5 s | M |
| ENF-PERF-07 | Reconnaissance d'un PDF de 4 pages | ≤ 60 s [HYP] | S |
| ENF-PERF-08 | Fluidité du défilement et du curseur | 60 images/s sur les appareils de référence | S |
| ENF-PERF-09 | Démarrage à froid de l'application | ≤ 3 s | S |
| ENF-PERF-10 | Démarrage de la lecture après appui | ≤ 150 ms | M |

## 2. Ressources (RES)

| ID | Exigence | Cible | Prio |
|---|---|---|---|
| ENF-RES-01 | Consommation de batterie en séance avec micro et analyse temps réel | ≤ 15 % par heure sur un appareil de référence [HYP] | S |
| ENF-RES-02 | Pas d'échauffement entraînant une baisse de performance sur une séance de 45 min | — | S |
| ENF-RES-03 | Taille de l'application téléchargée | ≤ 150 Mo [HYP] ; contenus lourds (banque de sons haute qualité, modèles) téléchargeables à la demande ; versions diffusées construites pour les seuls processeurs 64 bits (décision QO-47) | S |
| ENF-RES-04 | Mémoire | Pas d'arrêt pour manque de mémoire sur les appareils minimaux supportés avec une partition de 30 pages | M |

## 3. Compatibilité (COMP)

| ID | Exigence | Prio |
|---|---|---|
| ENF-COMP-01 | iOS / iPadOS : hors périmètre initial (Android d'abord). Le noyau métier doit rester portable (voir [07 §14](07-contraintes-et-pistes-techniques.md#14-conséquences-des-décisions-de-cadrage)). | W (now) |
| ENF-COMP-02 | Android : version minimale **Android 13 (API 33)** [QO-09] ; fonctionnalités des versions plus récentes activées à l'exécution quand elles sont disponibles. | M |
| ENF-COMP-03 | Téléphones (≥ 5,5 pouces) et tablettes (7 à 13 pouces), portrait et paysage | M |
| ENF-COMP-04 | Appareils de référence sur lesquels les cibles de performance sont mesurées : le téléphone Android 14 du porteur dans un premier temps ; au moins 3 appareils (dont 1 d'entrée de gamme), via des testeurs bénévoles, avant la V1 [QO-21] | M |
| ENF-COMP-05 | Compatibilité MIDI testée sur au moins 6 modèles de pianos numériques de marques différentes | S |
| ENF-COMP-06 | Fonctionnement en écran partagé / multifenêtre sur tablette sans perte de captation, reporté après le MVP. Au MVP, la séance est suspendue quand l'application perd la position d'activité principale | C |

## 4. Disponibilité et fonctionnement hors ligne (OFF)

| ID | Exigence | Prio |
|---|---|---|
| ENF-OFF-01 | Bibliothèque, visualisation, lecture, pratique, analyse et historique fonctionnent sans connexion. | M |
| ENF-OFF-02 | Les fonctions qui nécessitent une connexion (OMR serveur, sauvegarde, synchronisation) l'indiquent clairement et mettent les demandes en file d'attente. | S |
| ENF-OFF-03 | Disponibilité du backend : 99,5 % mensuel [HYP]. Une indisponibilité ne bloque jamais la pratique. | S |

## 5. Fiabilité et intégrité des données (FIA)

| ID | Exigence | Prio |
|---|---|---|
| ENF-FIA-01 | Aucune perte de données (bibliothèque, historique) en cas d'arrêt brutal de l'application ou de l'appareil. | M |
| ENF-FIA-02 | Le fichier source importé est conservé tel quel, en plus du modèle interne, pour permettre une réimportation après évolution du moteur. | M |
| ENF-FIA-03 | Les données sont versionnées (schéma) et migrées automatiquement lors des mises à jour. | M |
| ENF-FIA-04 | Une interruption (appel entrant, notification, mise en arrière-plan) pendant une séance suspend proprement la séance, sans plantage ni perte. | M |
| ENF-FIA-05 | Taux de sessions sans plantage ≥ 99,5 %. | S |

## 6. Qualité de l'analyse (QA)

Reprend les indicateurs de [01](01-contexte-et-objectifs.md#51-qualité-de-lanalyse) et le protocole de [06](06-analyse-du-jeu-et-erreurs.md#9-protocole-dévaluation-de-la-qualité).

| ID | Exigence | Prio |
|---|---|---|
| ENF-QA-01 | Taux de fausses erreurs ≤ 1 % en MIDI, ≤ 5 % au micro [HYP] sur le corpus de test. En mode attente : validations indues ≤ 1 % [HYP], non-validations ≤ 5 % [HYP]. Précision des erreurs signalées ≥ 95 % en MIDI, ≥ 85 % au micro [HYP] (métriques de [06 §9.2](06-analyse-du-jeu-et-erreurs.md#92-métriques)). | M |
| ENF-QA-02 | Évaluation automatique de la chaîne d'analyse sur le corpus à chaque évolution ; non-régression du taux de fausses erreurs. | M |
| ENF-QA-03 | Performance de la captation micro mesurée par catégorie d'appareil ; si un appareil ne permet pas d'atteindre la qualité minimale, l'utilisateur en est informé. | S |

## 7. Ergonomie et accessibilité (ACC)

| ID | Exigence | Prio |
|---|---|---|
| ENF-ACC-01 | Pendant le jeu, aucune interaction n'est requise (mains sur le clavier). Les actions essentielles (pause, recommencer) sont accessibles par de grandes cibles tactiles et, si possible, par une action au clavier MIDI (ex. note très grave) ou une pédale Bluetooth de tourne de page. Les commandes de séance mesurent au moins 64 × 64 dp, sont placées dans les coins, hors de la partition ; les appuis sur la partition sont ignorés pendant une séance. Au MVP : arrêt et reprise en jouant (EF-PRA-08) et grandes cibles ; commandes au clavier MIDI ou à la pédale en V1. | S |
| ENF-ACC-02 | Le résultat des notes n'est jamais communiqué uniquement par la couleur (formes, symboles, motifs). Palette lisible pour les daltonismes les plus courants. | M |
| ENF-ACC-03 | Contraste conforme aux recommandations WCAG 2.2 niveau AA pour les textes et commandes. Dès le MVP, règles de conception : couleurs définies en jetons conformes AA ; conformité complète vérifiée en V1. | S |
| ENF-ACC-04 | Prise en charge des tailles de texte système pour l'interface (hors partition). Dès le MVP, textes dimensionnés en sp et écrans principaux contrôlés à 130 % ; prise en charge complète en V1. | S |
| ENF-ACC-05 | Lecteurs d'écran (TalkBack ; VoiceOver lors du portage iOS) pour la navigation dans l'application, la bibliothèque et les comptes-rendus (la partition elle-même est hors périmètre). | C |
| ENF-ACC-06 | Interface utilisable par un enfant de 8 ans (vocabulaire simple, parcours courts) en style de retour « ludique ». | S |
| ENF-ACC-07 | Partition lisible à 70 cm sur tablette avec le réglage par défaut. | M |

## 8. Internationalisation (I18N)

| ID | Exigence | Prio |
|---|---|---|
| ENF-I18N-01 | Langues au lancement : français et anglais (anglais américain), dès le MVP (décisions QO-16 et QO-24 f). Textes externalisés pour faciliter l'ajout de langues. | M |
| ENF-I18N-02 | Notation des notes latine (do ré mi) ou anglo-saxonne (C D E), indépendante de la langue. Prévoir la notation allemande (H pour si) en option. | M / C |
| ENF-I18N-03 | Terminologie musicale validée par un musicien dans chaque langue. Dès le MVP, un lexique bilingue des termes affichés (types d'erreurs, niveaux, notions de solfège), en anglais américain (measure, quarter note), est validé par `pedagogue-piano` ; la relecture par un musicien anglophone arrive en V1. | S |

## 9. Sécurité (SEC)

| ID | Exigence | Prio |
|---|---|---|
| ENF-SEC-01 | Communications avec le backend chiffrées (TLS 1.2 minimum). Sans objet tant qu'aucun serveur n'existe (MVP). | M (dès qu'un serveur existe) |
| ENF-SEC-02 | Authentification déléguée à des fournisseurs reconnus (Google ; Apple lors du portage iOS) et/ou e-mail avec lien magique ; pas de stockage de mot de passe en clair. | S |
| ENF-SEC-03 | Données stockées sur le serveur chiffrées au repos. | S |
| ENF-SEC-04 | Fichiers importés traités comme non fiables : validation, limites de taille, protection contre les fichiers malformés (archives `.mxl` compressées, XML : entités externes désactivées). | M |
| ENF-SEC-05 | Les partitions et enregistrements envoyés au serveur pour OMR sont supprimés après traitement (délai à fixer, ex. 24 h). | S |
| ENF-SEC-06 | Aucun accès au micro en dehors des séances de pratique et du calibrage. | M |

## 10. Confidentialité (CONF)

Voir le détail dans [11 — Juridique et conformité](11-juridique-et-conformite.md).

| ID | Exigence | Prio |
|---|---|---|
| ENF-CONF-01 | Traitement audio local par défaut ; aucun son envoyé au serveur sans action explicite de l'utilisateur. La sauvegarde automatique d'Android et le transfert entre appareils sont désactivés : aucune donnée de l'application ne part dans le compte Google de l'utilisateur (décision QO-36) ; l'utilisateur est invité à exporter ses partitions avant de changer de téléphone. | M |
| ENF-CONF-02 | Télémétrie et rapports de plantage soumis au consentement, sans contenu audio ni partition. | M |
| ENF-CONF-03 | Collecte minimale ; pas de publicité ciblée. Tant qu'aucun serveur n'existe (MVP), l'application ne déclare pas la permission d'accès à Internet. | M |

## 11. Maintenabilité et exploitation (MAINT)

| ID | Exigence | Prio |
|---|---|---|
| ENF-MAINT-01 | Le noyau d'analyse est testable indépendamment de l'interface, avec une batterie de tests automatisés sur le corpus. | M |
| ENF-MAINT-02 | Paramètres de l'analyse (tolérances, seuils de confiance) regroupés et modifiables sans toucher au code. Au MVP, paramètres embarqués et versionnés : une nouvelle version de l'application suffit à les changer ; configuration distante seulement quand un serveur existera. | S |
| ENF-MAINT-03 | Remontée des plantages et des métriques techniques anonymes (latence mesurée, taux d'écarts incertains) pour piloter la qualité. | S |
| ENF-MAINT-04 | Journal de diagnostic exportable par l'utilisateur pour le support (sans audio par défaut). Forme réduite au MVP, pour la bêta : rapport de test anonymisé, exporté volontairement par le testeur et envoyé par e-mail (décision QO-28, conditions en [11 §2.4](11-juridique-et-conformite.md#24-rapport-de-test-de-la-bêta)). Journal complet en V2. | C (MVP : forme réduite) |
