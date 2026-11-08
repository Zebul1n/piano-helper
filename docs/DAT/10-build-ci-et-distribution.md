# 10 — Construction, intégration continue et distribution

Décisions : [ADR-0016](adr/0016-construction-et-dependances.md), [ADR-0017](adr/0017-integration-continue-et-diffusion.md) (acceptée : QO-45, GitHub), [ADR-0022](adr/0022-reproductibilite-ordinateur-telephone.md), [ADR-0023](adr/0023-deux-bibliotheques-natives.md). À détailler par `devops-release` dans `docs/DEVOPS/`.

## 1. Structure du dépôt

```
piano-helper/
  app/                   module Android principal (variantes interne, beta, publique)
  feature/ domain/ data/ platform/ bridge/ tools/   modules Kotlin (04 §2)
  core/                  noyau C++ (CMakeLists.txt unique, CMakePresets.json, include/, src/, capi/, tests/)
  core/import/           bibliothèque libpianoimport (Verovio, lecteurs, mise en page)
  core/platform/android/ core/platform/host/  core/jni/
  third_party/           sources tierces copiées à une version étiquetée (verovio, oboe, tinysoundfont, pocketfft, dr_libs, nlohmann_json, miniz, googletest)
  tools/bench/           banc (C++) ; tools/scripts/ (Node.js : elf16k.mjs, contrôles de l'APK, documentation) ; tools/poc/ (Python, hors APK)
  corpus/public/         emplacement du dépôt piano-helper-corpus à une étiquette donnée (QO-49 : partitions, MIDI, annotations ; audio joint à ses versions ; CC0)
  bench/reference/  bench/reports/
  docs/  LICENSES/  REUSE.toml  PRIVACY.md  README.md  CONTRIBUTING.md (DCO)
  gradle/libs.versions.toml  gradle/verification-metadata.xml
  .gitattributes  .github/workflows/
```

- **Pas de sous-modules ni de Git LFS pour le code** (JUR-03) ; aucun `export-ignore` sur `third_party/`, `core/` ou `app/` (DEV-17).
- `.gitattributes` dès le premier commit (DEV-12) : `* text=auto eol=lf`, `*.bat text eol=crlf`, `binary` pour `*.flac *.wav *.sf2 *.onnx *.png *.mxl` ; `git config core.longpaths true` sur le poste ; bit exécutable de `gradlew` et des scripts (`git update-index --chmod=+x`).

## 2. Chaîne de construction

| Outil | Version | Source et date |
|---|---|---|
| Gradle | 9.9.0, wrapper avec `distributionSha256Sum` | services.gradle.org [VÉRIFIÉ 2026-11-07] |
| Android Gradle Plugin | 9.5.x (Gradle ≥ 9.6.0, JDK 17, Build-Tools 36.0.0) | [VÉRIFIÉ 2026-11-07] |
| NDK | **r30 LTS** (30.0.16248370), fixé par `ndkVersion` (AGP 9.5 utilise r28 par défaut) ; préinstallé sur l'image `ubuntu-24.04` [VÉRIFIÉ par `devops-release`] ; compatibilité AGP 9.5 + r30 confirmée par la première construction en CI | [VÉRIFIÉ 2026-11-07] |
| CMake | **4.1.2** dès I0, déclaré dans `externalNativeBuild { cmake { version = "4.1.2" } }` ; même binaire (`$ANDROID_HOME/cmake/4.1.2/bin/cmake`, avec Ninja) pour les constructions hôte en CI. Toutes les sources tierces sont compatibles avec CMake 4 (minimums vérifiés par `devops-release`) (DEV-06) | [VÉRIFIÉ par `devops-release` le 2026-11-07] |
| C++ | C++20, `CMAKE_CXX_EXTENSIONS OFF` | Verovio exige C++20 |
| Compilateur hôte | **clang-18** sur `ubuntu-24.04` (CI) et dans WSL 2 Ubuntu 24.04 (poste) ; Clang ≥ 20 pour la seule tâche RealtimeSanitizer | [ADR-0022](adr/0022-reproductibilite-ordinateur-telephone.md) |
| JDK | 17, figé (`kotlin { jvmToolchain(17) }` ou critères de JVM du démon) (DEV-11) | — |
| Bibliothèque C++ standard | `c++_shared` (deux `.so` C++ du projet, DN-28) | [ADR-0023](adr/0023-deux-bibliotheques-natives.md) |

