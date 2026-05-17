---
name: doc-technique-questions
description: Produit une fiche de questions à poser au code owner ou à l'équipe technique pour valider et compléter une documentation technique. Cible les angles morts (intentions, décisions historiques, hypothèses) que le code seul ne révèle pas. Invoquer après avoir produit une doc avec /doc-technique, ou directement sur un dépôt avant un entretien de prise en main, un audit, ou une due diligence.
---

# Fiche de questions pour interview technique

Produit une fiche de questions structurée à poser à un membre de l'équipe (code owner, mainteneur principal, CTO) pour valider la compréhension d'un dépôt et combler les angles morts.

**Pré-requis** : lire d'abord `C:\Users\monta\.claude\skills\doc-technique\principes-communs.md` pour le vocabulaire et la structure de référence.

**Argument** :
- Un dépôt Git local (le skill ira chercher les angles morts dans le code), OU
- Un chemin vers une doc technique déjà produite par `/doc-technique` (le skill ira chercher les hypothèses signalées et les zones de silence), OU
- Les deux (privilégié pour une couverture complète).

Si rien n'est fourni, demander.

---

## 1. Pourquoi ce skill existe

Le code seul ne révèle jamais tout :
- Les **intentions** derrière les choix techniques (pourquoi X plutôt que Y)
- Les **incidents passés** qui ont façonné des règles défensives
- Les **personnes** qui détiennent une connaissance non écrite
- Les **plans futurs** qui orientent les évolutions
- Les **contraintes externes** (clients, partenaires, régulation) qui pèsent sur les décisions
- Les **fragilités connues** que personne n'a écrit nulle part

Une doc produite uniquement à partir du code en restera à du factuel. Pour passer de "ce qui existe" à "pourquoi ça existe et comment ça doit évoluer", il faut interviewer quelqu'un qui a la mémoire du projet.

Cette fiche prépare cette interview. Elle ne la remplace pas.

---

## 2. Détection des angles morts

### À partir de la doc technique (si fournie)

Parcourir le document principal et le doc points d'attention. Identifier :

- **Affirmations marquées "Hypothèse"** ou "à confirmer" → questions pour valider
- **Décisions sans rationale** dans la section "Choix techniques notables" → "Pourquoi ce choix plutôt que l'alternative ?"
- **Pièges sans origine documentée** dans la doc PA → "D'où vient ce piège, est-ce un incident passé ?"
- **Bus factor concentré** sur une personne → questions sur la passation, la documentation, les zones non partagées
- **Sections marquées "aucun monitoring", "aucun test"** → questions sur les compensations actuelles et les plans futurs
- **Composants commentés ou inactifs** dans le code (du genre `LinkedInGroup` chez TTH) → "Pourquoi est-ce gardé ? Plan de réactivation ou code mort ?"

### À partir du code (si fourni sans doc, ou en complément)

Lire le code et identifier :

- **Constantes et seuils sans commentaire** : 5 secondes de timeout, 6 mois d'heuristique, 1 an d'archivage. Pourquoi ces valeurs ?
- **Branches conditionnelles non évidentes** : pourquoi ce `if` existe-t-il, quel cas réel l'a motivé ?
- **TODO, FIXME, HACK, XXX** : sont-ils encore d'actualité, qui les a posés, à quel échéance prévue ?
- **Code commenté, code mort** : pourquoi gardé, qui décide de la suppression ?
- **Configurations en dur** : pourquoi pas de variable d'environnement ou de fichier de config ?
- **Dépendances en versions précises** : pourquoi ne pas suivre les mises à jour ?
- **Fichiers qui n'apparaissent plus dans le git log récent** : sont-ils stables, abandonnés, ou trop critiques pour y toucher ?
- **Workflows CI/CD désactivés ou commentés** : pourquoi, depuis quand, plan de réactivation ?

### Angles morts transversaux à toujours interroger

- **Histoire et contexte** : depuis quand le projet existe, quelle était l'intention initiale, qu'est-ce qui a changé ?
- **Équipe et rôles** : qui fait quoi, qui valide quoi, qui est joignable en cas d'incident ?
- **Cycle de release** : à quelle fréquence on déploie, qui décide, comment on annonce ?
- **Incidents passés** : un par an au moins, normalement. Lesquels ont marqué l'équipe ?
- **Plans à 6-12 mois** : refonte, migration, sunset prévu, fonctionnalités attendues ?
- **Dépendances humaines extérieures** : prestataire, contributeur externe, client clé qui dirige les évolutions ?

---

## 3. Structure du livrable

Un seul document, organisé par section pour faciliter une interview chronologique. Chaque section regroupe les questions par thème.

