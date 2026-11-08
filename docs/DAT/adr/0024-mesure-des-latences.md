# ADR-0024 — Mesure des latences : point de départ et référence temporelle unique

| Statut | Date | Exigences |
|---|---|---|
| Proposée — à confirmer par POC-02 | 2026-11-08 | ENF-PERF-01, ENF-PERF-02, ENF-PERF-03 ; EF-ECO-06 ; US-QUA-04 CA1 ; INC-11 |

## Contexte

La marge du budget au micro est faible ([05 §3.1](../05-flux-temps-reel-et-performance.md#31-enf-perf-01--mode-attente)). Deux ambiguïtés pèsent sur la décision de J1 :
1. **Point de départ** (MIR-05) : l'EDB mesure ENF-PERF-01 « de l'appui sur la touche ». Sur un piano acoustique, l'enfoncement jusqu'au choc du marteau dure d'environ 25 ms (forte) à 160 ms (piano) [source citée par `expert-audio-mir` : Askenfelt et Jansson, JASA 1991] ; mesurée depuis le doigt, la cible est hors d'atteinte pour un jeu doux. En MIDI, le message est émis vers la fin de la course.
2. **Double comptage** (MIR-06) : l'horodatage d'entrée d'AAudio donne l'instant où une trame entre dans la chaîne audio, latence d'entrée connue du système comprise. Lui ajouter la latence de captation mesurée au réglage, ou la latence d'entrée tirée d'un aller-retour, compte deux fois la même latence.

## Décision proposée

- **Point de départ** : au micro, l'**attaque acoustique** ; en MIDI, l'émission du message de note. Modification de l'EDB proposée (INC-11, [fichier 15](../15-modifications-edb-prd-proposees.md)).
- **Référence temporelle unique** : les horodatages de la plateforme. Test de cohérence dans POC-02 : le téléphone émet un clic par son haut-parleur et le capte par son micro ; on compare l'instant de sortie (horodatage de sortie) et l'instant de captation (horodatage d'entrée). Écart attendu : environ 0,1 ms. S'il est petit, on se fie aux horodatages et l'on n'ajoute rien ; sinon, on corrige d'un décalage mesuré au réglage du micro et l'on cesse d'utiliser l'horodatage d'entrée.
- **Statistique** : la décision se prend sur le 95e centile **du total mesuré** (ou de la convolution des distributions), jamais sur la somme des centiles des étapes ; résultats publiés par octave (MIR-04).
- **Attaque de référence** (MIR-21) : définie dans le protocole (instant du modèle de Kong et al., ou premier dépassement d'un seuil au-dessus du bruit) avec son incertitude.

## À confirmer par POC-02

Résultat du test de cohérence ; s'il échoue, la table `calibrage` porte une correction de latence ([06 §2](../06-donnees-et-stockage.md#2-schéma-version-1)).