- **Toutes les options de compilation natives sont dans `core/CMakeLists.txt`**, aucune dans Gradle, sauf l'option de variante `PIANO_INTERNAL_TOOLS` ; les préréglages Android utilisent le NDK r30 et `ANDROID_PLATFORM=android-33` ; un contrôle compare les options effectives des constructions Gradle et des préréglages (`compile_commands.json`) (DEV-07).
- Cible `INTERFACE pianocore_fp_strict` (`-ffp-contract=off -fno-fast-math`) appliquée aux modules de calcul et aux sources tierces de traitement du signal ; `-Ofast` et `-march=native` interdits, vérifié par script (DN-16).
- Verovio compilé avec `NO_HUMDRUM_SUPPORT`, `NO_ABC_SUPPORT`, `NO_PAE_SUPPORT`, `NO_GABC_SUPPORT`, `NO_EDIT_SUPPORT`, et `-Os`, `--gc-sections`, LTO, symboles retirés (DN-26).
- **Pages de 16 Ko** : NDK r28+ et AGP ≥ 8.5.1 alignent par défaut ; contrôles du §4 ; `jniLibs.useLegacyPackaging = true` interdit (DEV-02).
- **Construction reproductible** (DEV-16, utile pour F-Droid) : `-ffile-prefix-map=<racine>=.`, `-Wl,--build-id=none`, suppression de la section `.comment` ; contrôle « deux constructions dans deux répertoires → `.so` identiques », non bloquant au MVP, bloquant avant le choix de F-Droid.
- **Temps de construction** (DN-29) : `ccache` avec cache CI indexé sur l'empreinte de `third_party/` ; sources tierces en bibliothèques statiques séparées ; durée à froid et avec cache mesurée en I0, objectif ≤ 15 min avec cache [ARCH].

## 3. Variantes et signature

| Variante | ABI | Code natif | Signature | Contrôles |
|---|---|---|---|---|
| `interne` | `arm64-v8a`, `x86_64` | `PIANO_INTERNAL_TOOLS=ON` | Clé de débogage | — |
| `beta`, `publique` | `arm64-v8a` seulement (`abiFilters`, décision QO-47) | `PIANO_INTERNAL_TOOLS=OFF` | **Clé de diffusion pérenne** (QO-35), schémas v2 et v3 | §4 |

**Identifiant de l'application** (décision QO-46 du 2026-11-08) : `io.github.zebul1n.pianohelper`, définitif dès la première version diffusée. Il dérive du compte GitHub du porteur, **Zebul1n** (fourni le 2026-11-08), écrit en minuscules dans l'identifiant ; un tiret dans ce nom devient `_` ; le compte ne doit jamais être renommé ([fichier 14](14-decisions-a-prendre.md)).

**Clé de diffusion** (DEV-09) : PKCS12, RSA 4096, validité ≥ 25 ans, créée par le porteur avec `keytool`, **sauvegardée hors ligne** dès sa création ; secrets (keystore en base64, mots de passe, alias) dans un **environnement GitHub `release`**, limité aux étiquettes `v*`, avec **relecteur requis = le porteur** ; `permissions: contents: read` par défaut, `contents: write` seulement dans la tâche de diffusion ; les PR des copies du dépôt n'accèdent jamais aux secrets. **Empreinte SHA-256 du certificat** publiée dans le README et dans chaque note de version (`apksigner verify --print-certs`). Avec minSdk 33, la signature v3.1 permettra une rotation de clé plus tard. Le nom technique est fixé en I0 (QO-46).

