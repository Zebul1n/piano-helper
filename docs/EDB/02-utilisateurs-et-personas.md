# 02 — Utilisateurs et personas

## 1. Cibles

| Segment | Description | Priorité |
|---|---|---|
| Adulte débutant autodidacte | Apprend seul, avec méthode, tutoriels ou partitions simplifiées. | **Primaire** |
| Élève en cours (enfant ou adulte) | Suit des cours (conservatoire, école de musique, professeur particulier), travaille seul entre deux cours. | **Primaire** |
| Pianiste qui reprend | A joué plus jeune, reprend après des années, niveau intermédiaire, lit encore la musique. | Secondaire |
| Parent d'un élève mineur | N'est pas forcément musicien, veut accompagner et suivre le travail de l'enfant. | Secondaire |
| Professeur de piano | Prescripteur potentiel, pourrait suivre ses élèves à distance. | Tertiaire (V2) |

## 2. Personas

### 2.1 Claire — l'adulte autodidacte

| | |
|---|---|
| Âge | 38 ans |
| Instrument | Piano numérique avec sortie USB |
| Niveau | Débutante, 8 mois de pratique |
| Lecture | Lit lentement la clé de sol, difficilement la clé de fa |
| Équipement | Smartphone Android, tablette Android |
| Temps de pratique | 20 à 30 minutes, 4 à 5 soirs par semaine |

**Contexte.** Claire apprend avec une méthode papier et des partitions trouvées en ligne. Elle n'a pas de professeur.

**Frustrations.**
- Ne sait pas si elle joue le bon rythme.
- Se rend compte après plusieurs semaines qu'elle jouait une note fausse dans un passage.
- Se décourage parce qu'elle ne voit pas ses progrès.

**Ce qu'elle attend.**
- Entendre le morceau lentement, main par main.
- Être arrêtée quand elle joue une mauvaise note (mode « attente »).
- Une note ou un pourcentage qui progresse.

**Citation.** « J'aimerais un prof patient qui m'écoute tous les soirs. »

### 2.2 Lucas — l'élève de conservatoire (et sa mère, Nadia)

| | |
|---|---|
| Âge | 11 ans |
| Instrument | Piano droit acoustique |
| Niveau | Cycle 1, 4e année |
| Lecture | Bonne |
| Équipement | Tablette familiale |
| Temps de pratique | 20 minutes par jour, souvent à contrecœur |

**Contexte.** Lucas a cours une fois par semaine. Son professeur lui donne deux ou trois morceaux à travailler. Sa mère, Nadia, ne lit pas la musique et ne peut pas l'aider.

**Frustrations.**
- Lucas : la pratique est répétitive et ennuyeuse.
- Nadia : elle ne sait pas si Lucas a vraiment travaillé ni s'il progresse.
- Le professeur : il découvre au cours que les erreurs se sont installées.

**Ce qu'ils attendent.**
- Lucas : un retour immédiat et motivant, des objectifs courts.
- Nadia : un suivi simple (temps de pratique, progression).
- Contrainte : piano acoustique, donc **captation par le micro** obligatoire.

**Citation (Nadia).** « Je l'entends jouer mais je suis incapable de dire si c'est juste. »

### 2.3 Marc — le pianiste qui reprend

| | |
|---|---|
| Âge | 52 ans |
| Instrument | Piano acoustique, et un clavier numérique d'appoint |
| Niveau | Intermédiaire (ancien 2e cycle) |
| Lecture | Bonne mais rouillée |
| Équipement | Tablette Android haut de gamme (11 pouces) |
| Temps de pratique | Irrégulier, par sessions longues le week-end |

**Contexte.** Marc travaille des pièces classiques plus exigeantes (Chopin, Debussy). Il télécharge des partitions du domaine public, souvent en **PDF**.

**Frustrations.**
- Ses partitions sont en PDF, pas en format numérique structuré.
- Il veut travailler un passage précis en boucle, à tempo progressif.
- Il veut repérer les irrégularités rythmiques dans les traits rapides.

**Ce qu'il attend.**
- Import PDF fiable.
- Boucle sur sélection de mesures, montée progressive du tempo.
- Analyse fine du rythme, y compris main par main.

**Citation.** « Je sais lire, j'ai besoin d'un outil de travail, pas d'un jeu. »

### 2.4 Sophie — la professeure (V2)

| | |
|---|---|
| Âge | 45 ans |
| Statut | Professeure indépendante, 25 élèves |
| Équipement | Smartphone, ordinateur |

**Ce qu'elle attend (V2).**
- Envoyer une partition à un élève avec des consignes (passage, tempo cible).
- Consulter les comptes-rendus de pratique de ses élèves entre deux cours.
- Annoter la partition (doigtés, indications).

**Point de vigilance.** Sophie ne recommandera l'application que si l'analyse est **fiable** : un outil qui signale de fausses erreurs serait contre-productif pour ses élèves.

## 3. Matrice besoins × personas

| Besoin | Claire | Lucas / Nadia | Marc | Sophie |
|---|:-:|:-:|:-:|:-:|
| Import MusicXML / MIDI | ●● | ● | ● | ●● |
| Import PDF (reconnaissance) | ● | ●● | ●●● | ●● |
| Import photo d'une partition papier | ●● | ●●● | ● | ● |
| Écoute de la partition, tempo réglable | ●●● | ●● | ●● | ● |
| Mains séparées | ●●● | ●● | ●● | ●● |
| Mode attente (s'arrête sur erreur) | ●●● | ●● | ● | ●● |
| Analyse via MIDI | ●●● | — | ●● | ● |
| Analyse via micro (piano acoustique) | ● | ●●● | ●●● | ●●● |
| Analyse fine du rythme | ●● | ● | ●●● | ●● |
| Boucle sur passage, tempo progressif | ●● | ● | ●●● | ●● |
| Gamification, motivation | ●● | ●●● | — | ● |
| Suivi de progression | ●●● | ●●● | ●● | ●●● |
| Suivi parental | — | ●●● | — | — |
| Partage avec le professeur | — | ●● | — | ●●● |

Légende : ●●● essentiel, ●● important, ● utile, — sans objet.

## 4. Enseignements

1. **Deux sources de captation sont nécessaires** : le MIDI (piano numérique, fiable) et le micro (piano acoustique, beaucoup plus difficile). Le persona Lucas, et une part importante du marché, n'a pas de piano numérique.
2. **Le PDF est le format réel de la plupart des partitions** des utilisateurs. Ne supporter que MusicXML limiterait fortement l'usage. La reconnaissance optique de partition (OMR) est un sujet difficile [POC-03].
3. **Les attentes de restitution divergent** : ludique et motivant pour un enfant, précis et sobre pour un adulte de niveau intermédiaire. Prévoir un réglage de « style » de retour.
4. **La fiabilité conditionne la prescription** par les professeurs.
