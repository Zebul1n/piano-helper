# Revue du DAT v0.1 — `expert-audio-mir`

| Élément | Valeur |
|---|---|
| Document relu | [DAT v0.1](../README.md) : sommaire, fichiers 01 à 13, ADR-0001 à ADR-0021 |
| Références | [EDB v0.6](../../EDB/README.md) (06, 07, 13), [PRD v0.3](../../PRD/README.md) (03, 05, 09) |
| Point de vue | Traitement du signal, transcription, alignement, évaluation (POC-01, POC-05, banc) |
| Date | 2026-11-08 |
| Auteur | `expert-audio-mir` |

## 1. Verdict

**Prêt avec corrections.**

Le découpage est le bon : un étage temps réel informé par la partition et un étage différé qui fait foi, un banc C++ qui mesure le code embarqué, un son gardé en mémoire seulement. Les licences et les versions que j'ai recontrôlées sont exactes (Basic Pitch 0.4.0 du 2024-08-16, `nmp.onnx` de 230 444 octets, `mir_eval` 0.8.2 sous MIT, pocketfft sous BSD-3-Clause, dr_libs « public domain or MIT-0 », poids de Kong et al. sous CC-BY-4.0 avec un fichier de 171 966 578 octets).

Ce qui doit être corrigé :

1. **Avant de lancer POC-01 (bloquant)** : son plan de mesure ne permet pas de conclure sur les validations indues (≤ 1 %). Il n'y a ni sous-corpus de tentatives fausses, ni effectifs, ni règle de décision tenant compte de l'intervalle de confiance (MIR-07).
2. **Raisonnement sur Basic Pitch** : la conclusion est juste (Basic Pitch ne peut pas servir au mode attente), mais la raison donnée est fausse. La fenêtre de 2 s n'est qu'un découpage d'inférence. Les vraies causes sont l'anticipation de la CQT centrée et celle des convolutions non causales, soit plus de 200 ms (MIR-01). De plus, sa qualité publiée sur le piano est faible (MIR-02).
3. **L'étage temps réel est sous-spécifié** : il doit vérifier les notes attendues par une **mise en concurrence d'hypothèses** sur l'**augmentation** d'énergie après l'attaque, avec une décision asymétrique (MIR-03, proposition au §5).
4. **Définition et mesure d'ENF-PERF-01 au micro** : le point de départ et la latence d'entrée sont ambigus, alors que la marge n'est que de 7 ms (MIR-04 à MIR-06).
5. **Équivalence ordinateur / téléphone (INC-04)** : on peut viser l'**identité au bit près** pour le code maison, à condition de traiter quelques sources de non-déterminisme repérées dans pocketfft, le rééchantillonneur d'Oboe et les options de Clang (MIR-11, MIR-12).

Décompte : **1 bloquante, 12 majeures, 12 mineures** (25 remarques).

Les incohérences INC-01 à INC-10 ne sont pas reprises, sauf INC-04, à laquelle je réponds au §4.4.

**Convention.** **[SOURCE]** : fait lu à la source citée (consultée les 2026-11-07 et 2026-11-08). **[CALCUL]** : valeur calculée par moi à partir de faits sourcés. **[ESTIMATION]** : jugement d'expert non mesuré, à confirmer par POC-01.

## 2. Tableau des remarques

