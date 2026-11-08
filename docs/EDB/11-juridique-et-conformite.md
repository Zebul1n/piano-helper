# 11 — Juridique et conformité

> Ce document identifie les sujets à traiter. Il ne constitue pas un avis juridique : chaque point marqué ⚖ doit être validé par un juriste avant la publication.

## 1. Droits d'auteur sur les partitions

### 1.1 Principe retenu

L'application est un **outil** : l'utilisateur importe des partitions qu'il détient. L'application ne vend pas et ne redistribue pas de partitions (hors morceaux de démonstration).

### 1.2 Points d'attention

| Sujet | Analyse | Action |
|---|---|---|
| Partitions importées par l'utilisateur | Usage privé par l'utilisateur sur son appareil. Les CGU rappellent qu'il est responsable des droits sur les fichiers qu'il importe. | CGU ⚖ |
| Numérisation d'une partition papier (photo, scan) | Le régime de la reproduction des partitions est particulier en France (la reprographie de partitions est encadrée, notamment par la SEAM). La fonctionnalité de photo (EF-IMP-04) doit être examinée. | Avis juridique ⚖ avant V2 |
| Traitement OMR côté serveur | La partition transite par nos serveurs : conservation minimale, aucune réutilisation, suppression après traitement (ENF-SEC-05). | CGU, politique de confidentialité ⚖ |
| Partage professeur → élève (V2) | Il s'agit d'une communication de l'œuvre à un tiers. Limiter à un partage privé et rappeler la responsabilité du professeur. | CGU ⚖ |
| Morceaux de démonstration | Œuvres du domaine public (en France, en principe 70 ans après la mort de l'auteur, avec des prorogations possibles). Attention : une **édition** ou une **gravure** récente peut être protégée indépendamment de l'œuvre. | **Règle : tout morceau intégré à l'application ou publié dans le dépôt est un air traditionnel ou l'œuvre d'un compositeur mort avant 1900**, saisie d'après une édition ancienne, sans arrangement ni apport d'éditeur moderne (ce critère couvre toutes les prorogations françaises et les États-Unis). Source (décision QO-31) : gravure maison publiée sous CC0 1.0, saisie par un agent, relue note par note par `pedagogue-piano` et validée à l'écoute par le porteur ; en complément, fichiers sous licence libre vérifiée (CC BY ou CC BY-SA, attribution). Voir EF-BIB-05. |
| Catalogue de partitions (éventuel, [QO-05]) | Nécessiterait des accords de licence avec des éditeurs et des sociétés de gestion collective. | Hors périmètre actuel |
| Enregistrements de l'utilisateur | L'utilisateur interprète une œuvre ; l'enregistrement reste privé sur son appareil. Tout partage public serait une autre question. | Pas de partage public prévu |

## 2. Protection des données personnelles (RGPD)

### 2.1 Données traitées

| Donnée | Caractère personnel | Lieu | Base légale envisagée ⚖ |
|---|---|---|---|
| Profil (nom affiché, niveau, préférences) | Oui | Local ; serveur si compte | Exécution du contrat |
| Compte (e-mail, identifiant Apple / Google) | Oui | Serveur | Exécution du contrat |
| Historique de pratique (séances, scores) | Oui | Local ; serveur si sauvegarde | Exécution du contrat |
| Flux audio du micro | Oui (peut contenir des voix, des conversations) | **Traitement local, en mémoire, non conservé par défaut** | Exécution du contrat (fonction demandée) |
| Enregistrements audio des séances | Oui | Local uniquement par défaut | Consentement (option) |
| Partitions envoyées pour OMR | Pas en soi ; peut contenir des annotations manuscrites nominatives | Serveur, temporaire | Exécution du contrat |
| Télémétrie, rapports de plantage | Oui (pseudonymes) | Serveur | Consentement |
| Données d'élèves partagées avec un professeur (V2) | Oui | Serveur | Consentement |
| Enregistrements donnés pour améliorer le modèle (éventuel, [QO-15]) | Oui | Serveur | Consentement explicite, révocable |

### 2.2 Exigences

| ID | Exigence |
|---|---|
| RGPD-01 | Politique de confidentialité claire, accessible depuis l'application et les stores. |
| RGPD-02 | Minimisation : aucune donnée non nécessaire ; pas de publicité ; pas de revente. |
| RGPD-03 | Droits des personnes : accès, rectification, export (EF-PRO-09), effacement (EF-CPT-06) depuis l'application. |
| RGPD-04 | Hébergement des données dans l'Union européenne, ou garanties adéquates en cas de transfert. |
| RGPD-05 | Registre des traitements ; contrats de sous-traitance avec les prestataires (hébergement, analytique, authentification). |
| RGPD-06 | Analyse d'impact (AIPD) à envisager si traitement de données de mineurs à grande échelle ou d'enregistrements audio ⚖. |
| RGPD-07 | Durées de conservation définies pour chaque donnée serveur (ex. compte inactif depuis 3 ans). |
| RGPD-08 | Déclarations de confidentialité des stores (fiche « App Privacy » d'Apple, section « Sécurité des données » de Google Play) cohérentes avec la réalité. |

### 2.3 Mineurs

Une partie importante de la cible est mineure (persona Lucas).

| Sujet | Point d'attention ⚖ |
|---|---|
| Consentement | En France, un mineur de moins de 15 ans ne peut consentir seul à certains traitements fondés sur le consentement : accord du titulaire de l'autorité parentale nécessaire. |
| Création de compte | Parcours parental pour les moins de 15 ans, ou compte géré par le parent (profil enfant dans le compte parent). |
| Stores | Si l'application cible les enfants (catégorie « Enfants » chez Apple, programme « Familles » chez Google), règles spécifiques : pas d'analytique tierce non conforme, pas de publicité, contrôle parental sur les achats et les liens externes. Décider si l'on s'inscrit dans ces programmes ou non [QO-13]. |
| Autres juridictions | En cas de distribution aux États-Unis : réglementation COPPA (moins de 13 ans). |
| Professeur (V2) | Le partage des données d'un élève mineur avec un professeur exige l'accord du responsable légal (RG-05). |

### 2.4 Rapport de test de la bêta

Pendant la bêta, les retours des testeurs sont recueillis sans serveur (décision QO-28) : signalement « Ce n'était pas une erreur » dans l'application, export volontaire d'un **rapport de test** envoyé par e-mail, questionnaire court en complément.

| Sujet | Règle |
|---|---|
| Contenu | Version de l'application, modèle d'appareil et version d'Android, calibrages, résumé des séances (jour sans heure, mode, source, scores), signalements. Ni son, ni partition, ni titre de morceau importé, ni nom, ni identifiant de l'appareil. Le rapport est lisible par le testeur avant l'envoi. |
| Notice | Affichée sur l'écran d'export et en tête du rapport : contenu, destinataire (Ronan Launay, à l'adresse rlaunay@free.fr, voir §5), usage, durée de conservation, droits. Envoi par e-mail recommandé plutôt que dans un ticket public. |
| Conservation | Jusqu'à la fin de la bêta plus 6 mois ; ensuite, seules des statistiques anonymes sont gardées. |
| Mineurs | Testeurs majeurs ; pour un testeur de moins de 15 ans, le rapport est envoyé par un parent. |
| Mesure de l'usage | Les testeurs qui n'envoient rien sont comptés comme inactifs. |

## 3. Licences logicielles et contenus

| Sujet | Risque | Action |
|---|---|---|
| Bibliothèques LGPL (ex. Verovio, FluidSynth) | La LGPL permet l'usage dans une application propriétaire sous conditions (permettre de remplacer la bibliothèque). Sur iOS, la liaison statique et la distribution via l'App Store compliquent le respect de ces conditions. | Analyse juridique ⚖ ; préférer une liaison dynamique ou des alternatives sous licence permissive. Verovio est sous **LGPL-3.0-only** ; l'œuvre combinée (application et Verovio) est diffusée sous GPL-3.0 |
| Bibliothèques AGPL (ex. Audiveris) côté serveur | L'AGPL impose de fournir le code source du service aux utilisateurs qui y accèdent par le réseau, en cas de modification. | Isoler le composant, évaluer l'obligation de publication ⚖ ou choisir une alternative |
| Banques de sons de piano | Licences variables (domaine public, CC BY avec attribution, licences commerciales). | Vérifier l'usage commercial et l'obligation d'attribution. Décision QO-10 : banque légère embarquée sous licence libre (exemple vérifié : « Upright Piano KW », CC0, variante *small* : 2,9 à 6,0 Mio selon le format ; SF2 complet : 27 Mio) ; banque de meilleure qualité téléchargeable plus tard (exemple : « Salamander Grand Piano », CC BY) |
| Modèles d'IA préentraînés | Licence du code ≠ licence des poids ≠ licence des données d'entraînement. | Vérifier chacune ; inventaire en trois volets (code, poids, données). Décision QO-41 : un modèle dont les poids sont publiés sous licence libre mais entraîné en partie sur des données non commerciales (cas probable : Basic Pitch, MAESTRO) est accepté au MVP, avec un risque juridique résiduel documenté ; avis d'avocat avant la V1 si une version payante est envisagée. Sont exclus les modèles dont la licence elle-même interdit l'usage commercial ou restreint l'usage (NC, « recherche seulement », RAIL). Un modèle entraîné par le projet n'utilise que des données libres |
| Jeux de données audio / MIDI (évaluation, entraînement) | Certains jeux de données publics sont sous licence non commerciale (ex. CC BY-NC-SA). | Décision QO-37 : utilisables pour des **mesures complémentaires** seulement, jamais copiés dans le dépôt ni dans l'application, téléchargés par un script, jamais utilisés pour entraîner un modèle, hors du contrôle bloquant ([06 §9.1](06-analyse-du-jeu-et-erreurs.md#91-corpus-de-test)) ; à réexaminer si une version payante est décidée |
| Polices musicales (SMuFL, ex. Bravura) | Licences généralement libres (SIL OFL), à vérifier. Les polices de Verovio (Leipzig, Bravura) ont un nom réservé : une version modifiée ne peut pas le garder sans autorisation. | Inventaire ; polices embarquées sans modification. Décision QO-53 : demander à RISM l'autorisation de les alléger seulement si la taille de l'application l'exige (après POC-04) |
| Inventaire | — | Tenir une nomenclature logicielle (SBOM) avec licences, et un écran « Mentions légales / licences » dans l'application. Appliquer la spécification **REUSE** (en-têtes SPDX ou fichier `REUSE.toml`, dossier `LICENSES/`) et produire une nomenclature au format SPDX ou CycloneDX à chaque diffusion |
| Licence du projet lui-même [QO-19] — **décidé : open source, GPL-3.0-or-later** | Si l'application est publiée en open source sous une licence copyleft (ex. GPL-3.0), l'usage de composants LGPL et GPL ne pose plus de difficulté, et la distribution sur F-Droid devient possible. Une licence permissive (Apache-2.0, MIT) interdit d'intégrer du code GPL. Un code fermé impose les contraintes ci-dessus. Sur Android, les bibliothèques natives sont des bibliothèques partagées, ce qui facilite le respect de la LGPL. | Décider avant le premier composant tiers intégré ⚖. Chaque version diffusée affiche la mention « © 2026 Ronan Launay et les contributeurs » (décision QO-34, voir §5) et donne accès à ses sources. Décision QO-51 : aucune permission supplémentaire (GPL-3.0 §7) pour la diffusion sur les boutiques d'applications n'est ajoutée pour l'instant ; la question sera reposée avant d'accepter la première contribution extérieure |
| Licence de la documentation | La documentation du projet (EDB, PRD, guides) est publiée avec le dépôt. | Décision QO-38 : **CC BY-SA 4.0** (compatible dans un seul sens avec la GPLv3, ce qui permet de reprendre des extraits dans le code ou l'aide en ligne) ; une ligne dans le README du dépôt |

**Critère des contenus.** Le critère pour le **code** est la compatibilité avec la GPL-3.0-or-later. Pour les **contenus** (sons, partitions, polices, images, modèles, données embarquées), le critère est une licence libre **sans clause NC (non commerciale) ni ND (pas de modification)** : tout fichier qui n'est pas du code et ne respecte pas ce critère ne va ni dans le dépôt ni dans l'application.

| Catégorie | Licence de ce que le projet produit | Licences tierces acceptées | Exclues |
|---|---|---|---|
| Code de l'application | GPL-3.0-or-later | MIT, BSD, Apache-2.0, LGPL-2.1-or-later, LGPL-3.0-only (Verovio) ou LGPL-3.0-or-later, GPL-3.0, AGPL-3.0 (à isoler), MPL-2.0 sans la mention « Incompatible With Secondary Licenses » | GPL-2.0-only, SDK propriétaires, licences à restriction d'usage |
| Partitions de démonstration | CC0 1.0 | Domaine public, CC0, CC BY 4.0, CC BY-SA 4.0 | NC, ND, « usage personnel », arrangements protégés |
| Sons (banque embarquée, métronome) | CC0 pour les sons créés par le projet | CC0, CC BY 3.0 ou 4.0, CC BY-SA 4.0 | NC, ND, banques « royalty-free » propriétaires |
| Polices | — | SIL OFL-1.1 | Polices commerciales |
| Modèles d'IA embarqués | Apache-2.0 pour un modèle entraîné par le projet, sur des données libres uniquement | Poids sous Apache-2.0, MIT, BSD ou CC BY ; données d'entraînement documentées | Poids NC, « recherche seulement », RAIL |
| Corpus d'évaluation public | CC0 (enregistrements maison, annotations ; dépôt séparé, décision QO-49) | CC0, CC BY, CC BY-SA | — |
| Corpus d'évaluation non commercial | — | CC BY-NC-SA, aux conditions de la décision QO-37 | Toute diffusion dans le dépôt ou l'application |
| Documentation | CC BY-SA 4.0 (décision QO-38) | — | — |

## 4. Distribution et commerce

| Sujet | Point d'attention |
|---|---|
| Achats intégrés et abonnements | Règles des stores (moyens de paiement, commission, essais gratuits, résiliation) ; mentions obligatoires sur le renouvellement automatique. |
| Droit de la consommation | Droit de rétractation pour les contenus numériques, information précontractuelle, conditions générales de vente ⚖. |
| Marque | Vérifier la disponibilité du nom définitif (INPI, EUIPO, stores, nom de domaine) [QO-06]. Ne pas utiliser de marques de fabricants de pianos ou de concurrents dans la communication sans précaution. |
| Accessibilité | Vérifier si l'application entre dans le champ des obligations européennes d'accessibilité applicables aux produits et services numériques ⚖. Viser de toute façon les exigences ENF-ACC. |
| Vérification des développeurs Android | Google annonce pour 2027 (Europe comprise) que toute application installée sur un téléphone Android certifié devra être enregistrée par un développeur vérifié, même hors Google Play. Un compte gratuit de « distribution limitée » est prévu : **20 appareils** (FAQ de Google du 2026-06-18), validation en deux étapes et profil de paiement Google exigés ; les installations par ADB en sont exemptées ; depuis le 2026-09-30, l'application stricte est limitée aux boutiques participantes de quatre pays. La diffusion complète hors Google Play demande un compte à **25 USD** (question QO-54). Canal de diffusion (décision QO-20) : fichiers APK sur la page de publication du dépôt pour l'alpha et la bêta, avec le compte gratuit de distribution limitée ; choix du canal public (F-Droid ou Google Play) avant la V1 |
| Nom technique et clé de signature | Android n'accepte une mise à jour que si elle porte le même nom technique (identifiant d'application) et la même signature ; ce nom est celui qui sera enregistré auprès de Google. Décision QO-35 : nom technique **définitif** et clé de diffusion **pérenne** fixés avant la première version diffusée ; clé gardée hors du dépôt et sauvegardée hors ligne (sa perte empêcherait toute mise à jour). Décision QO-46 : identifiant `io.github.zebul1n.pianohelper`, fondé sur le compte GitHub du porteur, **Zebul1n** ; un tiret dans ce nom devient `_` ; le compte ne doit jamais être renommé |

## 5. Documents à produire avant publication

**Identité affichée (décision QO-34).** L'écran « À propos », la notice de confidentialité et la notice du rapport de test affichent le **nom civil** du porteur et son **adresse e-mail personnelle**, soit **Ronan Launay** et **rlaunay@free.fr** (fournis le 2026-11-08). Mention de droits d'auteur : « © 2026 Ronan Launay et les contributeurs ». La même adresse sert aux rapports de test et aux demandes relatives aux données personnelles.

- [ ] Conditions générales d'utilisation
- [ ] Conditions générales de vente (si payant)
- [ ] Politique de confidentialité
- [ ] Mentions légales et licences tierces
- [ ] Registre des traitements
- [ ] Parcours de consentement parental
- [ ] Fiches de confidentialité des stores
