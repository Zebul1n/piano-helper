# Piano Helper

Application Android libre d'aide à l'apprentissage du piano : importer sa partition, la voir et l'écouter, puis la jouer sur son propre piano et être corrigé. L'application écoute le jeu au micro (piano acoustique) ou en MIDI (piano numérique) et montre où sont les erreurs.

> **État du projet : conception.** La documentation (besoin, produit, architecture) est rédigée ; les preuves de concept et le développement n'ont pas commencé. Aucune version n'est encore diffusée.

## Principes

- **La partition de l'utilisateur d'abord** : l'application s'applique au morceau qu'il travaille réellement.
- **Fiable avant d'être riche** : une fausse erreur signalée coûte plus cher qu'une erreur non signalée.
- **Local d'abord** : aucune donnée ne quitte le téléphone ; le son du micro est analysé en mémoire et n'est jamais enregistré.
- **Libre et gratuit** : logiciel libre, construit uniquement avec des composants open source.

## Documentation

| Document | Contenu |
|---|---|
| [EDB — Expression de besoin](docs/EDB/README.md) | Besoin, personas, exigences, règles d'analyse du jeu |
| [PRD — Exigences produit](docs/PRD/README.md) | Epics, user stories, plan de réalisation, décisions |
| [DAT — Architecture technique](docs/DAT/README.md) | Architecture, décisions d'architecture (ADR), preuves de concept |
| [DevOps](docs/DEVOPS/README.md) | Installation du poste de développement |

Point d'entrée : [docs/README.md](docs/README.md).

## Plateformes

Android 13 (API 33) et versions ultérieures, téléphones 64 bits. Une version iOS est envisagée plus tard.

## Licences

- **Code** : [GNU GPL v3.0 ou ultérieure](LICENSE) (`GPL-3.0-or-later`).
- **Documentation** : [Creative Commons BY-SA 4.0](LICENSES/CC-BY-SA-4.0.txt) (`CC-BY-SA-4.0`).

Le dépôt suit la spécification [REUSE](https://reuse.software) : la licence de chaque fichier est déclarée dans [REUSE.toml](REUSE.toml) ou dans son en-tête.

© 2026 Ronan Launay et les contributeurs.
