# 01 — Installation du poste de développement (Windows 11)

Guide pas à pas pour préparer le poste du porteur avant I0, POC-01 et POC-05. Il met en œuvre la liste de prérequis du [DAT 10 §7](../DAT/10-build-ci-et-distribution.md#7-prérequis-du-poste-du-porteur) (décision QO-48).

**État constaté le 2026-11-08 :** Git 2.53 et winget présents ; virtualisation activée dans le BIOS ; WSL et Android Studio absents.

Temps estimé : 1 h à 1 h 30, dont beaucoup de téléchargements. Espace disque : environ 20 Go.

---

## Étape 1 — Activer WSL 2 et l'hyperviseur Windows (≈ 15 min, un redémarrage)

Ouvrir **PowerShell en administrateur** (clic droit sur le menu Démarrer → « Terminal (administrateur) »), puis :

```powershell
wsl --install -d Ubuntu-24.04
Enable-WindowsOptionalFeature -Online -FeatureName HypervisorPlatform -All -NoRestart
```

- La première commande active WSL 2 et télécharge Ubuntu 24.04.
- La seconde active la **Plateforme de l'hyperviseur Windows**, qui accélère l'émulateur Android. Elle est équivalente à la case du même nom dans « Activer ou désactiver des fonctionnalités Windows ».

**Redémarrer l'ordinateur.**

Vérification (PowerShell normal) :

```powershell
wsl -l -v
```

Résultat attendu : `Ubuntu-24.04` avec la colonne `VERSION` à `2`.

## Étape 2 — Préparer Ubuntu (≈ 15 min)

Ouvrir **Ubuntu 24.04** depuis le menu Démarrer. Au premier lancement, choisir un nom d'utilisateur et un mot de passe Linux. Ils sont propres à Ubuntu et peuvent différer de ceux de Windows.

Copier ensuite les commandes suivantes :

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential clang-18 lld-18 ninja-build python3-venv pipx git
pipx ensurepath
```

Fermer et rouvrir la fenêtre Ubuntu, puis :

```bash
pipx install cmake==4.1.2
pipx install reuse
```

Vérification :

```bash
clang-18 --version      # doit afficher « clang version 18… »
cmake --version         # doit afficher « cmake version 4.1.2 »
python3 --version       # Python 3.12
reuse --version
```

> Le code du projet restera dans `C:\GIT\piano-helper`. Depuis Ubuntu, ce dossier est accessible sous `/mnt/c/GIT/piano-helper`.

## Étape 3 — Installer Android Studio (≈ 20 min)

Dans PowerShell (normal) :

```powershell
winget install --id Google.AndroidStudio -e
```

Ou bien télécharger l'installeur sur https://developer.android.com/studio.

Au premier lancement, choisir l'installation **Standard**. L'assistant télécharge le SDK et l'émulateur.

**Ne pas installer** le pilote « Android Emulator hypervisor driver (AEHD) », s'il est proposé : son support prend fin le 2026-12-31. L'émulateur utilisera la Plateforme de l'hyperviseur Windows activée à l'étape 1.

## Étape 4 — Compléter le SDK Android (≈ 15 min)

Dans Android Studio : **More Actions → SDK Manager** (ou *Settings → Languages & Frameworks → Android SDK*).

**Onglet « SDK Platforms »** — cocher :
- Android 16 (API 36) ;
- Android 17 (API 37).

**Onglet « SDK Tools »** — cocher **« Show Package Details »** en bas à droite, puis :

| Paquet | Version à cocher |
|---|---|
| Android SDK Build-Tools | 36.0.0 |
| NDK (Side by side) | 30.0.16248370 |
| CMake | 4.1.2 |
| Android Emulator | dernière |
| Android SDK Platform-Tools | dernière |

Cliquer sur **Apply**.

## Étape 5 — Créer les émulateurs (≈ 15 min)

Dans Android Studio : **Device Manager → « + » → Create Virtual Device**. Choisir un téléphone (par exemple *Pixel 8*), puis l'image système. Créer trois émulateurs :

| Usage | Image système |
|---|---|
| Version minimale | API 33, *Google APIs*, x86_64 |
| Version cible | API 36 (ou 37), *Google APIs*, x86_64 |
| Contrôle des pages de 16 Ko | une image dont le nom mentionne *16 KB Page Size* (*Google APIs PS16K*) |

Lancer un émulateur pour vérifier qu'il démarre en moins d'une minute environ. Si le démarrage est très lent, vérifier que l'étape 1 a bien été faite.

## Étape 6 — Connecter le téléphone Android 14 (≈ 5 min)

Sur le téléphone :
1. *Paramètres → À propos du téléphone*, puis appuyer **7 fois** sur « Numéro de build ». Les options pour les développeurs apparaissent.
2. *Paramètres → Système → Options pour les développeurs*, puis activer **Débogage sans fil** (ou **Débogage USB** pour une connexion par câble).

Dans Android Studio, ouvrir **Device Manager → Pair devices using Wi-Fi** et scanner le QR code avec le téléphone. Le téléphone et l'ordinateur doivent être sur le même réseau Wi-Fi.

Vérification, dans un terminal :

```powershell
& "$env:LOCALAPPDATA\Android\Sdk\platform-tools\adb.exe" devices
```

Le téléphone doit apparaître avec l'état `device`.

## Étape 7 — Régler Git (≈ 2 min)

Dans PowerShell ou Git Bash :

```bash
git config --global core.longpaths true
git config --global user.name "Ronan Launay"
git config --global user.email "rlaunay@free.fr"
```

- Les commits du projet seront signés avec `git commit -s` : c'est la mention DCO, prévue au DAT 10.
- L'adresse e-mail apparaîtra publiquement dans l'historique du dépôt. C'est cohérent avec la décision QO-34. GitHub propose aussi une adresse `…@users.noreply.github.com` si tu préfères la masquer dans l'historique.

Faire la même chose dans Ubuntu (étape 2), car WSL a sa propre configuration Git :

```bash
git config --global user.name "Ronan Launay"
git config --global user.email "rlaunay@free.fr"
```

## Étape 8 — Sécuriser le compte GitHub (≈ 5 min)

Sur https://github.com/settings/security, activer la **validation en deux étapes** du compte **Zebul1n**. Elle est requise pour protéger la clé de signature dans la CI (ADR-0017).

---

## Récapitulatif des vérifications

| Vérification | Commande | Attendu |
|---|---|---|
| WSL 2 | `wsl -l -v` | Ubuntu-24.04, version 2 |
| Compilateur | `clang-18 --version` (Ubuntu) | clang 18 |
| CMake | `cmake --version` (Ubuntu) | 4.1.2 |
| Python | `python3 --version` (Ubuntu) | 3.12 |
| REUSE | `reuse --version` (Ubuntu) | affiche une version |
| SDK, NDK | SDK Manager | API 36 et 37, NDK 30.0.16248370, CMake 4.1.2 |
| Émulateur | Device Manager | démarre en moins d'une minute environ |
| Téléphone | `adb devices` | état `device` |

## Inutile d'installer

Python sous Windows, les outils de compilation de Visual Studio, Docker Desktop, LLVM pour Windows : tout passe par Android Studio et WSL.

## En cas de problème

- **`wsl --install` échoue** : vérifier que Windows est à jour (Windows Update), puis relancer la commande.
- **Version de NDK ou de CMake absente** du SDK Manager : noter la version proposée et la signaler. Le DAT fige ces versions pour la reproductibilité (ADR-0016), et `devops-release` devra adapter.
- **Émulateur très lent** : l'hyperviseur n'est pas actif. Refaire l'étape 1 et redémarrer.
