# ADR-0008 — Modèle de partition, format pivot MEI et identifiants structurels

| Statut | Date | Exigences |
|---|---|---|
| Proposée — à confirmer par POC-04 | 2026-11-07, révisée le 2026-11-08 | EF-IMP-01, EF-IMP-02, EF-IMP-12, ENF-FIA-02, ENF-FIA-03, RG-02, RG-03 ; US-IMP-07 |

## Contexte

L'EDB demande un modèle interne unique et propose MusicXML ou MEI comme pivot ([EDB 07 §2](../../EDB/07-contraintes-et-pistes-techniques.md#2-formats-de-partition)). Les identifiants de notes doivent être stables pour relier rendu, analyse et historique (RG-03). Lecture, affichage et analyse doivent voir la même partition.

## Options étudiées

| Option | Pour | Contre |
|---|---|---|
| A — Lecteur MusicXML maison → modèle interne → MEI maison pour Verovio | Indépendance vis-à-vis de Verovio | Écrire et maintenir un lecteur MusicXML complet : très coûteux (exports variables selon les éditeurs) |
| B — **Verovio lit le MusicXML et produit du MEI ; le noyau lit ce MEI**, remplace les identifiants par des identifiants structurels et le garde comme pivot | Un seul interprète du MusicXML pour l'affichage et l'analyse ; pivot ouvert relu tel quel par Verovio | Dépend de l'interprétation de Verovio ; lecteur MEI (sous-ensemble) à écrire |
| C — Pivot MusicXML (fichier source) relu à chaque ouverture | Pas de format de plus | Identifiants de Verovio non stables d'une version à l'autre ; relecture coûteuse |

## Décision proposée

**Option B.** Verovio ne tourne que dans le processus d'import ([ADR-0023](0023-deux-bibliotheques-natives.md)) ; le processus principal relit le MEI pivot avec le lecteur MEI du noyau. Le fichier source est toujours conservé (ENF-FIA-02) ; le MEI pivot porte une version de modèle ; les identifiants sont **structurels** (`n.P1.m012.s1.v1.e03.c0`, occurrence `~2`), indépendants de la version de Verovio ([07 §5](../07-import-et-rendu-de-partition.md#5-identifiants-stables-des-notes)) ; Verovio les transmet au contexte de dessin (`gId`), donc à la liste d'affichage ([ADR-0007](0007-affichage-liste-d-affichage-compose.md)). Le MIDI, que Verovio ne lit pas, passe par un lecteur maison qui écrit du MEI puis suit le même chemin.

Le **rapport d'import** compare des comptages faits par pugixml sur le MusicXML d'origine à ceux du modèle : c'est l'analyseur indépendant demandé par US-IMP-01 CA4.

## À confirmer par POC-04

Fidélité de la lecture MusicXML de Verovio sur les sous-corpus « éditeurs » et « tout-venant » (US-IMP-01 CA2 et CA3) ; complétude du MEI pour l'analyse (liaisons, voix, petites notes, arpèges, indications d'octave).

## Conséquences

- Mise à jour de Verovio = mise à jour du moteur d'import : nouvelle `version_import`, réimport possible depuis la source (US-IMP-07 CA2), séances antérieures marquées (RG-02).
- MusicXML organisé par mesures (`score-timewise`) refusé au MVP avec un message (permis par US-IMP-01 CA1).
