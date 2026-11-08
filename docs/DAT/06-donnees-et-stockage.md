# 06 — Données et stockage

Modèle physique dérivé du modèle conceptuel de l'[EDB 09](../EDB/09-modele-de-donnees.md). Décision : [ADR-0014](adr/0014-persistance-room-et-fichiers.md).

## 1. Répartition

| Donnée | Support | Raison |
|---|---|---|
| Profil, morceaux, séances, résultats, écarts, calibrages, signalements, préférences | Base **Room** (SQLite) `pianohelper.db` | Requêtes, transactions, migrations (ENF-FIA-01, ENF-FIA-03) |
| Fichier source importé | Fichier dans `filesDir/scores/<morceau>/source.<ext>`, tel quel | ENF-FIA-02, US-IMP-07 CA1 |
| Partition convertie (pivot MEI avec identifiants structurels) | `filesDir/scores/<morceau>/score-v<N>.mei` | [ADR-0008](adr/0008-modele-de-partition-et-format-pivot.md) |
| Caches de rendu (liste d'affichage, géométrie) | `filesDir/scores/<morceau>/render/<variante>.bin` | Régénérables, mais gardés hors de `cacheDir` pour ne pas être purgés par le système (ENF-PERF-05) |
| Démonstrations | `assets/demos/` dans l'APK, copiées dans `filesDir` à la première ouverture | Licence et attribution dans le fichier (US-BIB-05 CA3) |
| Son du micro | **Jamais** sur le stockage (variantes bêta et publique) | [ADR-0013](adr/0013-son-en-memoire-uniquement.md) |
| Enregistrements de l'enregistreur de séance | `filesDir/capture/` de la **variante interne** seulement | US-QUA-05 |

Une seule base pour le MVP (un profil local, EF-CPT-01). Le multi-profils (V1) est déjà prévu par une clé `profil_id` dans les tables.

## 2. Schéma (version 1)

Types : `TEXT` UUID pour les identifiants d'entités ; instants en millisecondes UTC (`INTEGER`) avec le décalage du fuseau à l'instant (US-HIS-01 CA4) ; durées et écarts en microsecondes.

| Table | Colonnes principales | Notes |
|---|---|---|
| `profil` | id, nom, niveau_declare, niveau_exigence, source_preferee, notation, retour_direct_defaut, mineur | EF-CPT-02 |
| `morceau` | id, profil_id, titre, compositeur, arrangeur, origine (`musicxml`, `midi`, `demo`), demo_cle (nullable), masque, favori, date_ajout, derniere_pratique, tempo_reference, reglages_json | `reglages_json` : zoom par orientation, derniers réglages de pratique (US-PRA-01 CA4) ; `masque` pour les démonstrations (US-BIB-04 CA3) |
| `fichier_source` | id, morceau_id, format, nom_origine, taille, sha256, chemin, date_import | Doublons détectés par `sha256` (US-IMP-07 CA4) |
| `partition` | id, morceau_id, fichier_source_id, version_modele, version_import, chemin_mei, nb_mesures, nb_portees, rapport_json, actuelle (booléen) | Une ligne par import ; une réimportation crée une nouvelle ligne, l'ancienne reste pour RG-02 |
| `calibrage` | id, profil_id, appareil (`Build.MANUFACTURER` + `Build.MODEL`), source, correction_latence_us (nulle si les horodatages suffisent, ADR-0024), diapason_hz, profil_registre_json (déviation d'accord et inharmonicité B par registre, MIR-18), niveau_ref, preset_demande, unprocessed_annonce, agc_detecte, date | US-ECO-05 CA5 : « réglé sur un autre téléphone » se déduit de `appareil` ; le calibrage dépend du téléphone, du piano et de la pièce (INC-09) |
| `seance` | id, profil_id, morceau_id, partition_id, debut_utc, decalage_fuseau_min, duree_ms, mesure_debut, mesure_fin, mode, mains, tempo_regle, source, calibrage_id, niveau_exigence, version_analyse, etat (`en_cours`, `terminee`, `interrompue`, `non_analysee`), taux_incertains, nb_blocs_perdus | `en_cours` pendant le jeu (points de reprise) |
| `passage` | id, seance_id, rang, mesure_debut, mesure_fin, tempo_effectif | Une répétition de la boucle = un passage (EF-PRA-11) |
| `resultat` | id, seance_id, passage_id (nullable), version_analyse, niveau_exigence, s_global, s_notes, s_rythme, s_continuite, fiable, date | RG-04 : une réanalyse ajoute une ligne |
| `note_jouee` | seance_id, rang, hauteur, debut_us, fin_us, velocite, confiance | Clé primaire (seance_id, rang) |
| `ecart` | id, resultat_id, type, sous_type, note_attendue (identifiant structurel), passage_occurrence, note_jouee_rang, delta_us, delta_fraction, confiance, severite, nb_essais, conteste | Index (resultat_id), (note_attendue) |
| `signalement` | id, ecart_id (nullable), seance_id, nature, jour (date sans heure), mesure, type, confiance, source, niveau | US-RES-08 CA3 : ni titre ni heure |
| `preference` | cle, valeur | Réglages généraux (US-CPT-04) ; évite une seconde technologie de stockage |

Identifiants de notes : chaînes structurelles stables ([07 §5](07-import-et-rendu-de-partition.md#5-identifiants-stables-des-notes)), pas de clé étrangère vers une table de notes : la partition vit dans le fichier MEI.

## 3. Volumétrie et compactage

| Élément | Estimation | Source |
|---|---|---|
| Ligne `note_jouee` | ≈ 40 octets avec index | Estimation [ARCH] |
| Ligne `ecart` | ≈ 80 octets avec index | Estimation [ARCH] |
| Séance type (500 notes) | ≈ 50 Ko | |
| Séance longue (5 000 notes, EDB 09 §4) | ≈ 0,5 Mo | |
| Profil très actif (1 000 séances par an, 500 notes en moyenne) | ≈ 50 Mo par an | Au pire (5 000 notes), 500 Mo : à mesurer |

Décision QO-11 : tout est gardé au MVP ; la place réelle est mesurée en bêta (le rapport de test, US-CNF-05, contient la taille de la base) ; le compactage est décidé avant la V1. Le schéma le permet sans migration lourde : supprimer les lignes `note_jouee` et `ecart` des anciennes séances en gardant `resultat` et des agrégats par mesure.

## 4. Fichiers

- **Écriture atomique** : écrire dans `<nom>.tmp`, `fsync`, renommer, puis `fsync` du dossier. Un fichier n'est référencé dans la base qu'après son renommage, dans la même opération logique ; au démarrage, un ménage supprime les `.tmp` et les dossiers sans ligne en base (US-IMP-06 CA5 : pas de fichier orphelin).
- **Fichier de transit** (DA-17) : à l'import, le processus principal copie aussitôt le flux reçu (`VIEW`, `SEND` ou sélecteur ; droit de lecture temporaire, taille parfois inconnue, stockage en ligne parfois lent) dans `filesDir/import-transit/`, en comptant les octets (refus au-delà de 20 Mio sans attendre la fin) et en calculant le SHA-256 (doublons, US-IMP-07 CA4) ; le processus d'import reçoit un descripteur de cette copie et écrit dans un dossier de travail ; le principal ne valide qu'après un code de succès ([ADR-0021](adr/0021-import-en-processus-isole.md)).
- **Ménage** : fait une seule fois, au démarrage du **processus principal** (jamais dans `:import`), avant toute création de séance ou d'import (DA-16, DA-07).
- **Stockage plein** : l'espace libre est vérifié avant l'import (taille du fichier × 4 [ARCH]) et avant l'enregistrement d'une séance ; échec → message, rien à moitié créé (US-IMP-06 CA5, US-HIS-01 CA3).

## 5. Durabilité et arrêts brutaux

ENF-FIA-01 et US-HIS-01 CA2 (50 arrêts forcés du processus et 10 arrêts brutaux de l'émulateur) :

- SQLite en journal **WAL** avec `PRAGMA synchronous=FULL` : une transaction validée survit à une coupure d'alimentation ; en WAL avec `NORMAL`, les dernières transactions peuvent être perdues [NON VÉRIFIÉ : à confirmer sur sqlite.org/pragma.html avant l'implémentation]. Le réglage vaut **par connexion** et Room utilise plusieurs connexions (DA-14) : un test instrumenté lit `PRAGMA synchronous` dans une transaction d'écriture ; s'il n'est pas `FULL`, le réglage est appliqué à chaque connexion par l'API du pilote de Room 2.8.
- La fin de séance est **une seule transaction** (séance, passages, résultat, notes, écarts).
- Points de reprise pendant la séance ([04 §6](04-application-android.md#6-cycle-de-vie-dune-séance-et-interruptions)) : la séance en cours existe en base à l'état `en_cours` ; au démarrage suivant, toute séance `en_cours` devient `interrompue` (US-PRA-06 CA3).

## 6. Migrations

- Schéma exporté en JSON par Room (`room.schemaLocation`) et versionné dans le dépôt.
- Une **base de référence** est archivée pour chaque version diffusée (`app/src/test/fixtures/db/vX.Y.Z.db`), remplie par un générateur de données ; le test de migration ouvre chacune et la migre jusqu'à la version courante (US-HIS-03 CA2), avec `MigrationTestHelper`.
- Avant une migration : vérification de l'espace libre (≥ 2 × taille de la base) ; copie de sauvegarde locale `pianohelper.db.vN.bak` ; migration en transaction. Échec (y compris par injection de défaut, US-HIS-03 CA3) : restauration de la copie, ouverture **en lecture seule**, message, export du rapport de test possible.
- Fichiers de partition : `version_modele` dans `partition` ; si le noyau ne sait plus lire une ancienne version, la partition est **réimportée** depuis le fichier source (ENF-FIA-02, US-VIS-08 CA4).
- Pas de `fallbackToDestructiveMigration` : interdit par règle de revue.

## 7. Export et suppression

| Action | Mise en œuvre | Exigences |
|---|---|---|
| Exporter le fichier source | `ACTION_CREATE_DOCUMENT` (dossier choisi) ou partage système via `FileProvider` ; octets identiques | US-BIB-05, EF-BIB-03 |
| Supprimer un morceau | Transaction : lignes en cascade (clés étrangères `ON DELETE CASCADE`), puis dossier `scores/<morceau>/` ; démonstration : `masque = 1` | RG-01, US-BIB-04 |
| Supprimer toutes mes données | `ActivityManager.clearApplicationUserData()` : efface les données internes de l'application, retire les autorisations et **termine le processus** (aucune API ne redémarre une application) ; le texte de l'écran E31 l'annonce (« l'application va se fermer ») ; les fichiers déjà exportés ne sont pas concernés (JUR-02) (DA-15 ; comportement exact [NON VÉRIFIÉ], à relire avant l'implémentation ; modification de US-CPT-05 proposée au [fichier 15](15-modifications-edb-prd-proposees.md)) | EF-CPT-06, US-CPT-05 |
| Rapport de test | JSON lisible, généré à la demande, partagé par le système ; jamais écrit ailleurs que dans `cacheDir` le temps du partage | US-CNF-05 |

## 8. Sauvegarde Android désactivée

Décision QO-36, ENF-CONF-01, JUR-02.

```xml
<application
    android:allowBackup="false"
    android:dataExtractionRules="@xml/data_extraction_rules" ... >
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<!-- res/xml/data_extraction_rules.xml : tout exclure (QO-36, ENF-CONF-01) — syntaxe de la revue dev-android, annexe A -->
<data-extraction-rules>
    <cloud-backup disableIfNoEncryptionCapabilities="true">
        <exclude domain="root" path="." />
        <exclude domain="file" path="." />
        <exclude domain="database" path="." />
        <exclude domain="sharedpref" path="." />
        <exclude domain="external" path="." />
        <exclude domain="device_root" path="." />
        <exclude domain="device_file" path="." />
        <exclude domain="device_database" path="." />
        <exclude domain="device_sharedpref" path="." />
    </cloud-backup>
    <device-transfer>
        <exclude domain="root" path="." />
        <exclude domain="file" path="." />
        <exclude domain="database" path="." />
        <exclude domain="sharedpref" path="." />
        <exclude domain="external" path="." />
        <exclude domain="device_root" path="." />
        <exclude domain="device_file" path="." />
        <exclude domain="device_database" path="." />
        <exclude domain="device_sharedpref" path="." />
    </device-transfer>
    <!-- Pas de <cross-platform-transfer> : il exige <platform-specific-params bundleId teamId contentVersion>
         et aucune application iOS n'existe. -->
</data-extraction-rules>
```

Pourquoi ces règles (DA-13) : pour une application qui cible Android 12 ou plus, `allowBackup="false"` désactive la sauvegarde dans le nuage mais, chez certains constructeurs, **pas le transfert d'appareil à appareil** ; une section absente rend ce mode entièrement actif ; les neuf domaines, dont les quatre domaines `device_*` (stockage protégé de l'appareil), sont exclus ; `cacheDir`, `codeCacheDir` et `noBackupFilesDir` sont toujours exclus par le système ; `android:fullBackupContent` ne concerne qu'Android 11 et inférieur [VÉRIFIÉ 2026-11-07 et par `dev-android` le 2026-11-08, developer.android.com/identity/data/autobackup]. La section `cross-platform-transfer` (Android 16 QPR2) exige des paramètres d'application iOS : elle est omise ; que son absence interdise tout transfert vers iOS est une **déduction** [NON VÉRIFIÉ], à tester sur un émulateur API 36.1 dès que possible.

Vérification (US-CNF-01 CA3) sur l'émulateur : `adb shell bmgr enable true`, transport local, `adb shell bmgr backupnow <paquet>` : aucune donnée de l'application dans la sauvegarde ; test de transfert d'appareil à appareil en mode test (`backup_enable_d2d_test_mode`) [VÉRIFIÉ 2026-11-07, developer.android.com/guide/topics/data/testingbackup]. Contrôle automatique sur l'APK : `allowBackup="false"`, présence des deux sections et des neuf exclusions dans le XML compilé.
