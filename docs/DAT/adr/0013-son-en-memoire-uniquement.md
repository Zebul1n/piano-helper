# ADR-0013 — Le son de la séance reste en mémoire vive

| Statut | Date | Exigences |
|---|---|---|
| Acceptée (formulation révisée en v0.2) | 2026-11-07, révisée le 2026-11-08 | ENF-CONF-01, ENF-SEC-06, EF-ECO-05, EF-ANA-13 ; US-ECO-03 §8.1, US-ECO-04 CA4, US-ANA-02 CA4 ; JUR-06 ; D8 |

## Contexte

JUR-06 demandait où vit le son pendant la séance, pour que le texte de l'écran d'autorisation soit exact (« gardé en mémoire le temps de la séance, puis effacé ; jamais enregistré dans un fichier ni envoyé »). L'EDB 07 §7.3 évoquait une transcription « sur l'enregistrement de la séance » (INC-05).

## Options étudiées

| Option | Pour | Contre |
|---|---|---|
| A — Fichier temporaire supprimé après le compte-rendu | Mémoire faible | Texte de l'écran faux ; fichier résiduel possible |
| B — Toute la séance en mémoire, puis analyse | Pas de fichier | ≈ 170 Mo pour 45 min à 16 kHz : contraire à ENF-RES-04 |
| C — **Traitement en flux** : anneau d'entrée borné, différé par fenêtres pendant le jeu, seules les sorties sont gardées | Pas de fichier ; mémoire bornée ; compte-rendu rapide | Le différé doit suivre le rythme du jeu |

## Décision

**Option C.**

- **L'application n'écrit jamais le son dans un fichier** dans les variantes bêta et publique : le code natif de l'enregistreur et de l'injection de fichiers n'est compilé que dans la variante interne (`PIANO_INTERNAL_TOOLS`), vérifié par l'absence de leurs symboles dans les `.so` diffusés (DA-23).
- En mémoire : anneau d'entrée de quelques secondes ; fenêtre en cours du différé ; tampons **préalloués, réutilisés et effacés** par une écriture que l'optimiseur ne peut pas supprimer, à la fin de l'analyse différée et de la séance ; aucune copie du son dans les messages d'erreur ni les journaux (JUR-D10).
- Arrêt forcé : la mémoire du processus disparaît ; l'application ne laisse aucun son dans le stockage (US-ECO-04 CA4).

**Limite connue** (JUR-D10) : sur certains appareils, **le système** peut placer temporairement des pages de mémoire sur un espace de stockage **chiffré et effacé à chaque démarrage** (réécriture de zram dans `/data/per_boot`) [VÉRIFIÉ par `juriste-conformite`, `init.rc` d'AOSP]. L'application n'y a pas accès et ne crée aucun fichier ; le texte « jamais enregistré dans un fichier » reste exact et loyal [interprétation de JUR]. La formule de la v0.1 (« le son n'est jamais écrit dans le stockage ») allait trop loin.

## Conséquences

- Le texte de l'écran d'autorisation (US-ECO-03 §8.1) est gardé tel quel (avis de JUR).
- La réécoute d'une séance (EF-ECO-09, V1) demandera une option explicite et une nouvelle ADR.