**Numéro de version** (DEV-10) : étiquettes `vX.Y.Z` uniquement ; `versionCode = X·1 000 000 + Y·1 000 + Z` (Z < 1 000) ; constructions non étiquetées : `versionCode = 1`, `versionName = X.Y.Z-dev+<sha>`, jamais diffusées ; contrôle : `versionCode` strictement supérieur à celui de la diffusion précédente.

## 4. Intégration continue

Décision QO-45 du 2026-11-08 : **GitHub Actions**, exécuteurs standard gratuits pour les dépôts publics (Linux x64 et arm64 ; 6 h par tâche ; cache de 10 Go par dépôt ; artefacts gardés 90 jours au plus) [VÉRIFIÉ par `devops-release` le 2026-11-07]. Toutes les tâches tournent sur **`ubuntu-24.04`**, jamais `ubuntu-latest` (DEV-08). Alternatives : GitLab.com (400 minutes par mois au niveau gratuit), Codeberg (CI en alpha) [VÉRIFIÉ 2026-11-07].

| Déclencheur | Tâches | Bloquant |
|---|---|---|
| Chaque PR (branche `main` protégée : PR obligatoire, contrôles requis, DEV-18) | `reuse lint` ; ktlint, Android Lint (detekt absent au MVP, DEV-04) ; tests Kotlin ; noyau hôte clang-18 avec ASan/UBSan et GoogleTest ; empreintes de reproductibilité ; **banc sur le corpus public** ; construction `android-arm64` et APK `beta` non signé ; **contrôles de l'APK** (ci-dessous) ; Licensee ; contrôle de la documentation (Node.js) | Oui |
| Branche principale, chaque nuit | Préréglages `android-x86_64` et `host-release` ; empreintes sur l'exécuteur arm64 ; RealtimeSanitizer et ThreadSanitizer ; tests instrumentés (API 33, 36, 37 ; profil ≥ 600 dp ; `google_apis_ps16k`) ; arrêts brutaux de l'émulateur par script dédié (DEV-14) ; fuzzing de 10 minutes ; migrations | Oui pour la diffusion suivante |
| Étiquette `vX.Y.Z` | Construction signée de `beta` ; nomenclature `reuse spdx` (CycloneDX facultatif) ; archive de sources et **preuve de reconstruction hors ligne** ; sommes SHA-256 ; diffusion immuable | — |

**Contrôles de l'APK** (`beta` et `publique` bloquants, `interne` pour information) :

