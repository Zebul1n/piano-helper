# ADR-0001 — Découpage : application Android, noyau C++ portable, outils hors application

| Statut | Date | Exigences |
|---|---|---|
| Acceptée | 2026-11-07 | ENF-COMP-01, ENF-MAINT-01, ENF-QA-02, ENF-PERF-01, ENF-OFF-01 |

## Contexte

Le noyau métier doit être portable vers iOS (ENF-COMP-01), testable sans interface sur un ordinateur et en CI (ENF-MAINT-01, ENF-QA-02), et le traitement audio doit tourner en natif ([EDB 07 §1](../../EDB/07-contraintes-et-pistes-techniques.md#1-contraintes-structurantes), C2). L'EDB recommande déjà un noyau natif partagé ([EDB 07 §10](../../EDB/07-contraintes-et-pistes-techniques.md#10-technologie-de-lapplication)).

## Options étudiées

| Option | Pour | Contre |
|---|---|---|
| A — Tout en Kotlin (y compris l'analyse) | Une seule langue | Temps réel audio difficile en machine virtuelle ; pas portable vers iOS sans Kotlin/Native ; Verovio et Oboe sont en C++ |
| B — Noyau C++ + interface Kotlin/Compose native | Audio, gravure et analyse partagés ; interface native ; banc sur le même code | Pont JNI à soigner |
| C — Noyau C++ + interface multiplateforme dès maintenant (Compose Multiplatform) | Interface partagée plus tard | Effort et risques immédiats sans bénéfice au MVP (pas d'iOS) |
| D — Interface web embarquée | Réutilise des moteurs JavaScript | Performance, intégration audio, pas de rendu natif |

## Décision

**Option B.** Trois ensembles :

1. **Application Android** en Kotlin et Jetpack Compose : écrans, état, persistance, autorisations, cycle de vie ;
2. **Noyau `core` en C++** : tout le métier ; aucune dépendance Android ; la plateforme est vue par des interfaces (`AudioIO`, `MidiInput`, `Clock`, `Logger`, `FileAccess`) ;
3. **Outils hors application** : banc d'évaluation (C++, même code que le noyau), corpus, scripts (Node.js), prototypes (Python dans WSL 2, décision QO-48).

Les couches `domain` et `data` de l'application restent sans dépendance d'interface pour ne pas fermer l'option C au portage iOS ([11](../11-portabilite-ios.md)).

## Conséquences

- Le banc mesure exactement le code embarqué (C-T2).
- Deux langues principales (Kotlin, C++) : conforme à K6.
- Le pont JNI devient un point de conception ([ADR-0003](0003-pont-jni-et-echanges.md)).
- Contrôle : la cible `core` se construit pour l'hôte Linux en CI sans en-tête Android.
