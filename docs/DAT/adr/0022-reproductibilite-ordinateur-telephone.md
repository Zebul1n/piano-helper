# ADR-0022 — Reproductibilité entre ordinateur et téléphone

| Statut | Date | Exigences |
|---|---|---|
| Acceptée (ε et δ du niveau 2 fixés par POC-01) | 2026-11-08 | ENF-QA-02, ENF-MAINT-01 ; EDB 06 §9.3 ; US-QUA-02 CA3 et CA5 ; INC-04 |

## Contexte

La règle de non-régression exige des exécutions déterministes et la comparaison des résultats du banc entre la CI, le poste du porteur (corpus privé) et le téléphone (US-QUA-02 CA5). La v0.1 proposait de tolérer des écarts d'instant inférieurs à 1 ms (INC-04). Trois relecteurs ont proposé des définitions complémentaires.

## Arbitrage

| Relecteur | Proposition | Retenu |
|---|---|---|
| `expert-audio-mir` (MIR-11, MIR-12) | Deux niveaux : identité au bit près pour le code maison ; tolérance ε pour l'inférence ; conditions : tables précalculées, pas de fonction transcendante dans les décisions, `-ffp-contract=off`, FTZ/DAZ fixés, pocketfft sans vectorisation ni fils | Oui, intégralement |
| `dev-natif-cpp` (DN-16 à DN-19) | Clang seul, options strictes sur `dsp` et les sources tierces, `CMAKE_CXX_EXTENSIONS OFF`, `long double` interdit, entiers dans `align` et `analyse`, trames ancrées sur la position absolue, corpus à 48 kHz, test sur exécuteur arm64 | Oui, intégralement |
| `devops-release` (DEV-07, DEV-08) | `ubuntu-24.04` figé, clang-18 explicite, version du compilateur dans le rapport, WSL 2 Ubuntu 24.04 sur le poste, mêmes options pour Gradle et les préréglages | Oui ; `dev-natif-cpp` souhaitait idéalement la même version majeure que le Clang du NDK : non retenu, car l'identité au bit près ne dépend pas de la version de Clang une fois les conditions du niveau 1 remplies [à vérifier par le test d'empreintes en I0] |

## Décision

**Niveau 1 — code du projet** (`dsp`, vérificateurs, inférence écrite à la main, `align`, `analyse`) : sorties **identiques au bit près**, comparées par empreinte ; tout écart est une anomalie. Conditions :
- **Clang seul** : clang-18 sur `ubuntu-24.04` (CI) et dans WSL 2 Ubuntu 24.04 (poste) ; Clang du NDK r30 (téléphone) ; ni GCC ni MSVC pour le banc ;
- cible `pianocore_fp_strict` (`-ffp-contract=off -fno-fast-math`) sur tous les modules de calcul et les sources tierces de traitement du signal ; `CMAKE_CXX_EXTENSIONS OFF` ; ni `-Ofast` ni `-march=native` ;
- tables précalculées hors ligne et embarquées (fenêtres, facteurs de rotation, filtres, noyaux de CQT) ; aucune fonction transcendante de la bibliothèque standard dans les décisions ;
- pocketfft avec `POCKETFFT_NO_VECTORS` et `POCKETFFT_NO_MULTITHREADING` ; FTZ/DAZ fixés à l'identique ; `long double` interdit (clang-tidy) ;
- instants en entiers, coûts en virgule fixe, départage explicite des égalités dans `align` et `analyse` ;
- trames ancrées sur la position d'échantillon absolue ; corpus audio à 48 kHz.

**Niveau 2 — sorties d'un moteur d'inférence tiers** (seulement si POC-01 en retient un) : max |p_téléphone − p_ordinateur| ≤ ε ; décisions et instants comparés en trames ; une décision qui change n'est pas une anomalie si sa marge au seuil sur l'ordinateur est < δ (elle est listée).

## Vérification

Empreintes sur vecteurs de référence en CI x86-64 **et** arm64 (`ubuntu-24.04-arm`), et sur le téléphone par `adb` ; `compare` refuse de comparer des compilateurs ou des images différents ; contrôle des options effectives (`compile_commands.json`).