| ID | Gravité | Référence | Constat | Correction proposée | Source |
|---|---|---|---|---|---|
| MIR-01 | Majeur | [05 §3.1](../05-flux-temps-reel-et-performance.md#31-enf-perf-01--mode-attente), [ADR-0010](../adr/0010-transcription-deux-etages.md), [03 §3.5](../03-noyau-natif.md#35-transcription-micro) | « Basic Pitch travaille sur des fenêtres de 2 s, donc ne peut pas servir au mode attente. » La conclusion est juste, la raison non. Les 2 s (`AUDIO_N_SAMPLES` = 43 844 échantillons) sont un découpage d'inférence : le réseau est entièrement convolutif et pourrait tourner sur une fenêtre glissante. La vraie limite est l'**anticipation** (le futur dont le calcul a besoin). (1) La CQT est centrée (rembourrage `n_fft // 2` de chaque côté), avec 36 cases par octave, donc Q ≈ 51,4 : la fenêtre dure Q/f, soit 197 ms à do 4, et le canal de l'harmonique 0,5 lit la case de do 3 (393 ms). Il faut donc environ 100 à 200 ms de son futur. (2) Toutes les convolutions sont en `padding="same"` (non causales). Sur le chemin des attaques, elles s'étendent sur ±1, ±2, ±3, ±3 et ±1 trames, soit environ 10 trames de 11,6 ms (≈ 116 ms) d'après ma lecture de `models.py`. Au total, plus de 200 ms d'anticipation à do 4. (3) Refaire tourner le modèle sur 2 s à chaque pas recalculerait environ 40 fois le même audio. | Remplacer la phrase par : « l'anticipation propre au modèle (CQT centrée, fenêtres de Q/f avec Q ≈ 51 ; convolutions non causales ≈ 10 trames) dépasse 200 ms en médium, plus encore dans le grave ; elle est incompatible avec le budget E4 de 60 ms ». Garder ADR-0010 tel quel sur le fond. | [SOURCE] `basic_pitch/constants.py`, `basic_pitch/models.py`, `basic_pitch/layers/nnaudio.py` (github.com/spotify/basic-pitch) ; [CALCUL] Q = 1/(2^(1/36) − 1) = 51,44 |
| MIR-02 | Majeur | [ADR-0010](../adr/0010-transcription-deux-etages.md), [13 RT-02](../13-risques-techniques-et-questions.md#1-risques-techniques) | La qualité de Basic Pitch sur le piano n'est pas chiffrée dans le DAT. L'article donne, sur le jeu de test de MAESTRO, une F-mesure par note (sans fins de note, tolérance d'attaque de 50 ms) de **70,9 %**, contre **95,2 %** pour Onsets and Frames. Le modèle de Kong et al. atteint 96,72 % de F-mesure sur les attaques. RT-02 est donc sous-estimé. Autre candidat absent du DAT : *Onsets and Velocities* (2023), environ 3,1 M de paramètres, F-mesure sur les attaques de 96,78 % sur MAESTRO, résolution de 24 ms, présenté par l'auteur comme utilisable en temps réel ; code sous GPL-3.0 (fichier LICENSE des dépôts), licence des poids non indiquée, entraîné sur MAESTRO (même situation que Basic Pitch au regard de QO-41). Les modèles de madmom sont sous CC BY-NC-SA 4.0 : à écarter (K3). | Inscrire ces chiffres dans ADR-0010 et passer RT-02 à P = 3. Dans POC-01 : (a) faire de l'**approche A en différé** (vérificateur informé par la partition, non causal, avec contexte de part et d'autre) un candidat **de premier rang**, et non un repli ; (b) ajouter une **approche D**, *Onsets and Velocities*, d'abord sur ordinateur, licence des poids à faire vérifier par `juriste-conformite`. | [SOURCE] arxiv.org/html/2203.09893 ; arxiv.org/abs/2010.01815 ; arxiv.org/abs/2303.04485 ; github.com/andres-fr/iamusica_demo et iamusica_training (LICENSE) ; github.com/CPJKU/madmom (LICENSE) |
| MIR-03 | Majeur | [ADR-0010](../adr/0010-transcription-deux-etages.md), [03 §3.5](../03-noyau-natif.md#35-transcription-micro), [12 POC-01](../12-specifications-techniques-des-poc.md#poc-01--transcription-au-micro) | L'étage temps réel se limite à « gabarits harmoniques des notes attendues + flux spectral ». Rien n'empêche alors : (a) qu'une note qui résonne encore (pédale, note répétée, note tenue) valide l'événement (US-ECO-07 CA3) ; (b) qu'une note jouée une octave **trop bas** valide la note attendue, car toutes les partielles de do 5 sont des partielles paires de do 4 : c'est une validation indue par construction ; (c) que le temps de décision soit adapté à la difficulté du cas. | Spécifier l'étage temps réel comme au §5 : vérification déclenchée par l'attaque, sur l'**augmentation** d'énergie de chaque partielle (avant / après l'attaque) ; **hypothèses concurrentes** (note attendue, ±1 et ±2 demi-tons, ±12, notes qui résonnent) ; test des partielles **impaires** de l'octave inférieure ; décision **asymétrique** : valider vite, signaler une fausse note seulement avec une confiance élevée, sinon attendre. | Principe « informé par la partition » : [EDB 06 §1](../../EDB/06-analyse-du-jeu-et-erreurs.md#1-chaîne-de-traitement) ; Benetos, Klapuri, Dixon, *Score-informed transcription for automatic piano tutoring*, EUSIPCO 2012 (suppression des notes d'octave en trop) |
| MIR-04 | Majeur | [05 §3.1](../05-flux-temps-reel-et-performance.md#31-enf-perf-01--mode-attente) (E3, E4) | (a) **Le budget E4 de 60 ms dépend du registre.** Pour séparer une note de sa voisine sur sa k-ième partielle, il faut une fenêtre L ≥ 33,6 / (k·f0) avec une fenêtre de Hann. À 40 ms, cela suppose k·f0 ≥ 840 Hz : facile dès do 4 (partielles 4 et suivantes), possible en do 3 (partielles 7 et suivantes), incertain sous do 2. Un budget unique masque ce fait. (b) Additionner les 95e centiles de chaque étape surestime le 95e centile du total (E6, l'attente de l'image, est uniforme entre 0 et 16,7 ms). (c) E3 vaut 6 ms pour des blocs de 256 échantillons ; en mode partagé, la taille réelle des rafales peut être plus grande. | Publier le délai de décision **par octave** dans POC-01 et POC-02. Calculer le 95e centile du total par convolution des distributions mesurées, et non par somme des centiles. Garder E3 en paramètre mesuré par POC-02 (taille des rafales effective). Si le grave dépasse le budget : proposer au porteur une cible micro différenciée par registre plutôt que relâcher la cible partout. | [CALCUL] demi-ton = 5,95 % de la fréquence ; lobe principal de Hann = ±2/L |
| MIR-05 | Majeur | [EDB 08](../../EDB/08-exigences-non-fonctionnelles.md) ENF-PERF-01, [05 §5](../05-flux-temps-reel-et-performance.md#5-stratégie-de-mesure) | ENF-PERF-01 se mesure « de l'appui sur la touche ». Sur un piano acoustique, l'enfoncement de la touche jusqu'au fond dure d'environ 25 ms (forte) à 160 ms (piano) avant le choc du marteau. Mesurée depuis le doigt, la cible de 150 ms est hors d'atteinte pour un jeu doux. Le DAT part en fait de l'attaque acoustique (T_attaque), mais sans le dire. En MIDI, le message de note est émis vers la fin de la course : c'est la même convention. | Écrire dans 05 §3 et dans le protocole de US-QUA-04 que le point de départ au micro est **l'attaque acoustique** (choc du marteau sur la corde), équivalente au message de note MIDI. Ajouter une incohérence INC-11 pour l'EDB (« de l'appui sur la touche » → « de l'attaque de la note »). | [SOURCE] Goebl, *Melody lead in piano performance*, JASA 2001, citant Askenfelt et Jansson, *From touch to string vibrations II*, JASA 90(5), 1991 |
| MIR-06 | Majeur | [05 §5.1](../05-flux-temps-reel-et-performance.md#51-instrumentation-interne-variante-interne), [03 §3.3](../03-noyau-natif.md#33-horloges-et-correspondance-des-temps) | Selon la documentation d'Oboe, `getTimestamp` donne l'instant estimé où une trame **entre** dans la chaîne audio, « au mieux de ce que sait l'implémentation ». Dater les attaques avec la correspondance position → temps de l'entrée inclut donc déjà la latence d'entrée connue du système. Deux conséquences : « l'instrumentation interne ne voit pas E2 » est inexact, et ajouter ensuite la latence de captation mesurée (03 §3.3) ou l'E2 tirée de l'aller-retour (05 §5.1) risque de **compter deux fois** la latence. Avec 7 ms de marge, c'est décisif pour la décision de passage. | Retenir **une seule** référence. Dans POC-02, ajouter un test de cohérence : le téléphone émet un clic par son haut-parleur, l'entend par son micro, et l'on compare l'instant de sortie donné par l'horodatage de sortie et l'instant de captation donné par l'horodatage d'entrée. L'écart attendu est d'environ 0,1 ms (quelques centimètres de propagation). S'il est petit, se fier aux horodatages et ne rien ajouter. Sinon, corriger d'un décalage mesuré et ne plus utiliser l'horodatage d'entrée. | [SOURCE] github.com/google/oboe `include/oboe/AudioStream.h` (`getTimestamp(clockid_t)`) |
| MIR-07 | **Bloquant** | [12 POC-01](../12-specifications-techniques-des-poc.md#poc-01--transcription-au-micro) (mesures et corpus), [08 §2](../08-strategie-de-test-et-banc-d-evaluation.md#2-banc-dévaluation) | Une validation indue suppose que l'événement attendu **n'a pas** été joué. Son dénominateur est donc le nombre de **tentatives fausses**, absentes du corpus de POC-01 (les erreurs scénarisées sont « un type par prise »). Sans effectif ni règle de décision, la cible ≤ 1 % est invérifiable. Avec un intervalle de Wilson à 95 %, 0 échec sur 300 tentatives donne une borne haute de 1,26 %, et 0 sur 400 donne 0,95 %. Il en va de même pour les fausses erreurs : 35 sur 1 000 donne [2,5 % ; 4,8 %]. | Ajouter au corpus de POC-01 un **sous-corpus de tentatives fausses** : au moins 400 par mode, réparties en ±1 et ±2 demi-tons, octave en dessus et en dessous, note d'accord oubliée, note voisine en trop. Écrire la **règle de décision de J1** : valeur mesurée ≤ cible et borne haute de l'intervalle de Wilson ≤ 1,5 × cible ; confirmation en I3 sur les 20 séances de US-ECO-07 CA2. Publier tous les résultats par octave. | [CALCUL] intervalles de Wilson ; définitions de l'[EDB 06 §9.2](../../EDB/06-analyse-du-jeu-et-erreurs.md#92-métriques) |
| MIR-08 | Majeur | [08 §2](../08-strategie-de-test-et-banc-d-evaluation.md#2-banc-dévaluation), [12 POC-01](../12-specifications-techniques-des-poc.md#poc-01--transcription-au-micro) (mode attente simulé) | « Chaque tentative enregistrée est soumise seule au détecteur. » Si chaque tentative est découpée sans le son qui la précède, la résonance des notes précédentes (US-ECO-07 CA3) et le cas des notes répétées ne sont pas testés, et le résultat est optimiste. | Soumettre chaque tentative **avec au moins 3 s de son qui la précède** (contexte) et l'état attendu (événement courant, notes tenues). Seule la décision prise après l'attaque de la tentative est évaluée. | [EDB 06 §4.3](../../EDB/06-analyse-du-jeu-et-erreurs.md#43-cas-particuliers) (pédale au micro, notes répétées) |
| MIR-09 | Majeur | [12 POC-01](../12-specifications-techniques-des-poc.md#poc-01--transcription-au-micro) (vérité terrain), US-QUA-01 CA1 | Sans piano numérique, la vérité terrain repose sur 300 attaques annotées deux fois **par la même personne**. L'accord d'une personne avec elle-même sous-estime l'erreur d'annotation, et la charge est lourde pour un porteur seul. Les hauteurs « = partition » ignorent les erreurs involontaires. | Vérité terrain **semi-automatique** (§6.3) : modèle de Kong et al. sur ordinateur, aligné sur la partition, puis vérification humaine des désaccords. Le sous-corpus de 300 attaques annotées à la main sert à **mesurer** l'écart entre Kong et l'annotation manuelle. Si le 95e centile de cet écart est ≤ 10 ms, les attaques de Kong font référence pour le reste. Ajouter une gamme chromatique enregistrée (la 0 à do 8, deux nuances), qui sert à la fois de vérité terrain et de mesure par registre (gabarits, accord étiré). | [SOURCE] zenodo.org/records/4034264 (CC-BY-4.0, 172 Mo) ; arxiv.org/abs/2010.01815 |
| MIR-10 | Majeur | QO-32, [EDB 06 §4.1](../../EDB/06-analyse-du-jeu-et-erreurs.md#41-niveaux-dexigence), [12 POC-01](../12-specifications-techniques-des-poc.md#poc-01--transcription-au-micro) (σ), US-ECO-08 CA4 | La règle « ≥ 2σ » est faisable, mais σ est mal défini. (a) Les erreurs de datation ont une distribution à **queue lourde** (attaques douces, accords, grave) : σ est gonflé par les valeurs extrêmes, ou bien les sous-estime. (b) Il faut retirer la part de l'annotation : σ_dét² = σ_mes² − σ_annot². (c) Un **biais qui dépend du registre** (attaques graves détectées plus tard) n'est pas compensé par le décalage global. (d) Pour la fenêtre d'accord, on compare **deux** attaques : l'écart type vaut √2·σ. (e) Le DAT ne dit pas quel étage est mesuré : le compte-rendu, qui juge le rythme, vient de l'étage **différé**. | Voir le §4.5 : mesurer l'incertitude par registre et pour l'étage différé ; utiliser un σ robuste (1,4826 × écart absolu médian) **et** le quantile u = 97,5e centile de \|e\|, avec T_eff = max(T ; 2σ_robuste ; u) ; W_eff = max(W ; 2√2·σ_robuste) ; corriger le biais par registre. La valeur décisive reste US-ECO-08 CA1 (fausses erreurs de rythme ≤ 5 %). | [CALCUL] ; résolution de Basic Pitch : trames de 11,6 ms (pas de 256 à 22 050 Hz, [SOURCE] `constants.py`) |
| MIR-11 | Majeur | [08 §4](../08-strategie-de-test-et-banc-d-evaluation.md#4-déterminisme-et-équivalence-ordinateur--téléphone), INC-04 | La définition proposée (« différence de décision, ou d'instant > 1 ms ») mélange deux cas. Pour le code maison, l'identité au bit près est **atteignable** (MIR-12) : c'est une exigence plus simple et plus forte. Pour l'inférence (ONNX Runtime, noyaux différents sur x86-64 et arm64), les sorties sont trames par trames : « > 1 ms » revient à « une trame d'écart », et une décision proche d'un seuil peut changer sans anomalie réelle. | Définition à deux niveaux (§4.4) : **niveau 1**, code maison (`dsp`, détecteur, `align`, `analyse`) : sorties identiques au bit près, comparées par empreinte ; tout écart est une anomalie. **Niveau 2**, sorties d'un moteur d'inférence : écart maximal des probabilités ≤ ε (à fixer par POC-01) ; une décision qui change n'est **pas** une anomalie si sa marge au seuil sur l'ordinateur est < δ ; elle est listée. Les instants se comparent en trames. | [SOURCE] ADR-0011 (moteur précompilé) ; [CALCUL] |
| MIR-12 | Majeur | [ADR-0019](../adr/0019-traitement-du-signal.md), [03 §1](../03-noyau-natif.md#1-principes) (déterminisme), C-T3 | Sources de différence entre plateformes trouvées dans le code des bibliothèques retenues. **pocketfft** : facteurs de rotation calculés avec `std::cos` / `std::sin` en double (la bibliothèque mathématique diffère entre glibc, bionic et MSVC) ; vectorisation active par défaut sauf `POCKETFFT_NO_VECTORS` ; fils multiples sauf `POCKETFFT_NO_MULTITHREADING`. **Rééchantillonneur d'Oboe** : coefficients calculés avec `sinf` et un cosinus hyperbolique. **Clang** : `-ffp-contract=on` par défaut, ce qui produit des FMA sur arm64 (le DAT le traite déjà). Restent aussi les modes de mise à zéro des nombres dénormaux (FTZ/DAZ) et les `log` / `exp` utilisés dans les caractéristiques. | Ajouter à ADR-0019 et 03 §1 : (1) **tables précalculées** hors ligne et embarquées comme constantes (fenêtres, facteurs de rotation, filtres) ; (2) dans les chemins de décision, aucune fonction transcendante de la bibliothèque standard : fonctions maison déterministes ou tables ; (3) `POCKETFFT_NO_VECTORS` et `POCKETFFT_NO_MULTITHREADING` définis ; (4) FTZ/DAZ fixés explicitement à l'identique sur l'hôte et sur le téléphone ; (5) test d'empreinte des sorties en CI, et sur le téléphone par `adb`. | [SOURCE] `pocketfft_hdronly.h` (github.com/mreineck/pocketfft, branche `cpp`, l. 139-181 et 348-381) ; `MultiChannelResampler.cpp` (github.com/google/oboe) ; clang.llvm.org/docs/UsersManual.html (`-ffp-contract`) |
| MIR-13 | Mineur | [ADR-0019](../adr/0019-traitement-du-signal.md) | pocketfft **alloue** un tampon de travail à chaque transformée (`arr<T> ch(length)`, donc `malloc`). C'est admissible dans le fil d'analyse (les règles sans allocation ne visent que les rappels audio), mais le temps de calcul devient moins prévisible. | Soit l'accepter explicitement dans ADR-0019, soit choisir une transformée à tampon préalloué (à vérifier pour KissFFT et PFFFT). Pour la vérification des notes, un banc de filtres ciblés (Goertzel) suffit (§5). | [SOURCE] `pocketfft_hdronly.h` (l. 1645 et 2450) |
| MIR-14 | Mineur | [ADR-0004](../adr/0004-moteur-audio-oboe.md), [ADR-0019](../adr/0019-traitement-du-signal.md) | Le rééchantillonneur d'Oboe a au plus 32 coefficients (qualité « Best »). Le passage de 48 à 16 kHz est une décimation entière par 3. La référence Python de Basic Pitch rééchantillonne avec `librosa.load(sr=22050)`, autrement que le C++. | Pour 48 → 16 kHz : un décimateur à réponse impulsionnelle finie, coefficients en table (déterministe, retard < 1 ms). Garder le rééchantillonneur d'Oboe pour 48 → 22,05 kHz et pour les appareils à 44,1 kHz. Pour valider Basic Pitch en C++, donner **le même fichier à 22,05 kHz** aux deux chaînes. | [SOURCE] `MultiChannelResampler.cpp` (2, 4, 8, 16 ou 32 coefficients) ; `basic_pitch/inference.py` |
| MIR-15 | Mineur | [03 §3.5](../03-noyau-natif.md#35-transcription-micro), [03 §5](../03-noyau-natif.md#5-modèle-de-threads), [05 §1](../05-flux-temps-reel-et-performance.md#1-chaîne-de-traitement-en-séance) | `DeferredTranscriber::push(const float* x16k…)` et le schéma (RS 48 → 16 kHz vers DF) supposent 16 kHz, alors que Basic Pitch attend 22,05 kHz. | Paramétrer la fréquence d'entrée du transcripteur différé et corriger le schéma. | [SOURCE] `constants.py` (`AUDIO_SAMPLE_RATE = 22050`) |
| MIR-16 | Majeur | [ADR-0010](../adr/0010-transcription-deux-etages.md), EF-ANA-13, [EDB 06 §8](../../EDB/06-analyse-du-jeu-et-erreurs.md#8-contraintes-temps-réel) | En mode attente, le rôle de l'étage différé n'est pas défini. Si le différé « fait foi », il peut **créer** a posteriori une erreur sur un événement déjà validé à l'écran (« trouvée du premier coup »). C'est une incohérence choquante, contraire au principe « fausse erreur > erreur non signalée ». Faire tourner le modèle pendant de longues attentes coûte aussi de la batterie. | Proposer au PO et au porteur : en mode attente, le compte-rendu reprend les décisions temps réel ; le différé peut seulement **rendre incertaine** une décision (confiance), jamais créer une erreur ; le modèle différé est facultatif dans ce mode (choix selon la mesure de batterie de POC-02). Le différé fait foi en jeu continu. | [PRD 03 US-ANA-09 CA2-CA3](../../PRD/03-user-stories-mvp.md), [EDB 06 §6](../../EDB/06-analyse-du-jeu-et-erreurs.md#6-confiance-et-filtrage) |
| MIR-17 | Mineur | [08 §3](../08-strategie-de-test-et-banc-d-evaluation.md#3-règle-de-non-régression), EDB 06 §9.3, US-QUA-02 CA3 | Le seuil de « + 0,1 point » est acceptable sur le principe, mais ambigu : sur un corpus fixe et une exécution déterministe, + 0,1 point vaut exactement 1 fausse erreur pour 1 000 notes. Sur un sous-corpus de 200 notes, cela revient à une tolérance nulle. Une hausse concentrée dans un sous-corpus (grave, pédale, bruit) peut aussi passer inaperçue dans le total. | Voir le §4.4 : règle exprimée **en effectifs** par mode, avec une garde par sous-corpus et la même règle pour les validations indues. | [CALCUL] |
| MIR-18 | Mineur | [03 §3.5](../03-noyau-natif.md#35-transcription-micro), US-ECO-09 CA2, [06 §2](../06-donnees-et-stockage.md#2-schéma-version-1) (table `calibrage`) | Une tolérance de ± 30 cents, élargie aux extrêmes, approche du demi-écart entre deux demi-tons (50 cents). L'accord étiré d'un piano (courbe de Railsback) atteint plusieurs dizaines de cents aux extrêmes, et les partielles sont décalées vers l'aigu par l'inharmonicité, fₖ = k·f0·√(1 + B·k²). L'écart est surtout sensible dans le grave, sur les partielles élevées. | Modéliser l'inharmonicité B et la déviation d'accord **par registre**, estimées sur la gamme chromatique de POC-01, puis mises à jour pendant les séances à partir des notes justes alignées. Stocker ce profil dans `calibrage`. Avec des hypothèses concurrentes (MIR-03), la tolérance en cents devient moins critique. | [SOURCE] en.wikipedia.org « Piano acoustics » (inharmonicité, courbe de Railsback) ; Fletcher, JASA 1964 (formule des cordes raides) |
| MIR-19 | Mineur | US-ECO-07 CA5-CA6 (question posée à `expert-audio-mir`), [03 §3.5](../03-noyau-natif.md#35-transcription-micro) | Réponse : le CDD d'Android 14 n'exige de la source `UNPROCESSED` qu'une réponse ±10 dB de 100 Hz à 7 kHz, et ±20 dB **sous 100 Hz**. La source est en outre **facultative** (la propriété peut valoir `null`). Sous sol 2 (98 Hz), la fondamentale peut donc manquer et l'octave devient ambiguë. À 16 kHz (limite de 8 kHz), une note au-dessus de do 7 (2 093 Hz) n'a plus qu'une à trois partielles. | Garder les bornes « sous la 1, au-dessus de do 7 » pour la confiance réduite. Exiger le **seuil d'octave renforcé** dès **sol 2** et en dessous. Fixer les valeurs définitives à partir des résultats par octave de POC-01. | [SOURCE] source.android.com, CDD Android 14, §5.11 (C-1-2, C-1-3, C-2-1) |
| MIR-20 | Mineur | [03 §3.5](../03-noyau-natif.md#35-transcription-micro) (`declareAppSound`), US-ECO-11 | L'exclusion des sons de l'application n'est pas décrite. Or l'application connaît exactement le signal qu'elle émet. | Masquer les partielles des notes émises (et les instants des clics), avec une confiance réduite pendant qu'elles sonnent. Option à évaluer : soustraction d'un écho estimé par un filtre adaptatif dont la référence est le signal de sortie. Mesure sur le sous-corpus « métronome par le haut-parleur » (US-ECO-11 CA4). | [EDB 07 §6.2](../../EDB/07-contraintes-et-pistes-techniques.md#62-points-critiques) |
| MIR-21 | Mineur | [05 §5.1](../05-flux-temps-reel-et-performance.md#51-instrumentation-interne-variante-interne) | Une détection hors ligne de l'attaque « précise à l'échantillon » n'existe pas : le début d'une attaque de piano est ambigu de quelques millisecondes. | Définir la référence (par exemple l'instant de l'attaque donné par Kong et al., ou le premier dépassement de X dB au-dessus du bruit de fond) et indiquer son incertitude dans le rapport. | [ESTIMATION] |
| MIR-22 | Mineur | [ADR-0011](../adr/0011-moteur-d-inference-onnx-runtime.md) option C, QO-50 | L'option C (inférence écrite à la main) est réaliste pour Basic Pitch : 16 782 paramètres, une CQT et quelques convolutions (le fichier ONNX prend l'audio brut et contient la CQT). Il faut cependant réimplémenter fidèlement la CQT avec sous-échantillonnage précoce. Elle apporterait l'identité au bit près et supprimerait le binaire précompilé. | Chiffrer l'effort de l'option C dans le rapport de POC-01 ; valider ses sorties contre ONNX Runtime à ε près. | [SOURCE] `basic_pitch/inference.py` (entrée `serving_default_input_2:0` de forme (n, 43 844, 1)) ; arxiv 2203.09893 (16 782 paramètres) |
| MIR-23 | Mineur | [12 POC-01](../12-specifications-techniques-des-poc.md#poc-01--transcription-au-micro) (versions transposées de −20 et +24 cents) | Transposer par rééchantillonnage change aussi les durées (environ 1,2 à 1,4 %) et ne reproduit ni un désaccord inégal ni des unissons qui battent. | Recalculer la vérité terrain temporelle avec le même rapport. Indiquer cette limite dans le rapport ; les vrais désaccords seront vérifiés en bêta. | [CALCUL] 2^(24/1200) = 1,0140 |
| MIR-24 | Mineur | [12 POC-01](../12-specifications-techniques-des-poc.md#poc-01--transcription-au-micro) (comparaison avec et sans traitements « voix ») | Les deux préréglages ne peuvent pas être captés sur la même prise : la comparaison porte sur des jeux différents, non appariés. | Enregistrer en alternance des prises courtes et identiques (même pièce, même tempo) et comparer des taux, en le disant dans le rapport. | [ESTIMATION] |
| MIR-25 | Mineur | [03 §3.5](../03-noyau-natif.md#35-transcription-micro) (interface `RealtimeDetector`) | Le détecteur n'émet que des `NoteOnset {pitch, pos, confidence}`. Le suiveur a besoin d'un **verdict par événement attendu** et des preuves de chaque hypothèse. | Ajouter un `EventVerdict {événement, état : Validé / Faux(hauteur) / Incertain / Rien, confiance, preuves par hypothèse, délai}` à côté des attaques inattendues. | — |

## 3. Ce qui est bien fondé

- Deux étages, avec un temps réel **informé par la partition** : c'est la seule voie compatible avec 150 ms au micro (même conclusion après MIR-01).
- Un banc C++ qui exécute **le code embarqué**, des métriques validées une fois contre `mir_eval` 0.8.2 (MIT, version contrôlée sur PyPI), une tolérance d'attaque de 50 ms identique à celle de `mir_eval` et de la littérature.
- Le son reste en mémoire et le différé travaille par fenêtres (ADR-0013). Le calcul de 170 Mo pour 45 min à 16 kHz est exact.
- Des positions musicales en fractions exactes, un temps exprimé en échantillons, `-ffp-contract=off` : ce sont les bonnes bases du déterminisme.
- Kong et al. comme référence haute sur ordinateur seulement ; Onsets and Frames écarté. Je confirme la licence CC-BY-4.0 et la taille de 172 Mo des poids de Kong.

## 4. Réponses aux points demandés par l'architecte

### 4.1 ADR-0010 (transcription en deux étages)

Accord sur la décision. Trois corrections :

1. remplacer l'argument de la fenêtre de 2 s (MIR-01) ;
2. chiffrer la qualité piano de Basic Pitch (70,9 % de F-mesure par note sur MAESTRO) et mettre en concurrence, dès POC-01, un **vérificateur différé informé par la partition** et *Onsets and Velocities* (MIR-02) ;
3. préciser le rôle du différé en mode attente (MIR-16).

L'étage temps réel doit être spécifié (MIR-03, §5).

### 4.2 ADR-0019 (pocketfft, rééchantillonneur, dr_libs)

- **dr_libs** : accord (dr_flac v0.13.4 du 2026-10-31, « public domain or MIT-0 »).
- **pocketfft** : accord, à trois conditions : définir `POCKETFFT_NO_VECTORS` et `POCKETFFT_NO_MULTITHREADING`, précalculer les facteurs de rotation pour l'identité au bit près (MIR-12), accepter ou contourner l'allocation à chaque appel (MIR-13).
- **Rééchantillonneur d'Oboe** : le garder pour 48 → 22,05 kHz, mais faire la décimation entière 48 → 16 kHz du chemin temps réel avec un filtre en table (MIR-14).
- **Représentation** : le chemin temps réel n'a pas besoin d'une CQT. Une transformée de Fourier courte pour la fonction d'attaque, plus des filtres ciblés (Goertzel) sur les partielles des hypothèses, suffisent et coûtent peu (§5).

### 4.3 Budget de 60 ms, marge de 7 ms et Basic Pitch

- **Le raisonnement sur Basic Pitch est faux, la conclusion est juste** (MIR-01). L'anticipation propre au modèle dépasse 200 ms à do 4 [CALCUL d'après le code].
- **Le budget de 60 ms est réaliste en médium et dans l'aigu, incertain dans le grave** (MIR-04). Ma décomposition [ESTIMATION] : détection de l'attaque en 10 à 25 ms (pas de 8 ms, pic local confirmé une trame plus tard) ; fenêtre d'observation de 30 à 50 ms commençant environ 5 ms après l'attaque ; calcul inférieur à 1 ms. La décision tombe donc environ **40 à 60 ms après l'attaque à partir de do 3**. Sous do 2, comptez 60 à 100 ms, sauf validation précoce sur les partielles élevées, à mesurer.
- **La marge de 7 ms dépend surtout de la façon de mesurer** (MIR-05, MIR-06) : point de départ = attaque acoustique, et pas de double comptage de la latence d'entrée. Additionner les 95e centiles est prudent : le 95e centile réel du total sera plus bas (MIR-04 b).

### 4.4 INC-04 (écart ordinateur / téléphone) et tolérance de + 0,1 point

**Définition proposée de l'écart** (remplace « > 1 ms ») :

| Niveau | Périmètre | Règle | Écart = anomalie |
|---|---|---|---|
| 1 | Code maison : `dsp`, détecteur temps réel, vérificateur différé maison, `align`, `analyse` | Sorties **identiques au bit près**, comparées par empreinte du flux d'événements et des résultats. Conditions : MIR-12 (tables précalculées, aucune fonction transcendante de la bibliothèque standard dans les décisions, `-ffp-contract=off`, FTZ/DAZ fixés, pocketfft sans vectorisation ni fils) | Toute différence |
| 2 | Sorties d'un moteur d'inférence (ONNX Runtime) | max \|p_tél − p_ordi\| ≤ ε (ε fixé par POC-01, ordre de grandeur 10⁻⁴ à 10⁻³ [ESTIMATION]) ; décisions et instants comparés **en trames** | Écart au-delà de ε ; ou décision changée alors que la marge au seuil sur l'ordinateur est ≥ δ. Les changements près du seuil (marge < δ) sont **listés, pas bloquants** |

Si POC-01 retient l'option C d'ADR-0011 (inférence maison), tout passe au niveau 1.

**Tolérance de + 0,1 point au micro.** Accord sur le principe : la valeur est assez fine pour détecter une dérive, et le corpus étant fixe, la règle ne mesure pas un bruit d'échantillonnage mais un changement réel. Elle doit être écrite **en effectifs** :

1. par mode (attente, jeu continu), sur le corpus micro agrégé de N notes : **hausse nette des fausses erreurs ≤ ⌊0,001 × N⌋** (0 sous 1 000 notes, 1 de 1 000 à 1 999, etc.) ;
2. **garde par sous-corpus** (grave, aigu, pédale, notes répétées, bruit, à 1,5 m, transposé) : hausse nette ≤ max(2 ; ⌊0,005 × N_s⌋) ; sinon, blocage ;
3. la **même règle** pour les validations indues du mode attente ;
4. dans le rapport, un tableau apparié note par note : nouvelles fausses erreurs (b) et fausses erreurs corrigées (c). Une hausse acceptée par le porteur cite b et c.

À écrire dans [08 §3](../08-strategie-de-test-et-banc-d-evaluation.md#3-règle-de-non-régression), dans EDB 06 §9.3 et dans US-QUA-02 CA3, à la place de « 0,1 point [PO, à valider] ».

### 4.5 Faisabilité de la tolérance rythmique « ≥ 2σ » (QO-32)

**Faisable, avec une définition précise de σ** (MIR-10).

- **Ordre de grandeur [ESTIMATION]** : sur les attaques détectées en médium, l'écart type de datation d'un bon détecteur est de quelques millisecondes. La quantification seule des trames de Basic Pitch (11,6 ms) donne 11,6/√12 ≈ 3,4 ms. 2σ ≤ 50 ms (σ ≤ 25 ms) est donc **probablement tenu en médium**. Il l'est moins dans le grave, dans les accords denses et dans les notes répétées rapides, où apparaissent des valeurs extrêmes (attaques fusionnées, attaques tardives).
- **Statistique proposée** : par registre (au moins trois zones : sous do 3, do 3 à do 6, au-dessus de do 6) et **pour l'étage qui produit le compte-rendu** :
  - biais b_r corrigé par registre ;
  - σ_r = 1,4826 × écart absolu médian (robuste), dont on retire la part de l'annotation (σ_annot ≈ 3,6 ms si l'accord entre annotations est de 10 ms au 95e centile [CALCUL]) ;
  - u_r = 97,5e centile de \|e − b_r\| ;
  - **T_eff = max(T plafonné ; 2σ_r ; u_r)** et **W_eff = max(W ; 2√2·σ_r)**, puisqu'on compare deux attaques ;
  - marque individuelle à 1,5 × T_eff (inchangé). Avec une erreur gaussienne, la part de notes justes marquées à tort ne vaut alors que 0,27 % ; l'emploi de u_r protège contre les queues lourdes.
- **Condition du niveau « exigeant » au micro** : max(2σ ; u) ≤ 50 ms **en médium**, et non sur l'ensemble. Ailleurs, un T_eff plus large s'applique automatiquement.
- **Limite à signaler au pédagogue** : au micro, la mesure de l'asynchronie d'un accord sous 30 ms est peu fiable, car une seule attaque large est souvent observée. Je recommande de ne signaler « accord non synchronisé » au micro qu'au-delà de W_eff, et seulement s'il s'agit d'un décalage **entre mains**.

## 5. Proposition détaillée : étage temps réel (mode attente et retour en direct)

Objectif : décider en ≤ 60 ms après l'attaque (médium), en minimisant d'abord les fausses erreurs, puis les validations indues.

### 5.1 Chaîne

```
48 kHz (Oboe) → décimation ×3 (FIR en table) → 16 kHz
   ├─ A. Détection d'attaques (générique, causale)
   │     TFCT : fenêtre de Hann de 512 (32 ms), pas de 128 (8 ms)
   │     fonction d'attaque : flux spectral log-compressé avec filtre maximum en fréquence (SuperFlux),
   │     + énergie haute fréquence (choc du marteau) ; seuil adaptatif sur le passé ;
   │     pic local confirmé 1 trame plus tard → instant t_a affiné par interpolation
   └─ B. Vérification des hypothèses, déclenchée par t_a (ou en continu si une attaque est attendue)
         banc de filtres ciblés (Goertzel ou TFD glissante) sur les partielles k = 1..K
         des hypothèses, aux fréquences fₖ = k·f0·√(1 + B_r·k²) (diapason et accord étiré calibrés)
```

Coût [CALCUL] : 20 hypothèses × 12 partielles × 800 échantillons ≈ 0,2 M opérations par décision. C'est négligeable et ne justifie ni réseau ni CQT.

### 5.2 Hypothèses mises en concurrence

Pour l'événement attendu E (notes n₁…n_m) :

- **H_att** : les notes de E sont attaquées ;
- **H_voisines** : n ± 1, n ± 2 demi-tons pour chaque note de E (touche voisine, altération) ;
- **H_octave** : n ± 12 ;
- **H_fond** : notes qui résonnent encore (événements précédents, notes tenues, sons de l'application déclarés) ;
- **H_rien** : bruit (pédale, couvercle, voix).

### 5.3 Preuves

1. **Augmentation, pas présence** : pour chaque partielle, ΔEₖ = Eₖ(fenêtre après t_a) − Êₖ(prédiction de la décroissance d'avant t_a). Une note qui résonne ou une note répétée qui n'a pas été frappée ne produisent pas d'augmentation (US-ECO-07 CA3).
2. **Moindres carrés non négatifs sur un petit dictionnaire** : on explique ΔE par des gabarits paramétriques (amplitudes des partielles en k^(−α) par registre, appris sur la gamme chromatique de POC-01) des **seules** hypothèses ci-dessus. On obtient une activation par hypothèse.
3. **Règle d'octave** : on ne déclare n − 12 que si l'on observe de l'énergie sur ses partielles **impaires**, soit (2m+1)·f0(n)/2 ; on ne déclare n que si ses partielles ne sont pas entièrement expliquées par n − 12. C'est ce qui évite la validation indue « octave en dessous ».
4. **Fenêtres croissantes, test séquentiel** : on réévalue à t_a + 24, 32, 40, 48, 64 ms (puis 96 et 128 ms dans le grave). On s'arrête dès que le rapport de vraisemblance franchit un seuil d'acceptation ou de rejet : les cas faciles se décident vite, les cas difficiles plus tard.
5. **Confiance** : régression logistique, calibrée sur le corpus de POC-01, des caractéristiques suivantes : écart d'activation entre H_att et la meilleure hypothèse concurrente, rapport signal sur bruit, registre, nombre de notes simultanées, force de l'attaque.

### 5.4 Décision asymétrique

| Situation | Décision | Délai |
|---|---|---|
| Toutes les notes de E ont une preuve d'attaque dans W_eff (ou sont tenues ou liées), aucune hypothèse concurrente forte | **Validé**, le curseur avance | Dès le seuil d'acceptation (≤ 60 ms visé en médium) |
| Une hypothèse concurrente l'emporte nettement et E est absent, avec une confiance ≥ seuil de rejet (plus haut pour l'octave, et encore plus haut sous sol 2) | **Faux**, signalé | ≤ 150 ms au total, sinon rien |
| Preuves insuffisantes | **Rien**, on attend ; bénéfice du doute de US-ECO-07 CA4 | — |
| Note d'un accord dont toutes les partielles sont partagées avec d'autres notes de l'accord (doublure d'octave) | Non exigée séparément | — |

### 5.5 Ce que POC-01 doit en tirer

Délai de décision par octave ; taux par hypothèse confondue (matrice de confusion H_att / voisines / octave) ; seuils d'acceptation et de rejet ; gabarits et B par registre. Le même vérificateur, **rendu non causal** (fenêtres de part et d'autre, contexte complet), est le candidat « A différé » de MIR-02.

## 6. Proposition détaillée : protocole de POC-01 adapté au matériel réel

Matériel : un téléphone Android 14, le piano droit du porteur, l'ordinateur (WSL 2 selon QO-48), pas de piano numérique, pas de second téléphone.

### 6.1 Phase 0 — captation (1 à 2 jours)

- Application minimale de captation (préfiguration de POC-02) : flux Oboe à 48 kHz mono, préréglage `Unprocessed` si la propriété `PROPERTY_SUPPORT_AUDIO_SOURCE_UNPROCESSED` est présente, sinon `VoiceRecognition`. Elle enregistre en WAV ou FLAC le son, le préréglage obtenu et les couples (position, temps) de `getTimestamp`.
- Relever la propriété `UNPROCESSED` du téléphone et la consigner : si elle est absente, c'est un résultat à part entière (RT-09).
- Test du clic émis par le haut-parleur et capté par le micro (MIR-06).

### 6.2 Phase 1 — enregistrements (environ 4 à 6 h de jeu réparties sur plusieurs jours)

| Lot | Contenu | Volume visé | Sert à |
|---|---|---|---|
| L0 | Bruit de fond de la pièce, 30 s au début de chaque séance ; gamme chromatique de la 0 à do 8, notes d'1 s, sans pédale, en *p* et en *f*, téléphone sur le pupitre et à 1,5 m | 4 × 88 notes | Gabarits, inharmonicité B et accord étiré par registre, σ et délai par octave, bornes de US-ECO-07 CA6 |
| L1 | Mode attente, jeu juste : chaque événement joué puis une pause d'environ 1 s, comme en séance ; 10 pièces (dont des pièces à une main, QO-42), avec et sans pédale | ≥ 1 000 événements | Non-validations, fausses erreurs, délai, résonance (CA3) |
| L2 | Mode attente, **tentatives fausses** scénarisées (fiche) : pour les événements tirés au sort, une tentative fausse puis la bonne ; types ±1 et ±2 demi-tons, octave en dessus et en dessous, note d'accord oubliée, note voisine ajoutée | ≥ 400 tentatives fausses, dont ≥ 80 d'octave | **Validations indues** (MIR-07), règle d'octave |
| L3 | Jeu continu au métronome entendu **dans un écouteur filaire** (pour ne pas polluer le micro), tempo lent, juste puis avec erreurs scénarisées | ≥ 1 000 notes | Jeu continu, σ du différé, QO-29 |
| L4 | Conditions : bruit (fichier CC0 joué par l'ordinateur), jeu trop doux, téléphone à 1,5 m, métronome par le haut-parleur, autre main jouée par l'application, bruits mécaniques (pédale, couvercle, page) ; traitements « voix » en prises alternées (MIR-24) | ≥ 200 notes par condition | US-ECO-07 CA8 et CA10, US-ECO-10, US-ECO-11 |
| L5 | Versions transposées de −20 et +24 cents, calculées à partir de L1 et L3 (MIR-23) | Dérivé | US-ECO-09 CA1 |

Chaque prise suit une fiche (annexe F de PED). Le porteur enregistre seul dans la pièce (US-QUA-05 CA4).

### 6.3 Phase 2 — vérité terrain semi-automatique

1. Hauteurs : partition + fiche de la prise.
2. Kong et al. (ordinateur seulement) transcrit chaque prise ; un alignement partition ↔ transcription (programmation dynamique) associe chaque note attendue à une attaque détectée.
3. **Désaccords** (note attendue sans attaque, attaque non attendue, hauteur différente) : vérifiés à la réécoute par le porteur, sur un spectrogramme ; annotation douteuse retirée (US-QUA-01 CA3).
4. **Sous-corpus manuel** : au moins 300 attaques annotées à la main deux fois, à au moins une semaine d'intervalle. Il mesure (a) l'accord du porteur avec lui-même (son 95e centile doit être ≤ 10 ms) et (b) l'écart entre Kong et l'annotation manuelle. Si (b) est ≤ 10 ms au 95e centile, les attaques de Kong font référence partout ; sinon, la référence temporelle se limite au sous-corpus manuel.
5. Limite écrite dans le rapport : la référence vient d'un seul annotateur et d'un seul modèle.

### 6.4 Phase 3 — approches et exécution

| Approche | Où | Mode |
|---|---|---|
| A temps réel : vérificateur du §5, en C++ | Banc (ordinateur, CI), téléphone par `adb` | Attente, retour en direct |
| A différé : même vérificateur, non causal | Banc, téléphone | Compte-rendu |
| B : Basic Pitch (ONNX) interprété avec la partition | Python (référence), puis C++ | Compte-rendu |
| C : Kong et al. | Ordinateur seulement | Référence haute et vérité terrain |
| D : *Onsets and Velocities* (si la licence des poids le permet) | Ordinateur d'abord | Compte-rendu, éventuellement temps réel |

Le mode attente est simulé avec le contexte qui précède chaque tentative (MIR-08). Les mesures sont faites à paramètres figés **après** un réglage effectué sur une moitié des pièces : on règle sur 5 pièces, on évalue sur les 5 autres, puis on inverse les deux moitiés.

### 6.5 Phase 4 — mesures et règle de décision

| Mesure | Ventilation | Règle de J1 |
|---|---|---|
| Fausses erreurs (attente, continu) | Mode, octave, condition, pédale | Valeur mesurée ≤ 5 % **et** borne haute de Wilson ≤ 7,5 % |
| Validations indues (L2) | Type de tentative fausse, octave | Valeur mesurée ≤ 1 % **et** borne haute de Wilson ≤ 1,5 % |
| Non-validations | Octave | Valeur mesurée ≤ 5 % |
| Délai de décision (de l'attaque de référence à la décision) | Octave ; 95e centile | ≤ 60 ms en médium (do 3 à do 6) ; autres registres publiés |
| Incertitude de datation (b_r, σ_r, u_r) | Registre, étage | Alimente QO-32 (§4.5) |
| Temps de calcul du différé (téléphone) | Approche | 1 min de jeu en ≤ 10 s ; facteur temps réel ≤ 0,2 visé |
| Déterminisme et équivalence | Approche | Niveau 1 ou 2 du §4.4 |
| Taille, mémoire | Approche | ENF-RES-03, ENF-RES-04 |

Le rapport sépare **mesuré** (corpus du porteur), **estimé** (extrapolation à d'autres pianos ou appareils) et **supposé**. Il propose la branche de J1 (continu, attente seulement, une main seulement, échec) et chiffre l'effort d'industrialisation, y compris l'option C d'ADR-0011.

## 7. Prochaines étapes

1. `architecte` : corriger 05 §3.1, ADR-0010, ADR-0019, 08 §3-4 et 12 (MIR-01 à MIR-16) ; ajouter INC-11 (point de départ d'ENF-PERF-01 au micro).
2. `product-owner` : décider le rôle du différé en mode attente (MIR-16) ; reprendre la règle en effectifs dans US-QUA-02 CA3 ; ajouter le lot de tentatives fausses à US-QUA-01.
3. `pedagogue-piano` : valider la limite sur l'asynchronie des accords au micro (§4.5) et les types de tentatives fausses de L2.
4. `juriste-conformite` : licence des poids d'*Onsets and Velocities* ; usage des sorties du modèle de Kong et al. comme annotations.
5. `expert-audio-mir` : prototype Python du vérificateur du §5 sur L0 et L1 dès que la phase 0 est disponible.

## 8. Sources consultées

- Basic Pitch : github.com/spotify/basic-pitch — `basic_pitch/constants.py`, `basic_pitch/models.py`, `basic_pitch/layers/nnaudio.py`, `basic_pitch/inference.py`, `basic_pitch/saved_models/icassp_2022/` (tailles) ; pypi.org/project/basic-pitch (0.4.0, 2024-08-16).
- Bittner et al., *A Lightweight Instrument-Agnostic Model for Polyphonic Note Transcription and Multipitch Estimation*, ICASSP 2022 — arxiv.org/html/2203.09893.
- Kong et al., *High-resolution Piano Transcription with Pedals by Regressing Onsets and Offsets Times* — arxiv.org/abs/2010.01815 ; poids : zenodo.org/records/4034264.
- Fernandez, *Onsets and Velocities: Affordable Real-Time Piano Transcription Using Convolutional Neural Networks*, EUSIPCO 2023 — arxiv.org/abs/2303.04485 ; github.com/andres-fr/iamusica_training, github.com/andres-fr/iamusica_demo (LICENSE GPL-3.0).
- madmom : github.com/CPJKU/madmom (LICENSE : code BSD, modèles CC BY-NC-SA 4.0).
- Böck et Widmer, *Maximum Filter Vibrato Suppression for Onset Detection* (SuperFlux), DAFx 2013 — dafx.de/paper-archive ; github.com/CPJKU/SuperFlux.
- Benetos, Klapuri, Dixon, *Score-informed transcription for automatic piano tutoring*, EUSIPCO 2012 — webspace.eecs.qmul.ac.uk/s.e.dixon/pub/2012/Benetos-Klapuri-Dixon-EUSIPCO2012a.pdf.
- Goebl, *Melody lead in piano performance: expressive device or artifact?*, JASA 110(1), 2001 — iwk.mdw.ac.at/goebl/papers/Goebl_JASA2001_melodyLead.pdf (cite Askenfelt et Jansson, JASA 1990-1991).
- Inharmonicité et courbe de Railsback : Wikipedia « Piano acoustics » ; Fletcher, *Normal vibration frequencies of a stiff piano string*, JASA 36, 1964.
- pocketfft : github.com/mreineck/pocketfft (branche `cpp`, `pocketfft_hdronly.h`, BSD-3-Clause, dernier envoi 2026-10-19).
- Oboe : github.com/google/oboe — `src/flowgraph/resampler/README.md`, `MultiChannelResampler.cpp`, `include/oboe/AudioStream.h`.
- dr_libs : github.com/mackron/dr_libs — `dr_flac.h` v0.13.4 (2026-10-31).
- Android 14 CDD, §5.11 *Capture for Unprocessed* — source.android.com/docs/compatibility/14/android-14-cdd.
- Clang, *Users Manual* (`-ffp-contract`) — clang.llvm.org/docs/UsersManual.html.
- mir_eval 0.8.2 (MIT) — pypi.org/project/mir_eval.

## Suite donnée (architecte, DAT v0.2, 2026-11-08)

| ID | Statut | Où c'est traité |
|---|---|---|
| MIR-01 | Intégrée | [05 §3.1](../05-flux-temps-reel-et-performance.md#31-enf-perf-01--mode-attente), [ADR-0010](../adr/0010-transcription-deux-etages.md) (raisonnement corrigé) |
| MIR-02 | Intégrée | [ADR-0010](../adr/0010-transcription-deux-etages.md), [12 POC-01](../12-specifications-techniques-des-poc.md#poc-01--transcription-au-micro) (approches A différé et D), [13 RT-02](../13-risques-techniques-et-questions.md#1-risques-techniques) (P = 3), [09 §5.3](../09-securite-confidentialite-licences.md#5-inventaire-des-composants-et-compatibilité) |
| MIR-03 | Intégrée | [03 §3.5](../03-noyau-natif.md#35-transcription-micro), [ADR-0010](../adr/0010-transcription-deux-etages.md) |
| MIR-04 | Intégrée ; cible propre au grave décidée par le porteur (QO-55, 2026-11-08) | [05 §3](../05-flux-temps-reel-et-performance.md#3-budgets-de-latence-par-étape) et [§3.5](../05-flux-temps-reel-et-performance.md#35-délai-de-décision-par-registre), [ADR-0024](../adr/0024-mesure-des-latences.md), [14](../14-decisions-a-prendre.md) |
| MIR-05 | Intégrée ; modification de l'EDB proposée (INC-11, MDAT-07) | [ADR-0024](../adr/0024-mesure-des-latences.md), [05 §3](../05-flux-temps-reel-et-performance.md#3-budgets-de-latence-par-étape), [15](../15-modifications-edb-prd-proposees.md) |
| MIR-06 | Intégrée | [ADR-0024](../adr/0024-mesure-des-latences.md), [03 §3.3](../03-noyau-natif.md#33-horloges-et-correspondance-des-temps), [12 POC-02](../12-specifications-techniques-des-poc.md#poc-02--latence-et-captation-sur-lappareil) |
| MIR-07 | Intégrée (bloquant levé) | [12 POC-01](../12-specifications-techniques-des-poc.md#poc-01--transcription-au-micro) (lot L2, règle de Wilson), MDAT-11, MDAT-24 |
| MIR-08 | Intégrée | [08 §2](../08-strategie-de-test-et-banc-d-evaluation.md#2-banc-dévaluation), MDAT-26 |
| MIR-09 | Intégrée ; usage des sorties de Kong et al. à confirmer par JUR | [12 POC-01](../12-specifications-techniques-des-poc.md#poc-01--transcription-au-micro) (phase 2), MDAT-17 |
| MIR-10 | Intégrée ; formule soumise au porteur (précise QO-32) | [12 POC-01](../12-specifications-techniques-des-poc.md#poc-01--transcription-au-micro), MDAT-14, MDAT-28 |
| MIR-11 | Intégrée | [ADR-0022](../adr/0022-reproductibilite-ordinateur-telephone.md), [08 §4](../08-strategie-de-test-et-banc-d-evaluation.md#4-déterminisme-et-équivalence-ordinateur--téléphone), MDAT-23 |
| MIR-12 | Intégrée | [ADR-0022](../adr/0022-reproductibilite-ordinateur-telephone.md), [ADR-0019](../adr/0019-traitement-du-signal.md) |
| MIR-13 | Intégrée | [ADR-0019](../adr/0019-traitement-du-signal.md) |
| MIR-14 | Intégrée | [ADR-0019](../adr/0019-traitement-du-signal.md), [03 §3.5](../03-noyau-natif.md#35-transcription-micro) |
| MIR-15 | Intégrée | [03 §3.5](../03-noyau-natif.md#35-transcription-micro), [05 §1](../05-flux-temps-reel-et-performance.md#1-chaîne-de-traitement-en-séance) |
| MIR-16 | Intégrée : QO-52 décidée le 2026-11-08 (option A) | [14](../14-decisions-a-prendre.md), [ADR-0010](../adr/0010-transcription-deux-etages.md), MDAT-19, MDAT-29 |
| MIR-17 | Intégrée ; modification de l'EDB et du PRD proposée | [08 §3](../08-strategie-de-test-et-banc-d-evaluation.md#3-règle-de-non-régression), MDAT-13, MDAT-22 |
| MIR-18 | Intégrée | [03 §3.5](../03-noyau-natif.md#35-transcription-micro), [06 §2](../06-donnees-et-stockage.md#2-schéma-version-1) |
| MIR-19 | Intégrée | [03 §3.5](../03-noyau-natif.md#35-transcription-micro), [05 §3.5](../05-flux-temps-reel-et-performance.md#35-délai-de-décision-par-registre), MDAT-16, MDAT-27 |
| MIR-20 | Intégrée | [03 §3.5](../03-noyau-natif.md#35-transcription-micro) |
| MIR-21 | Intégrée | [05 §5.1](../05-flux-temps-reel-et-performance.md#51-instrumentation-interne-variante-interne), [ADR-0024](../adr/0024-mesure-des-latences.md) |
| MIR-22 | Intégrée | [ADR-0011](../adr/0011-moteur-d-inference-onnx-runtime.md), [12 POC-01](../12-specifications-techniques-des-poc.md#poc-01--transcription-au-micro) |
| MIR-23 | Intégrée | [12 POC-01](../12-specifications-techniques-des-poc.md#poc-01--transcription-au-micro) (lot L5) |
| MIR-24 | Intégrée | [12 POC-01](../12-specifications-techniques-des-poc.md#poc-01--transcription-au-micro) (lot L4) |
| MIR-25 | Intégrée | [03 §3.5](../03-noyau-natif.md#35-transcription-micro) (`EventVerdict`) |

Bilan : 25 intégrées, 0 rejetée, 0 en attente (MIR-16 intégrée après la décision QO-52 du 2026-11-08). Les propositions des §4 à §6 sont reprises dans le [fichier 03](../03-noyau-natif.md), le [fichier 08](../08-strategie-de-test-et-banc-d-evaluation.md) et le [fichier 12](../12-specifications-techniques-des-poc.md). Arbitrages : ARB-01, ARB-03, ARB-04, ARB-10 ([README](../README.md)).