```markdown
# [Nom du projet], questions pour interview

> **Date de préparation** : [date]
> **Interviewer attendu** : [code owner, à préciser]
> **Durée estimée** : 30 à 90 minutes selon le périmètre
> **Objectif** : valider la compréhension de [nom] et combler les angles morts identifiés.

## Comment utiliser ce document

- Cocher chaque question au fur et à mesure
- Noter la réponse en quelques mots après chaque question
- Marquer "à creuser" pour les sujets qui méritent une seconde session
- Marquer "?" pour les questions auxquelles l'interviewé ne sait pas répondre (ce sont des angles morts confirmés)

## 1. Vue d'ensemble et histoire

[Questions sur l'intention initiale, l'évolution du périmètre, le pourquoi du projet]

## 2. Architecture et choix techniques

[Questions sur les décisions structurantes, les alternatives écartées, les points de bascule]

## 3. Règles métier et cas limites

[Questions sur les seuils, les heuristiques, les comportements non documentés]

## 4. Équipe, rôles et bus factor

[Questions sur les personnes, les zones de connaissance, les passations]

## 5. Production, incidents et monitoring

[Questions sur les incidents passés, les compensations à l'absence de tests/monitoring, les plans futurs]

## 6. Conditions d'exploitation et conformité

[Questions sur les licences, les contrats, les obligations réglementaires]

## 7. Roadmap et évolutions à 6-12 mois

[Questions sur les plans futurs, les refontes envisagées, les sunset]

## Annexe : questions d'ouverture libre

3 à 5 questions ouvertes pour laisser l'interviewé apporter ce qui n'a pas été abordé. Exemples :
- Qu'est-ce que vous feriez en premier si vous repreniez ce projet de zéro ?
- Quel est le sujet sur lequel vous appréhendez qu'on vous pose des questions ?
- Qu'est-ce qui vous empêche de dormir sur ce projet ?
```

---

## 4. Format de chaque question

Chaque question est une puce. Elle comporte :

```
- **[Q]** [Question rédigée naturellement, comme on la poserait à l'oral]
  - *Pourquoi cette question* : [angle mort visé en une phrase]
  - *Référence* : [section du doc, fichier de code, ou ligne précise]
  - *Niveau de criticité* : critique / utile / complément
```

Exemple :

```
- **[Q]** Pourquoi avoir choisi C# .NET 10 pour le scraper alors que Python est plus courant pour ce type de tâche ?
  - *Pourquoi cette question* : la doc identifie ce choix comme une barrière à l'entrée, mais ne donne pas le rationale historique. La réponse oriente la stratégie de remplacement éventuel.
  - *Référence* : doc principale section 4, choix 3
  - *Niveau de criticité* : utile
```

**Niveaux de criticité** :
- **critique** : sans la réponse, impossible de prendre une décision sur le projet (ex : "qui détient la propriété intellectuelle ?")
- **utile** : la réponse oriente significativement la compréhension ou les décisions
- **complément** : enrichit la compréhension, peut être posé en fin d'interview si le temps le permet

Calibrer la fiche : viser **15 à 25 questions critiques + utiles** pour une interview de 60 minutes. Au-delà, l'interviewé sature.

---

## 5. Règles de rédaction des questions

- **Question ouverte plutôt que fermée** : "Pourquoi avoir choisi X" plutôt que "Êtes-vous content de X". Sauf si on cherche un oui/non explicite.
- **Une question, un sujet** : ne pas combiner deux interrogations dans une même puce.
- **Pas de jugement implicite** : "Qu'est-ce qui a motivé l'absence de tests ?" plutôt que "Pourquoi n'avoir jamais écrit de tests ?".
- **Ancrage factuel** : référencer un fichier, une section, un commit pour que l'interviewé sache de quoi on parle.
- **Question contextualisée** : pas de "Comment ça marche ?". Préférer "Comment fonctionne le mécanisme de skip décrit en règle 4.4 ?".
- **Préférer les questions qui appellent un récit** ("Racontez-moi le dernier gros incident") aux questions qui appellent une affirmation ("Y a-t-il eu un incident ?"). Le récit révèle plus.

---

## 6. Déroulement

1. **Collecte** : si une doc technique existe, la lire en entier. Si on a accès au code, parcourir les sections principales pour identifier les angles morts (constantes, TODOs, branches non évidentes).
2. **Identification des angles morts** : produire en interne une liste brute de tous les sujets à interroger, avec leur source.
3. **Tri par section et par criticité** : organiser les questions selon les 7 sections du livrable.
4. **Calibrage** : si la liste dépasse 30 questions critiques + utiles, prioriser. Mieux vaut 20 bonnes questions répondues qu'une liste de 50 jamais terminée.
5. **Rédaction** : appliquer le format de la section 4 pour chaque question.
6. **Présenter à l'utilisateur** : montrer la fiche complète, demander s'il manque un thème ou s'il faut ajuster le niveau de détail.
7. **Sauvegarde** : proposer `[date]-[nom-du-module]-questions-interview.md`. Demander le chemin.

---

## 7. Après l'interview

Une fois l'interview menée, l'utilisateur revient avec ses notes. Proposer alors d'invoquer `/doc-technique` en **mode mise à jour** pour intégrer les réponses dans la doc principale et/ou le doc points d'attention.

Les questions sans réponse (marquées `?` par l'interviewé) sont des angles morts confirmés. Elles peuvent rester comme telles dans une nouvelle version de la doc, dans une section "Questions ouvertes" ou comme hypothèses signalées.

---

## 8. Calibrage selon le contexte

| Contexte | Nombre de questions critiques + utiles | Durée d'interview cible |
|---|---|---|
| Onboarding nouveau dev | 10 à 15 | 45 minutes |
| Reprise d'un projet abandonné | 25 à 35 | 90 minutes, en deux sessions |
| Audit qualité interne | 20 à 30 | 60 à 90 minutes |
| Due diligence externe | 30 à 50 | 2 à 4 heures, en plusieurs sessions, avec preuves demandées |
| Préparation cession ou levée | 25 à 40 | 90 minutes, suivi d'une session de validation des preuves |

En due diligence, ajouter systématiquement des questions sur les preuves :
- "Pouvez-vous me montrer les logs CI des 12 derniers mois ?"
- "Avez-vous un rapport d'audit sécurité à me communiquer ?"
- "Pouvez-vous me donner accès au gestionnaire de tickets ?"

Ces questions sont marquées **critiques**, et leur absence de réponse est en soi un signal.
