# ADR-0019 — Bibliothèques et règles de traitement du signal

| Statut | Date | Exigences |
|---|---|---|
| Proposée — à confirmer par POC-01 | 2026-11-07, révisée le 2026-11-08 | EF-ECO-04, EF-ECO-06, EF-ECO-07, EF-ECO-10, ENF-QA-02, ENF-MAINT-01 |

## Contexte

Le vérificateur temps réel, le réglage du micro, l'indicateur de signal, l'étage différé et le banc ont besoin de transformées, de filtres, de rééchantillonnage et de lecture de fichiers, avec des sorties identiques au bit près sur l'ordinateur et le téléphone ([ADR-0022](0022-reproductibilite-ordinateur-telephone.md)).

## Décision proposée

| Besoin | Retenu | Conditions (revues) |
|---|---|---|
| Transformée de Fourier (fonction d'attaque, différé) | **pocketfft** (BSD-3-Clause) ; KissFFT en repli | `POCKETFFT_NO_VECTORS` et `POCKETFFT_NO_MULTITHREADING` définis ; facteurs de rotation **précalculés** et embarqués ; plans créés à la préparation ; allocation à chaque appel acceptée dans le fil d'analyse ou tampons fournis (MIR-12, MIR-13, DN-20) |
| Vérification des notes | **Filtres ciblés (Goertzel)** sur les partielles des hypothèses | Coût négligeable (≈ 0,2 M opérations par décision, MIR §5) |
| Décimation 48 → 16 kHz | **Filtre à réponse impulsionnelle finie en table**, retard < 1 ms | Déterministe (MIR-14) |
| Rééchantillonnage 48 → 22,05 kHz et appareils à 44,1 kHz | Rééchantillonneur d'Oboe (Apache-2.0, utilisable hors d'Oboe) | Coefficients calculés par `sinf` : précalculés pour l'identité au bit près (MIR-12) |
| Fichiers WAV et FLAC (banc, injection) | dr_wav / dr_flac (Unlicense OR MIT-0) | Variante interne et banc seulement |
| Fonctions mathématiques | Fonctions maison déterministes ou tables dans les chemins de décision ; jamais `cos`, `exp`, `log`, `pow` de la bibliothèque standard | MIR-12, DN-17 |

## À confirmer par POC-01

Fenêtres, résolutions et représentation de l'étage différé.
