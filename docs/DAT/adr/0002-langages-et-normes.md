# ADR-0002 — Langages : Kotlin et C++20

| Statut | Date | Exigences |
|---|---|---|
| Acceptée | 2026-11-07 | ENF-COMP-01, ENF-COMP-02, ENF-MAINT-01 |

## Contexte

[ADR-0001](0001-decoupage-application-noyau-outils.md) sépare l'application et le noyau. Il faut fixer les langages et leurs versions, en limitant le nombre de technologies (K6).

## Options étudiées

| Domaine | Options | Retenu |
|---|---|---|
| Application | Kotlin ; Java | **Kotlin 2.4.21** (Apache-2.0) [VÉRIFIÉ 2026-11-07, kotlinlang.org] : langage recommandé pour Android et Compose |
| Noyau | C++17 ; C++20 ; Rust | **C++20** : Verovio exige C++20 (`CMAKE_CXX_STANDARD 20`) [VÉRIFIÉ 2026-11-07, `cmake/CMakeLists.txt` de Verovio] ; Oboe, TinySoundFont, ONNX Runtime sont en C/C++ ; Rust ajouterait une chaîne d'outils et des liaisons |
| Construction du noyau | CMake ; ndk-build ; Bazel | **CMake** (pris en charge par Gradle, utilisable seul pour l'hôte) |
| Outils | Node.js ; Python | **Node.js** pour les scripts (installé) ; **Python** seulement pour les prototypes des POC, dans WSL 2 (décision QO-48) |

## Décision

Kotlin (application), C++20 (noyau, `CMAKE_CXX_EXTENSIONS OFF`), CMake 4.1.2, Clang seul pour toutes les constructions ([ADR-0022](0022-reproductibilite-ordinateur-telephone.md)). Python est exclu de l'APK, de sa chaîne de construction et de la CI bloquante (ce qui pèse contre la construction d'ONNX Runtime par ses scripts, [ADR-0011](0011-moteur-d-inference-onnx-runtime.md)). Toute autre technologie demande une nouvelle ADR.

## Conséquences

- NDK r30 ([ADR-0016](0016-construction-et-dependances.md)).
- Règles de style : ktlint et Android Lint (Kotlin ; detekt reporté, DEV-04), clang-format et clang-tidy (C++).