| Contrôle | Outil | Source |
|---|---|---|
| ABI : `lib/` ne contient que `arm64-v8a` | Script | DEV-03 |
| Segments `LOAD` alignés ≥ 0x4000 **et** fin de `GNU_RELRO` alignée (ou RELRO en fin de son `LOAD`), sur **toutes** les `.so` | `tools/scripts/elf16k.mjs` (script Node.js sans dépendance de l'[annexe A de la revue `devops-release`](revues/devops-release.md#annexe-a--contrôle-16-ko-proposé-nodejs-sans-dépendance)) | DEV-02, [VÉRIFIÉ 2026-11-07, page-sizes] |
| Alignement ZIP des `.so` non compressées | `zipalign -c -P 16 -v 4` (Build-Tools 36.0.0) | DEV-02 |
| Permissions : liste blanche et liste noire | `aapt2 dump permissions` | [09 §1](09-securite-confidentialite-licences.md#1-permissions-et-absence-de-réseau) |
| Sauvegarde : `allowBackup="false"`, deux sections, neuf exclusions | Lecture du manifeste et du XML compilé | DA-13 |
| Outils de test absents : classes de `:tools` **et** symboles natifs de l'enregistreur et de dr_wav | `apkanalyzer`, `llvm-nm -D` | DA-23 |
| Télémétrie absente | Recherche de chaînes | JUR-D01 |
| Taille : seuil bloquant par variante (80 Mo pour `beta` [ARCH], ajusté après POC-01 et POC-04) | Script | DEV-03 |

**Cache** (DEV-15) : corpus (clé = étiquette du corpus), Gradle (lecture seule sur les PR), une image d'émulateur par niveau d'API ; budget mesuré en I0.

**Socle Gradle en I0** (DEV-04) : projet vide AGP 9.5 + Gradle 9.9 + chaque greffon (Licensee, AboutLibraries, CycloneDX, ktlint), construit en CI avant d'écrire du code ; tout greffon qui échoue est retiré ou remplacé.

## 5. Diffusion et sources

Décision QO-20 : APK sur la page de publication du dépôt pour l'alpha et la bêta ; canal public choisi avant la V1.

Chaque diffusion contient (US-CNF-04, JUR-03) : l'APK signé et sa somme SHA-256 ; l'empreinte du certificat ; la phrase « Code source de cette version : [lien vers l'étiquette] — licence GPL-3.0-or-later » ; l'archive `piano-helper-X.Y.Z-src.tar.gz` produite par `git archive --format=tar --prefix=piano-helper-X.Y.Z/ vX.Y.Z | gzip -n` avec un `SOURCES.md` qui renvoie à `libs.versions.toml` et `verification-metadata.xml` pour les dépendances Gradle (JUR-D13) ; la nomenclature ; les notes de version (ce qui n'est vérifié que par simulation ou sur un seul appareil) ; le guide d'installation.

**Preuve des sources** (DEV-17, JUR-D13) : à chaque étiquette, l'archive est extraite dans un répertoire vierge et `beta` est reconstruit **hors ligne** (`--offline`, cache des dépendances rempli) ; si la reconstruction échoue, la diffusion échoue. Si une version d'ONNX Runtime était diffusée, l'archive de ses sources et de ses dépendances serait jointe (JUR-D03).

**Diffusions immuables** de GitHub : étiquette et pièces jointes verrouillées, attestation ; diffusion créée en brouillon, pièces jointes ajoutées, puis publiée (DEV-09) [disponibilité gratuite : NON VÉRIFIÉ]. Copie de secours du dépôt (Software Heritage ou miroir Codeberg en lecture) pour limiter la dépendance à une plateforme.

**Vérification des développeurs Android** (DEV-01) [VÉRIFIÉ par `devops-release` le 2026-11-07] :
1. depuis le 2026-09-30, l'application stricte ne vise que les **boutiques participantes** de quatre pays ; une application installée directement (APK de GitHub) n'est pas encore concernée ; extension mondiale annoncée pour 2027 sans date ;
2. les installations par **ADB sont exemptées** : la variante interne et les essais du porteur ne sont pas touchés ;
3. le compte de distribution limitée est gratuit mais exige une **validation en deux étapes**, un profil de paiement Google et un e-mail ; il couvre **20 appareils** (FAQ du 2026-06-18 ; le guide dédié dit 50) : planifier sur 20 ; partage par QR code ou lien, avec consentement sur chaque appareil (à décrire dans le guide d'installation) ;
4. le **compte de diffusion complète coûte 25 USD** : contraire au budget nul pour une V1 publique hors Play (QO-54) ;
5. sans enregistrement ni ADB, la procédure « avancée » impose le mode développeur, un redémarrage, une attente unique de 24 h et une authentification ;
6. une clé perdue empêche l'enregistrement du nom de paquet (QO-35).

**F-Droid (avant la V1)** : un AAR libre de Maven Central n'est pas exclu par principe, mais la télémétrie d'ONNX Runtime entraînerait au moins l'anti-fonctionnalité « Tracking » (JUR-D12). La voie qui garde **une seule clé** est la publication par F-Droid de l'APK signé par le développeur, si sa construction le reproduit (`Binaries`, `AllowedAPKSigningKeys`) (DEV-16).

## 6. Taille de l'APK

| Élément | Mesure ou estimation (arm64) | Statut |
|---|---|---|
| `libpianoimport.so` (Verovio sans les formats inutiles, lecteurs, mise en page) | 8 à 14 Mo ; roue Python complète de Verovio 6.4.1 : 17,5 Mo | [VÉRIFIÉ par `dev-natif-cpp`] pour la roue ; Android : [NON VÉRIFIÉ, POC-04] |
| `libpianocore.so` (noyau, Oboe, TinySoundFont) + `libc++_shared.so` | Quelques Mo + environ 1 Mo | [NON VÉRIFIÉ] |
| ONNX Runtime, AAR officiel 1.31.1 | **34,2 Mo** (40,8 Mo en x86_64) ; l'AAR fournit quatre ABI | [VÉRIFIÉ par `dev-natif-cpp` et `devops-release`] ; **exclu des versions diffusées** (QO-50) ; 0 avec l'option C |
| Données Verovio (Bravura, Leipzig, texte) | ≈ 1 Mo | [VÉRIFIÉ par `dev-natif-cpp`] |
| Banque de sons (small) | ≤ 6 Mio | [VÉRIFIÉ 2026-11-07] |
| Modèle (Basic Pitch, si retenu) | 0,23 Mo | [VÉRIFIÉ 2026-11-07] |

Les `.so` ne sont pas compressées dans l'APK (bibliothèques non extraites) ; c'est pourquoi les APK diffusés ne portent qu'une ABI (QO-47) et qu'un seuil de taille bloquant est contrôlé en CI (§4).

## 7. Prérequis du poste du porteur

Liste minimale de `devops-release` ([revue §5](revues/devops-release.md#5-prérequis-du-poste-du-porteur-liste-minimale)), retenue par la décision QO-48 du 2026-11-08 ; installation à faire par le porteur avant I0 et avant POC-01 et POC-05.

| # | Élément | Pour |
|---|---|---|
| 1 | Virtualisation activée dans le BIOS ; **Plateforme de l'hyperviseur Windows (WHPX)** ; ne pas installer AEHD, qui prend fin le 2026-12-31 [VÉRIFIÉ par `devops-release` le 2026-11-08] | Émulateur accéléré, WSL 2 |
| 2 | **Android Studio** stable, avec SDK 36 et 37, Build-Tools 36.0.0, Platform-Tools, Emulator, NDK 30.0.16248370, CMake 4.1.2, images `google_apis` 33 et 36 (ou 37), une image `google_apis_ps16k` | Application, émulateur, `keytool`, `apksigner` |
| 3 | Accès `adb` au téléphone (débogage sans fil ou pilote USB du fabricant) | Variante interne, banc et mesures sur le téléphone |
| 4 | **WSL 2 + Ubuntu 24.04** : `build-essential clang-18 lld-18 ninja-build python3-venv pipx git`, CMake 4.1.2 (archive de cmake.org ou `pipx`), `pipx install reuse` | Noyau hôte, sanitiseurs, fuzzing, banc sur le corpus privé, prototypes Python (venv), `reuse lint` ; **même environnement que la CI** |
| 5 | Réglages Git (`core.longpaths`, `git commit -s` pour le DCO) | Dépôt partagé Windows / CI |
| 6 | Compte GitHub du porteur (existant ; Zebul1n, QO-46), avec validation en deux étapes | Dépôt, CI, environnement `release` |
| 7 | Avant la bêta : compte de distribution limitée (validation en deux étapes, profil de paiement Google, e-mail, sans frais) | Enregistrement du nom de paquet et de la clé |

Inutiles : Python sous Windows, outils de compilation de Visual Studio, Docker Desktop, LLVM pour Windows. Espace disque : environ 3 Go pour le NDK, 1 à 2 Go par image système, quelques Go pour WSL et les caches.
