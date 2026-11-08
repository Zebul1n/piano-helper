# 11 — Portabilité iOS

iOS est hors périmètre initial (ENF-COMP-01, W) ; le noyau métier doit rester portable ([EDB 07 §14.1](../EDB/07-contraintes-et-pistes-techniques.md#141-android-dabord-ios-à-terme)). Ce fichier liste ce qui est conçu **dès maintenant** pour que le portage ne remette pas en cause l'architecture, et ce qui reste à décider plus tard.

## 1. Ce qui est portable dès le MVP

| Élément | Pourquoi c'est portable | Ce qu'il faudra écrire pour iOS |
|---|---|---|
| Noyau `core` (C++20) et façade C `core/capi` | Aucune dépendance Android ; interfaces de plateforme `AudioIO`, `MidiInput`, `Clock`, `Logger`, `FileAccess`, `ThreadControl` ([03 §2](03-noyau-natif.md#2-découpage-et-dépendances)) ; la façade C sert aussi à Swift ou à Kotlin/Native (DN-15) ; construit pour l'hôte en CI | `platform/ios` : entrée et sortie audio (RemoteIO traite entrée et sortie **dans un seul rappel** : le noyau ne suppose pas deux fils, DN-31), CoreMIDI, `Clock` en temps hôte (`mach_absolute_time`) converti en nanosecondes, `ThreadControl` par groupe de travail audio (`os_workgroup`) |
| Verovio, TinySoundFont, pocketfft, pugixml, miniz, nlohmann/json | C/C++ portables | Rien |
| ONNX Runtime | Paquets iOS officiels (CocoaPods `onnxruntime-c`, dernière version publiée 1.30.0) [VÉRIFIÉ 2026-11-07, trunk.cocoapods.org] | Intégration ; Core ML possible plus tard |
| Basic Pitch | Le dépôt fournit aussi un modèle Core ML [VÉRIFIÉ 2026-11-07] | — |
| Paramètres, corpus, banc | Indépendants de la plateforme | Rejouer le corpus micro sur un iPhone |
| Format pivot MEI, identifiants structurels | Fichiers ordinaires | — |
| Liste d'affichage binaire | Format du noyau, indépendant du moteur de dessin | Un moteur de dessin (Core Graphics, SwiftUI Canvas ou Compose Multiplatform) |

## 2. Ce qui est préparé côté Kotlin

- `:domain` et `:data` en Kotlin sans dépendance d'interface Android ([04 §2](04-application-android.md#2-couches-et-modules)) ; Room prend en charge Kotlin Multiplatform (Android, iOS, JVM) depuis la version 2.7.0 [VÉRIFIÉ 2026-11-07, developer.android.com/jetpack/androidx/releases/room].
- Compose Multiplatform 1.12.2 (Apache-2.0) : la cible iOS est **stable** depuis la version 1.8.0 (mai 2025) [VÉRIFIÉ 2026-11-07, blog JetBrains et kotlinlang.org].
- Textes dans des ressources séparées du code, codes du noyau traduits côté application.

## 3. Options d'interface à évaluer au moment du portage

| Option | Pour | Contre |
|---|---|---|
| A — Compose Multiplatform + Kotlin Multiplatform (partage de l'interface et des couches `domain` / `data`) | Une seule base de code d'interface ; équipe réduite | Pont Kotlin/Native ↔ C++ à construire (cinterop) ; maturité à réévaluer |
| B — SwiftUI + noyau C++ (interopérabilité Swift / C++) | Interface native iOS | Deux interfaces à maintenir pour un porteur seul |
| C — A pour les écrans courants, vue native pour la partition | Compromis | Complexité |

Pas de décision maintenant : le choix sera fait par une ADR au lancement du portage, au vu de la maturité des outils à ce moment-là. Le DAT évite seulement ce qui fermerait l'option A (aucune logique métier dans les écrans Compose).

## 4. Obstacles connus

| Obstacle | Nature | Qui |
|---|---|---|
| Un Mac est nécessaire pour compiler pour iOS ; l'App Store exige l'adhésion payante au programme développeur Apple | Budget nul ([EDB 07 §14.1](../EDB/07-contraintes-et-pistes-techniques.md#141-android-dabord-ios-à-terme)) | Porteur |
| Distribution d'une application **GPL-3.0** et d'une bibliothèque **LGPL-3.0-only** (Verovio) sur l'App Store : la FSF a jugé ses règles incompatibles avec la GPL en 2010 (retrait de VLC en 2011) ; la licence propre au développeur atténue la question sans la régler ; la licence type interdit l'ingénierie inverse, ce qui heurte la LGPL §4 ; le risque vient des titulaires tiers (contributeurs, auteurs de Verovio) (JUR-D11) | Juridique ([EDB 11 §3](../EDB/11-juridique-et-conformite.md#3-licences-logicielles-et-contenus)) | `juriste-conformite`, avocat |
| Audio : catégorie et mode de session `AVAudioSession`, traitement de la voix à désactiver (mode « measurement ») | Technique | `dev-natif-cpp` |
| Mesures de latence et corpus micro à refaire sur iPhone | Matériel | Testeurs |
| Pas de processus auxiliaire d'import sur iOS [NON VÉRIFIÉ] : la robustesse face aux fichiers piégés repose sur les bornes du noyau lui-même (mémoire comptée, annulation coopérative, aucun `abort`), prévues dès le MVP (DN-30) | Technique | `dev-natif-cpp` |
| Permission supplémentaire (GPL-3.0 §7) autorisant la diffusion par les boutiques d'applications : non ajoutée pour l'instant, question à reposer **avant la première contribution extérieure**, sinon il faudra l'accord de chaque contributeur (JUR-D11) | Juridique | Porteur : décision QO-51 du 2026-11-08, option B (rien n'est ajouté pour l'instant ; question reposée avant la première contribution extérieure) |
| Au lancement du portage : demander à RISM une autorisation écrite pour Verovio sur l'App Store, utiliser une licence propre au développeur (texte de la GPL) à la place de la licence type d'Apple, avis d'avocat ; Verovio fournit lui-même `Package.swift` et `Verovio.podspec`, ce qui n'est pas une autorisation (JUR-D11) | Juridique | Porteur, `juriste-conformite` |
