# ADR-0009 — Synthèse : TinySoundFont et banque SF2 libre

| Statut | Date | Exigences |
|---|---|---|
| Proposée — à confirmer par POC-02 (et par l'écoute du porteur, US-LEC-01 CA5) | 2026-11-07, révisée le 2026-11-08 | EF-LEC-01, EF-LEC-03, EF-LEC-05, ENF-PERF-03, ENF-PERF-10, ENF-OFF-01, ENF-RES-03 |

## Contexte

Son de piano hors ligne, tempo variable sans changement de hauteur, banque légère libre (QO-10) ; synthèse dans le rappel de sortie, sans allocation.

## Options étudiées

| Option | Licence | Pour | Contre |
|---|---|---|---|
| A — TinySoundFont (dernier commit 2026-07-19) | MIT | Un en-tête C ; `tsf_set_max_voices` préalloue les voix | Pas de réverbération ; alloue par `malloc`/`realloc` (`TSF_MALLOC`) si les voix manquent ; mode multi-fils « not tested much » [VÉRIFIÉ par `dev-natif-cpp`, `tsf.h`] |
| B — FluidSynth 2.6.2 | LGPL-2.1-or-later | Référence ; plus de glib | Plus gros |
| C — Échantillonneur maison | — | Contrôle | Effort |

Banques : Upright Piano KW (CC0-1.0, variantes small de 2,9 à 6,0 Mio) ; Salamander (CC-BY-3.0, 296 Mio, plus tard).

## Décision proposée

**Option A** :
- **tout appel à TinySoundFont dans le rappel de sortie**, jamais ailleurs (DN-21) ;
- voix préallouées, au moins 256 [ARCH] selon la banque (une note stéréo occupe au moins deux voix ; la pédale augmente la polyphonie) ; épuisement compté et testé sur le Prélude op. 28 n° 4 avec pédale ;
- `TSF_MALLOC` et `TSF_REALLOC` redirigés vers l'allocateur instrumenté, pour que RealtimeSanitizer détecte toute allocation (DN-05) ;
- commandes reçues par une file SPSC par producteur (UI, analyse) (DN-01) ;
- mentions : deux copyrights de la licence (Schelling ; SFZero, Steve Folta) (JUR-D08).

## À confirmer

POC-02 (ENF-PERF-10, ENF-PERF-03), écoute du porteur, test des 88 notes (US-LEC-01 CA4). Repli : B.
