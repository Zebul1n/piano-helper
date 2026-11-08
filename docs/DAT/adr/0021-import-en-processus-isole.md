# ADR-0021 — Import des fichiers dans un processus séparé

| Statut | Date | Exigences |
|---|---|---|
| Proposée — à confirmer par POC-04 | 2026-11-07, révisée le 2026-11-08 | ENF-SEC-04, ENF-PERF-06, ENF-RES-04, ENF-FIA-01 ; US-IMP-06 CA3 |

## Contexte

Les fichiers importés sont non fiables et lus par du code natif tiers (Verovio, pugixml, miniz). US-IMP-06 CA3 exige que l'application reste utilisable et que la mémoire reste bornée.

## Options étudiées

| Option | Pour | Contre |
|---|---|---|
| A — Import dans le processus principal | Simple | Un plantage natif tue l'application |
| B — **Processus privé** (`android:process=":import"`, service non exporté) | Confine les plantages et le manque de mémoire ; mémoire de Verovio libérée avec le processus | **Même UID** : un code natif compromis lirait les fichiers de l'application ; ce n'est pas un bac à sable [VÉRIFIÉ par `dev-android`, `service-element`] |
| C — **Processus isolé** (`android:isolatedProcess="true"`) | Processus sans permission, entrées et sorties seulement par descripteurs | Plus complexe ; tout passe par `ParcelFileDescriptor` ouverts par le processus principal |

## Décision proposée

**B au MVP**, avec sa limite de sécurité écrite telle quelle (DA-16) ; **C étudié en POC-04**. Mise en œuvre :
- le processus principal copie le fichier reçu dans un **fichier de transit** (comptage des octets, SHA-256), puis passe un descripteur au service (DA-17) ;
- `Application.onCreate` teste `getProcessName()` : dans `:import`, il n'initialise que le service (ni Room, ni ménage) (DA-16) ;
- **minuterie de surveillance dans le processus d'import**, qui se termine lui-même au bout de 5 s ; déliaison côté principal ;
- plafond de la mémoire native (`setrlimit` ou allocateur) à valider en POC-04 sur l'archive de 1 Go ;
- le noyau se borne aussi lui-même (DN-30), car ce processus n'existera pas sur iOS ;
- le principal ne valide l'import (ligne en base, renommage atomique) qu'après un code de succès.

Le processus d'import charge `libpianoimport.so` ([ADR-0023](0023-deux-bibliotheques-natives.md)).

## À confirmer par POC-04

Surcoût de démarrage (≤ 300 ms [ARCH]), comportement sur les fichiers piégés, faisabilité de C.
