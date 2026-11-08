# ADR-0007 — Affichage : liste d'affichage produite par un contexte de dessin du projet, dessinée par Compose

| Statut | Date | Exigences |
|---|---|---|
| Proposée — à confirmer par POC-04 | 2026-11-07, révisée le 2026-11-08 | EF-VIS-02, EF-VIS-03, EF-VIS-04, EF-RES-04, ENF-PERF-05, ENF-PERF-08, ENF-ACC-02 |

## Contexte

Il faut dessiner une bande très longue à 60 images/s, zoomer sans recalcul, superposer un curseur et des marques par identifiant de note (≤ 16 ms). Verovio ne produit qu'une sortie graphique publique, le SVG ; mais `Toolkit::RenderToDeviceContext` est publique (non documentée) et `DeviceContext` est une interface abstraite d'environ 40 méthodes à coordonnées entières, avec `StartGraphic(Object*, gClass, gId)` [VÉRIFIÉ par `dev-natif-cpp` le 2026-11-08, `include/vrv/toolkit.h`, `include/vrv/devicecontext.h`].

## Options étudiées

| Option | Pour | Contre |
|---|---|---|
| A — Convertir le SVG de Verovio en liste d'affichage | Format documenté | Sous-ensemble plus large que prévu (`<use>` avec transformations, rotations, tirets, opacités, `<image>`, **texte SMuFL en `<tspan>`** rendu par une police woff2) ; SVG de plusieurs dizaines de Mo pour P30 [NON VÉRIFIÉ] ; précision perdue ; 1 000 à 1 500 lignes [estimation de `dev-natif-cpp`] ; format non stable |
| **D — `DisplayListDeviceContext`** : Verovio dessine directement dans un contexte du projet qui écrit la liste d'affichage | Pas de texte intermédiaire ; identifiants depuis `gId` ; glyphes SMuFL reçus par `DrawMusicText` et convertis une fois en chemins ; une rupture d'interface se voit **à la compilation** | API non documentée ; couplage à l'interface interne de Verovio (comparable à celui du format SVG) |
| B — AndroidSVG | Lit le SVG | Inactif depuis 2019 |
| C — Vue web | Moteur SVG complet | Mémoire, synchronisation |

## Décision proposée

**Option D** (recommandation de `dev-natif-cpp`, DN-22), comparée à A sur des mesures de POC-04 (temps d'import de P10, pic mémoire de P30, fidélité, lignes de code). Le **SVG reste l'oracle** des tests de captures (US-VIS-01 CA4). Côté Compose (DA-18) : chemins construits une fois, une tuile par `GraphicsLayer` redessinée seulement si son contenu change, défilement et curseur en phase de dessin, coordonnées relatives à la tuile (DN-23). Images de référence des captures produites et comparées en CI seulement (DEV-13).

**Arbitrage** : la v0.1 avait écarté D par crainte du couplage ; `dev-natif-cpp` montre que le couplage existe aussi avec le SVG et qu'il est plus visible avec D. D est retenu comme proposition ; A reste le repli.

## Conséquences

Un test de non-régression du contexte de dessin accompagne chaque mise à jour de Verovio. Le format de la liste d'affichage est indépendant du moteur de dessin (portable vers iOS).
