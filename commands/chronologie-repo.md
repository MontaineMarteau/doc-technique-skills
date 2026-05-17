# Chronologie R&D d'un dépôt Git

Produis une chronologie R&D structurée à partir de l'historique Git d'un dépôt. Le document est destiné à des lecteurs non techniques (auditeur fiscal, expert CIR/CII) et doit être compréhensible sans connaissance du code.

**Argument** : chemin vers un dépôt Git local (ex: `/chronologie-repo ~/code/mon-projet/front`). Si absent, le demander.

---

## 1. Détection du mode

Avant de commencer, vérifie si une chronologie existe déjà pour ce dépôt :
- Cherche dans le répertoire courant, dans `5. Documents de travail/`, ou demande
- Si un fichier existe → **mode incrémental** (voir section 6)
- Sinon → **mode complet** (sections 2 à 5)

---

## 2. Collecte (Phase 1)

### Métadonnées

```
git -C <repo> log --oneline --all | wc -l
git -C <repo> log --format="%an" --all | sort -u
git -C <repo> log --format="%ai" --all | sort | head -1
git -C <repo> log --format="%ai" --all | sort | tail -1
git -C <repo> log --all --format="%ai" | cut -d'-' -f1-2 | sort | uniq -c
```

### Commits

Tous les commits avec détails et stats :

```
git -C <repo> log --all --format="COMMIT_START%nHash: %h%nDate: %ai%nAuthor: %an%nSubject: %s%nBody: %b%nCOMMIT_END" --stat
```

Si plus de 500 commits, procéder mois par mois avec `--after` / `--before`.

### Complément (optionnel)

```
git -C <repo> ls-tree -r --name-only HEAD | head -200
git -C <repo> tag -l --format="%(refname:short) %(creatordate:short) %(subject)"
git -C <repo> branch -a --format="%(refname:short)"
```

**À la fin de la collecte** : afficher un résumé (période, nombre de commits, contributeurs).

---

## 3. Analyse (Phase 2)

Pour chaque mois d'activité :
- Regrouper les commits par thème (fonctionnalité, refactoring, infrastructure, etc.)
- Identifier les changements d'architecture ou de direction
- Repérer les jalons R&D (nouvelle capacité, pivot, passage en production, problème technique résolu)
- Repérer les échecs et fausses pistes (code ajouté puis supprimé, changements de direction, tâtonnements)

Pour chaque observation, noter le **niveau de confiance** :
- **Fait** : observable directement dans les commits
- **Interprétation** : déduit mais pas explicite
- **Hypothèse** : incertain, à confirmer avec l'équipe

---

## 4. Rédaction (Phase 3)

### Structure du document

Tout est en **ordre décroissant** (le plus récent en premier) : table des matières, mois, jalons.

```markdown
# Chronologie R&D — [nom du module]

> **Module** : [nom]
> **Période couverte** : [mois année] à [mois année] ([durée])
> **Source** : historique Git du dépôt `[URL ou chemin]`
> **Objectif** : documenter l'activité R&D sur le module [nom].

---

## Synthèse

[2-3 phrases sur le module et son évolution.]

**Chiffres clés :**
- [N] commits sur [durée]
- [N] contributeur(s) : [noms]
- [Points saillants]

---

## Chronologie mois par mois

### [Mois Année] — [Titre descriptif]

> [N] commits | [N] fichiers modifiés | +[N] / -[N] lignes | Contributeurs : [noms]

**Ce qui s'est passé :** [résumé en 2-3 phrases]

**Travaux :**
- [Items regroupés par sous-thème si > 5 items, sinon liste plate]

**Jalon :** [Si applicable, 1-2 phrases]

---

## Jalons R&D majeurs — Vue synthétique

| Date | Jalon | Signification |
|------|-------|---------------|

---

## Échecs, fausses pistes et aller-retours

### [N]. [Titre] ([date], [durée/résolution])

**L'hypothèse :** [ce qui a été tenté]
**Ce qui s'est passé :** [pourquoi ça n'a pas marché]
**Pourquoi c'est notable :** [ce que ça révèle]

---

## Axes de recherche et développement identifiés

### [N]. [Titre]
[Description, pourquoi c'est de la R&D]

---

## Questions ouvertes et zones d'incertitude

[Uniquement sur les 3 derniers mois. Questions à poser à l'équipe.]

---

## Note méthodologique

Ce document a été produit à partir de l'analyse des [N] commits du dépôt `[nom]` entre le [date] et le [date]. [...]

*Document généré le [date du jour].*
```

### Règles de rédaction

**Ton et style**
- Langage accessible : chaque terme technique est expliqué à sa première occurrence
- Factuel d'abord : distinguer faits, interprétations et hypothèses
- Pas de tiret long ni simple dans les phrases (virgule, point ou parenthèses à la place). OK dans les titres.
- Pas d'emojis

**Ce qu'il faut mettre**
- Les chiffres (commits, fichiers, lignes) uniquement dans le bloc `>` en en-tête de chaque mois
- Des sous-thèmes en gras (pas de `###`) pour regrouper les items quand un mois a plus de 5-6 travaux. Noms courts (2-4 mots), orientés produit/fonctionnalité. Si 5 items ou moins, liste plate.
- Les échecs, fausses pistes et aller-retours dans leur section dédiée (ils démontrent la démarche R&D : essayer, mesurer, pivoter)
- L'échelle des livrables significatifs dans les jalons et axes R&D (ex: "framework de 920 lignes")

**Ce qu'il ne faut pas mettre**
- Pas de chiffres fichiers/lignes dans les descriptions de travaux (ça alourdit la lecture)
- Pas de jugement sur les pratiques de commit (messages, découpage, fréquence). Le document décrit ce qui a été produit, pas comment.
- Pas de questions ouvertes sur du code de plus de 3 mois (le code a évolué depuis)

---

## 5. Déroulement (mode complet)

1. Collecte → afficher résumé rapide
2. Analyse (en silence)
3. Rédaction → montrer un aperçu (synthèse + table des jalons) avant d'écrire
4. Demander confirmation → écrire le fichier
5. Signaler les questions ouvertes

**Sauvegarde** : proposer `[date-du-jour]-chronologie-rd-[nom-du-module].md`, demander à l'utilisateur.

---

## 6. Mode incrémental (mise à jour)

Quand une chronologie existe déjà :

**Détection** : lire la Note méthodologique du fichier existant pour trouver la date du dernier commit analysé.

**Collecte** : recollecter depuis le début du dernier mois analysé (il était peut-être incomplet).

**Mise à jour du document** :
1. Réécrire le dernier mois (compléter)
2. Ajouter les nouveaux mois en haut (ordre décroissant)
3. Mettre à jour : table des matières, synthèse (chiffres, période), jalons, échecs, axes R&D
4. Réécrire les questions ouvertes (3 derniers mois uniquement)
5. Mettre à jour la note méthodologique (nouvelle date, nouveau total)

**Déroulement** :
1. Annoncer le mode incrémental et le périmètre ("2 nouveaux mois, 1 à compléter")
2. Collecter et analyser
3. Montrer un aperçu des changements
4. Demander confirmation → mettre à jour le fichier

**Publication wiki** (si demandé) : chercher un dossier wiki local correspondant au projet (par exemple `<chemin-projet>/group-wiki/`), copier, `git pull`, commit, push.
