# ADR-0018 — Conformité des licences : REUSE, inventaire, écran des licences

| Statut | Date | Exigences |
|---|---|---|
| Acceptée (révisée en v0.2) | 2026-11-07, révisée le 2026-11-08 | US-CNF-02, US-CNF-03, US-CNF-04 ; D7 ; JUR-01, JUR-14, JUR-15 ; [EDB 11 §3](../../EDB/11-juridique-et-conformite.md#3-licences-logicielles-et-contenus) |

## Contexte

Chaque APK, dès I0, doit contenir les textes intégraux des licences, les NOTICE et les attributions ; chaque fichier du dépôt doit avoir une licence connue ; une nomenclature accompagne chaque diffusion ; les outils doivent être libres.

## Décision

| Besoin | Retenu |
|---|---|
| Licence de chaque fichier | REUSE 3.3 ; `reuse lint` en CI ; annotations de `third_party/verovio/**` en LGPL-3.0-only et des composants inclus, `precedence = "closest"` (JUR-D05) |
| Contrôle des dépendances Gradle | Licensee 1.14.1 ; **angle mort** : il ne voit pas le contenu natif d'un AAR, que l'inventaire couvre à la main (JUR-D02) |
| Écran des licences | AboutLibraries 15.3.0 avec **`offlineMode = true` activé explicitement** (JUR-D09) + entrées manuelles tirées de `REUSE.toml` ; colonne « Mention à reproduire » de l'inventaire reprise mot pour mot (JUR-D08) |
| Verovio (LGPL-3.0 §4 a) à c)) | Avis visible « Ce programme utilise Verovio, © les auteurs de Verovio (RISM Digital Center), sous licence LGPL-3.0-only » ; textes GPL-3.0 et LGPL-3.0 hors ligne ; `LICENSES/LGPL-3.0-only.txt` (JUR-D04) |
| Polices | `LICENSE.txt` et `FONTLOG.txt` de Leipzig et Bravura ; licence OFL de la police de texte (JUR-D06, JUR-D07) |
| Nomenclature | **Obligatoire : `reuse spdx`** (tous les fichiers, `third_party/` compris) ; CycloneDX facultatif et non bloquant (DEV-05) |

## Conséquences

`reuse` (Python) tourne en CI par son action ; localement dans WSL 2 par `pipx` (décision QO-48). L'inventaire ([09 §5](../09-securite-confidentialite-licences.md#5-inventaire-des-composants-et-compatibilité)) est relu par JUR et validé par le porteur avant chaque diffusion.
