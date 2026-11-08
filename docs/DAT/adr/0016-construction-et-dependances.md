# ADR-0016 — Construction : Gradle, CMake 4.1.2, NDK r30, sources tierces incluses

| Statut | Date | Exigences |
|---|---|---|
| Acceptée (révisée en v0.2) | 2026-11-07, révisée le 2026-11-08 | ENF-COMP-02, ENF-RES-03 ; US-CNF-04 CA2 et CA3 ; JUR-03 ; C-T6, C-T7 |

## Contexte

Chaque APK diffusé doit correspondre à une étiquette dont les sources sont complètes, avec des versions figées, des bibliothèques alignées sur 16 Ko et une taille maîtrisée.

## Décision

| Sujet | Retenu |
|---|---|
| Sources C++ tierces | Copiées dans `third_party/` (ni sous-module, ni `FetchContent`, ni LFS) ; aucun binaire précompilé dans les versions diffusées (décision QO-50) |
| Dépendances Gradle | Catalogue de versions ; `verification-metadata.xml` produit par la CI puis complété sur le poste ; `distributionSha256Sum` du wrapper ; JDK 17 figé (DEV-11) |
| NDK | r30 LTS (30.0.16248370) fixé |
| CMake | **4.1.2** dès I0, même binaire pour Android et l'hôte ; toutes les sources tierces sont compatibles avec CMake 4 (DEV-06). L'étape 3.31 de la v0.1 est supprimée |
| Options natives | Toutes dans `core/CMakeLists.txt` ; contrôle d'égalité des options effectives entre Gradle et les préréglages (DEV-07) ; options strictes de l'[ADR-0022](0022-reproductibilite-ordinateur-telephone.md) |
| Bibliothèque C++ standard | `c++_shared` (deux `.so` du projet, DN-28, [ADR-0023](0023-deux-bibliotheques-natives.md)) |
| Taille | Verovio sans les formats inutiles ; `-Os`, `--gc-sections`, LTO ; `abiFilters` par variante ; seuil de taille en CI (DN-26, DEV-03) |
| Temps de construction | `ccache` indexé sur `third_party/` ; bibliothèques tierces statiques séparées ; préréglages réduits sur les PR ; mesure en I0 (DN-29) |
| Reproductibilité des binaires | `-ffile-prefix-map`, `-Wl,--build-id=none`, suppression de `.comment` ; contrôle « deux répertoires → `.so` identiques », bloquant avant le choix de F-Droid (DEV-16) |
| Lint Kotlin | ktlint et Android Lint ; detekt reporté à sa 2.0 stable (DEV-04) |

## Conséquences

Un « socle Gradle » vide est construit en CI en I0 avec tous les greffons avant d'écrire du code (DEV-04).
