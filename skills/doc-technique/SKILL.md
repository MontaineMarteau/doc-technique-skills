---
name: doc-technique
description: Produit une documentation technique factuelle d'un depot de code, structuree en 5 sections progressives (vue d'ensemble, architecture, regles metier, choix techniques, conditions d'exploitation), plus un document separe pour les points d'attention (pieges, fragilites, bus factor). Fonctionne en mode complet (tout d'un coup) ou en mode progressif (section par section, avec accompagnement). Invoquer quand l'utilisateur veut documenter un repo de code, generer de la doc technique a partir du code source, ou produire une doc pour dossier reglementaire, onboarding, audit ou due diligence.
---

# Documentation technique d'un dépôt de code

Produis une documentation technique factuelle à partir du code source d'un dépôt. Le document est construit de manière progressive, pour être compréhensible par un lecteur non technique autant que par un développeur.

**Pré-requis** : ce skill fait partie d'une famille (`doc-technique`, `doc-technique-questions`, `doc-technique-review`). Lire d'abord `C:\Users\monta\.claude\skills\doc-technique\principes-communs.md` pour le vocabulaire, la structure de référence, les niveaux de confiance et les règles de ton communs aux trois skills.

Deux livrables :
- **Documentation technique** : factuelle, neutre, partageable à l'extérieur (auditeur, nouveau partenaire, prestataire)
- **Points d'attention** : interne, contient les pièges, fragilités, décisions qui coûtent, bus factor, risques. À ne pas diffuser sans filtrage.

**Règle centrale** : la doc principale ne contient aucun jugement, aucune recommandation, aucune supposition non étayée. Tout ce qui relève du "risque", du "problème", du "à améliorer" va dans le document séparé.

**Argument** : chemin vers un dépôt Git local. Si absent, le demander.

---

## 1. Détection du mode

Vérifier si une documentation existe déjà :
- Chercher dans le répertoire courant, dans `5. Documents de travail/`, ou demander
- Si un fichier existe → **mode mise à jour** (section 10)
- Sinon → **mode complet** ou **mode progressif** (section 7)

---

## 2. Collecte

### Mise à jour du dépôt (obligatoire)

Avant toute analyse, s'assurer que le dépôt local est à jour :

```
git -C <repo> fetch --all
git -C <repo> status
```

Si la branche locale est en retard par rapport à la branche distante, demander à l'utilisateur s'il veut faire un `git pull` avant de continuer. Par défaut, proposer le pull.

Si la branche locale a des changements non committés, le signaler et demander comment procéder : documenter l'état actuel (y compris les modifications locales), ou stasher/ignorer les modifications pour documenter la branche propre. Ne jamais écraser de travail local sans confirmation.

**Pourquoi** : la documentation doit refléter l'état du code tel qu'il est partagé avec l'équipe et les contributeurs, pas une version locale obsolète ou divergente.

### Métadonnées

```
git -C <repo> log --oneline --all | wc -l
git -C <repo> log --format="%an" --all | sort -u
git -C <repo> log --format="%ai" --all | sort | head -1
git -C <repo> log --format="%ai" --all | sort | tail -1
```

### Structure

```
git -C <repo> ls-tree -r --name-only HEAD | head -300
```

Identifier : dossiers principaux, entry points, fichiers de configuration, modèles de données, routes ou endpoints.

### Stack et licences

Lire les fichiers de dépendances (package.json, requirements.txt, composer.json, Cargo.toml, go.mod, csproj, Dockerfile, docker-compose.yml). Noter les licences des dépendances principales.

### Code

Lire les fichiers clés en intégralité, pas en survol. Priorité :
1. Entry points (main, index, app, routes, workflows)
2. Modèles de données et schémas
3. Configuration et déploiement
4. Modules métier (les plus gros, les plus modifiés)
5. Tests s'il y en a (révèle ce qui est considéré comme important)

**Depth probe** : pour chaque composant majeur, lire 2 à 3 fichiers clés en intégralité. Si tu ne peux pas expliquer le mécanisme en 3 phrases concrètes, relire. La superficialité d'une v1 vient presque toujours d'une lecture trop rapide.

### Données opérationnelles (optionnel, si accessibles)

Si le projet expose des données opérationnelles mesurables, les collecter à ce stade. Voir `principes-communs.md` section "Données opérationnelles observées" pour la règle d'usage et le placement.

Exemples de sources à tenter selon le contexte du projet :
- Historique d'exécution de pipelines CI/CD (succès, échecs, durées).
- Dashboards de supervision ou d'uptime accessibles.
- Tickets d'incident ou de support archivés.
- Rapports de vulnérabilité de dépendances.
- Statistiques d'usage si le projet en publie.

Si ces données ne sont pas accessibles (pas d'accès, projet sans CI, pas de monitoring), ne pas insister. Noter comme angle mort à creuser dans `doc-technique-questions`. Ne pas bloquer la production de la doc pour attendre ces données.

**Résumé en fin de collecte** : afficher période, commits, contributeurs, stack, structure principale. Si des données opérationnelles ont été collectées, les mentionner aussi. Demander confirmation avant de continuer.

---

## 3. Analyse

Pour chaque section à produire, identifier les éléments à documenter.

**À vérifier explicitement** :
- Les flux de données (entrées, sorties, passages)
- Les dépendances entre composants (qui appelle qui, dans le code, pas dans les noms de dossiers)
- Les règles métier (constantes, validations, transformations, seuils)
- Les configurations de déploiement
- Les tests (ce qui est testé révèle ce qui est important)
- Les licences des dépendances
- L'historique des contributeurs

**Niveau de confiance** pour chaque observation :
- **Fait** : observable dans le code
- **Interprétation** : déduit mais pas explicite, signalé dans le texte si doute
- **Hypothèse** : incertain, à confirmer avec l'équipe, signalé explicitement

**Règle d'or** : si l'information n'est pas observable ou déductible du code, ne pas l'écrire. Pas d'estimation de volume d'utilisateurs si aucune mesure, pas de coût annuel si aucune donnée. Préférer le silence à la supposition.

---

## 4. Structure du document principal

Un seul titre, 5 sections progressives. L'ordre est pensé pour qu'un lecteur découvre le projet du plus général au plus détaillé.

```markdown
# [nom du module], documentation technique

> **Module** : [nom]
> **Dépôt** : [URL ou chemin]
> **Dernière mise à jour** : [date]
> **Objectif** : décrire le fonctionnement, l'architecture, les règles métier et les choix techniques du module [nom].

## Table des matières
(générée avec le détail 1.1, 1.2, 2.1, 2.2, etc.)

## 1. Vue d'ensemble

### 1.1 Présentation
- **En une phrase** : ce que le module fait
- **Scénario d'usage concret** : qui fait quoi, dans quel contexte
- **Place dans l'écosystème** : diagramme ASCII simple ou description courte des flux avec l'extérieur

### 1.2 Valeur et cas d'usage
Pour 2 à 4 cas d'usage : utilisateur, problème résolu, valeur concrète. Factuel, pas de supposition de volume.

### 1.3 Chiffres clés
Tableau compact : stack, lignes de code, contributeurs, période, volume de contenu. Uniquement des chiffres observables, pas d'estimation. Si des données opérationnelles mesurables ont été collectées (disponibilité, taux d'incidents, durée moyenne d'exécution), ajouter les agrégats headline ici, en datant explicitement la fenêtre de mesure.

### 1.4 Stack technique
Langages, frameworks, dépendances principales avec versions.

## 2. Architecture technique

L'architecture se construit de manière progressive : vue d'ensemble d'abord, puis zoom par composant ou par pipeline. Chaque sous-section explique un aspect avec un diagramme ou un flux, pour qu'un lecteur non technique puisse suivre.

### 2.1 Vue logique
Les grandes couches et leurs responsabilités. Diagramme ASCII simple. Destiné à poser la carte mentale.

### 2.2 [Pipeline ou composant majeur 1]
Description progressive : qu'est-ce qui entre, qu'est-ce qui passe par quel composant, qu'est-ce qui sort. Diagramme étape par étape.

### 2.3 [Pipeline ou composant majeur 2]
(même structure)

### 2.4 [Autres composants]
Traités plus rapidement si secondaires. Une phrase descriptive + rôle.

### 2.N Déploiement et infrastructure
Où ça tourne, comment c'est déployé, briques externes utilisées.

Le nombre de sous-sections dépend du projet. Pour un monolithe simple, 2.1 + 2.2 + déploiement suffisent. Pour un système avec plusieurs pipelines, une sous-section par pipeline.

## 3. Règles métier

**Regroupement par thème** : si la liste des règles dépasse 6-7 items, regrouper en familles (3.1, 3.2, etc.) plutôt que de produire une liste plate. Le regroupement permet à un lecteur de trouver la règle qu'il cherche par sujet, et il met en évidence les zones de complexité du métier. Exemples de familles : affichage, cycle de vie des données, synchronisation, parsing, conventions.

**Critère de sélection des règles** : appliquer le test "si je supprime cette règle, est-ce qu'un futur lecteur pourrait perdre 2 heures à comprendre pourquoi le code se comporte comme ça ?". Une règle évidente (ex : on n'affiche pas les évènements passés) ne mérite pas une entrée dédiée si elle est triviale à deviner. Viser 6 à 10 règles vraiment utiles, pas 15 dont la moitié paraphrase le code.

**Format condensé (privilégier)** :
```
### [Nom de la règle, court et descriptif]
[Description en 2 à 4 lignes. Ce que fait la règle, pourquoi elle existe, mécanisme.]
Voir [fichier ou fonction où le code se trouve].
```

**Format détaillé (pour règles avec cas limites non triviaux)** :
Garder le même style visuel que le format condensé : paragraphes fluides, pas de titres en gras de type "Ce que fait : / Pourquoi : / Comment : / Cas limites :". L'alternance entre titres en gras (sur les règles complexes) et paragraphes (sur les règles simples) crée un effet visuel inégal qui casse la lecture.

Pour une règle complexe, écrire 2 ou 3 paragraphes : un paragraphe sur ce que fait la règle et pourquoi, un paragraphe sur le mécanisme et les cas limites. Garder le pointeur vers le code en fin de bloc.

Règle de choix : si la règle tient honnêtement en 3 lignes sans rien perdre, format condensé. Le format détaillé est réservé aux règles dont les cas limites sont importants ou dont le mécanisme nécessite plusieurs paragraphes pour être compris.

## 4. Choix techniques notables

Pour chaque choix non évident, où l'alternative aurait été raisonnable :

```
### Choix X : [nom]
**Le problème** : [ce que ce choix adresse]
**Le choix fait** : [ce qui a été implémenté]
**L'alternative** : [ce qui aurait pu être fait, pourquoi non retenu]
**La conséquence** : [ce que ça implique pour le système, factuel, observable]
```

3 à 5 choix majeurs. Un choix banal (utiliser Git, utiliser PostgreSQL dans un projet web) ne figure pas ici.

Pas de spéculation sur "qui a décidé", "quand", ou "signal de remise en question". Rester sur du factuel observable.

## 5. Conditions d'exploitation

Section factuelle, sans jugement. Sert à un auditeur ou à un nouveau prestataire qui veut savoir dans quoi il s'engage.

### 5.1 Dépendances et licences
Tableau des dépendances principales avec leur licence. Identification des licences copyleft si présentes.

### 5.2 Sécurité et conformité
Données personnelles collectées (liste ou "aucune"). Base de données (description ou "aucune"). Gestion des secrets. Normes applicables si projet régulé.

### 5.3 Processus qualité
Tests (existence, couverture si mesurée). Pipeline CI/CD (description courte). Déploiement (mode et fréquence). Monitoring (outils ou "aucun"). Si des données opérationnelles mesurables ont été collectées (historique d'exécutions, taux de succès, durées, incidents passés), ajouter ici une sous-section "Exécutions observées" ou équivalent, avec datage explicite de la fenêtre et mention de la source de mesure.

## Annexe : Glossaire
Termes propres au projet, acronymes, vocabulaire métier. Chaque terme en 1 à 2 lignes.
```

---

## 5. Structure du document séparé (points d'attention)

Document interne, qui regroupe tout ce qui pourrait être perçu comme un jugement ou une critique. À ne pas diffuser sans filtrage.

**Important** : c'est ici que vivent les limites du produit (ce que le produit ne fait pas). Ne pas créer de section "Limitations" dans la vue d'ensemble du doc principal, ça se transforme presque toujours en analyse mal placée. Tout va en section 6 du présent document.

**Règle de placement : un item, un seul home. Pas de renvois entre sections.**

Les 6 sections ci-dessous se chevauchent par nature (un même problème est à la fois un piège, une dette, et une fragilité). Pour éviter les redondances dans le document produit, appliquer la règle de tri suivante :

- **Pièges** : gotcha opérationnel. Ce qu'il faut savoir AVANT de toucher au code, sinon le contributeur perd 2 heures à comprendre un comportement non-évident. Pas de "coût" ni "alternative", juste une règle du jeu.
- **Fragilités externes** : ce qu'on ne peut PAS réparer nous-mêmes (cause hors de notre contrôle, comme un site tiers qui change son HTML). À distinguer strictement de la dette, qui est actionnable. Si une alternative interne existe, ce n'est pas une fragilité externe.
- **Couplages cachés** : "si tu changes X, vérifie Y, Z, W". Effets en chaîne entre fichiers ou sous-systèmes.
- **Dette technique mesurable** : quelque chose qu'on PEUT réparer, avec coût actuel et alternative chiffrée. Le "coût actuel" absorbe le piège opérationnel que la décision génère. Si un sujet est à la fois un piège et une dette réparable, il va en dette, et le piège est décrit dans le "coût actuel".
- **Bus factor et concentration des compétences** : personnes et compétences, pas code.
- **Limites et absences** : choix de périmètre assumés ou features manquantes sans refactoring concret à faire. Une absence qui a un coût mesurable et une alternative chiffrée (ex : absence de tests) va plutôt en dette technique. Une absence qui est un choix (pas d'inscription, pas de tracking) va en limites.

```markdown
# [nom du module], points d'attention

> **Module** : [nom]
> **Date** : [date]
> **Usage** : document interne, destiné à l'équipe. Ne pas partager sans filtrage préalable.

## 1. Pièges à connaître avant de modifier le code

10 à 15 pièges concrets, chacun en 3 à 5 lignes.

### Piège : [nom court]
[Description en langage accessible.]
**Conséquence si ignoré** : [ce qui casse concrètement]
**Comment éviter** : [action précise]

Critère de sélection : chaque piège répond à "qu'est-ce qui fait perdre 2 heures à quelqu'un qui ne le sait pas".

## 2. Fragilités externes

Ce qu'on ne peut pas réparer nous-mêmes. Classées par sévérité (critique, moyen, mineur) avec description, impact concret, probabilité, et ce qu'on peut seulement faire (détecter vite, réagir).

**Probabilité estimée vs observée** : privilégier la probabilité observée quand des données opérationnelles sont disponibles (ex : un incident passé qui a matérialisé la fragilité). Formuler "Probabilité observée (sur N jours au [date]) : fragilité déjà matérialisée, [description de l'incident]". Si aucune donnée n'est disponible, rester sur une estimation, mais la marquer explicitement comme telle ("Probabilité estimée : ...").

## 3. Couplages cachés

Changements qui impliquent plusieurs endroits du code. Format : "si tu changes X, vérifie aussi Y, Z, W".

## 4. Dette technique mesurable

Décisions qu'on peut réparer. Pour chaque item :
- **Décision actuelle** : ce qui est en place
- **Coût actuel** : le prix payé aujourd'hui (inclut les pièges opérationnels que la décision génère)
- **Alternative** : ce qu'on pourrait mettre à la place
- **Coût estimé** : pour basculer

## 5. Bus factor et concentration des compétences

Qui sait faire quoi. Temps de reprise estimé si quelqu'un d'indispensable s'arrête. Zones de connaissance non partagées. Définir "bus factor" en ouverture de section pour un lecteur non initié.

## 6. Limites et absences

Ce que le produit ne fait pas. Trois sous-catégories possibles :
- **6.1 Limites produit assumées** : choix de périmètre explicites (pas d'inscription, pas de tracking, pas de carte).
- **6.2 Limites fonctionnelles non compensées** : absences qui produisent des effets visibles (pas de modération, pas de notification).
- **6.3 Absences techniques** : infrastructure ou opérations qui manquent (pas de monitoring, pas de doc API, pas de versioning).

Sans jugement. Si une absence a un coût mesurable et une alternative chiffrée, elle va en dette technique (section 4), pas ici.
```

---

## 6. Règles de rédaction

**Ton et style**
- Langage accessible. Chaque terme technique est expliqué à sa première occurrence, ou évité quand un mot plus simple suffit.
- Ton factuel. Pas de jugement de valeur ("propre", "élégant", "bien pensé", "bonne pratique", "intéressant"). Décrire ce qu'un choix permet ou implique, le lecteur tire ses conclusions.
- Pas de tiret long ni simple dans les phrases (virgule, point ou parenthèses). Autorisé dans les titres.
- Pas d'emojis.
- "Vous" dans les documents clients, jamais "tu".
- **Pas de mention des profils de lecteur dans le document produit** (jamais "pour un CEO lire telle section", "en tant que CPO"). Le document est neutre.

**Critère central de chaque ligne**
Chaque ligne doit satisfaire l'un des deux tests :
1. Un lecteur compétent ne peut pas trouver cette information dans le code en moins de 5 minutes.
2. Cette information aide à comprendre, décider ou éviter un piège.

Si ni l'un ni l'autre, supprimer.

**Séparation factuel / jugement, critique**
- Le document principal décrit ce qui existe.
- Le document points d'attention décrit ce qui pose problème.
- Ne jamais mélanger les deux. Un auditeur qui lit le document principal ne doit trouver que du factuel.

**Pas de supposition**
- Pas de volume d'utilisateurs si aucune mesure.
- Pas de coût annuel si aucun chiffrage réel.
- Pas de supposition sur les intentions des contributeurs ("l'équipe a décidé parce que...") sauf si documenté dans un commit, une PR, un issue.
- Préférer le silence à la supposition.

**Ce qu'il faut mettre**
- Les chiffres d'implémentation (lignes de code, nombre de fichiers) uniquement dans 1.3 (Chiffres clés). Pas ailleurs.
- Un schéma ASCII simple quand il clarifie (5 à 7 cases max). **Disposition verticale par défaut** : les flèches descendantes (`▼`) sont plus naturelles à lire en markdown et les boîtes empilées tiennent mieux dans la largeur d'un éditeur. Si plusieurs sources convergent vers un même point, regrouper avec une jonction en T avant de continuer vers le bas. La disposition horizontale reste possible pour les cas où elle est plus parlante (par exemple un cycle qui boucle, ou deux composants strictement parallèles à comparer côte à côte). Le critère final est la lisibilité, pas une règle stricte.
- Une table des matières cliquable, avec des liens markdown explicites vers les ancres en kebab-case. Exemple : `- [1.1 Présentation](#11-présentation)`. La TOC sans liens n'est pas navigable et frustre le lecteur.
- Pour chaque règle métier : le mécanisme concret, pas seulement la présence d'une fonctionnalité.
- Pour chaque choix technique : le problème résolu et l'alternative écartée.

**Ce qu'il ne faut pas mettre**
- Pas de paraphrase du code (si visible par `ls`, ne pas l'écrire).
- Pas de noms de fichiers, classes ou fonctions sans contexte.
- Pas de règles métier inventées.
- Pas de comportements inventés ou embellis (si le code retourne un booléen, ne pas écrire "retourne un score").
- Pas de formulations creuses ("gère X", "prend en charge X", "permet de X") sans la mécanique concrète qui suit.

---

## 7. Déroulement

Au démarrage, après la collecte et la confirmation du résumé, poser cette question :

> "Comment souhaitez-vous construire la documentation ?
> 1. **Mode complet** : je génère tout d'un coup, puis nous relisons.
> 2. **Mode progressif** : nous construisons section par section ensemble, je vous explique ce qui est produit à chaque étape et vous validez avant d'avancer.
>
> Le mode progressif prend plus de temps mais permet de mieux comprendre et orienter le résultat. Il est recommandé si vous n'êtes pas familier avec Claude ou si c'est votre première documentation de ce type."

### 7.1 Mode complet

1. **Collecte et analyse** (section 2 et 3 du skill), en silence.
2. **Aperçu** : montrer la section 1 complète + la table des matières du document principal avant d'écrire le reste. Demander validation.
3. **Rédaction complète** : écrire le document principal en entier.
4. **Rédaction points d'attention** : écrire le document séparé.
5. **Relecture indépendante** (section 8) → corriger et présenter les corrections.
6. Signaler les questions ouvertes.

### 7.2 Mode progressif

Accompagner pas à pas. Pour chaque section, suivre le même pattern :

**Étape A : Présenter la section**
Avant d'écrire, expliquer en 2 à 3 phrases : ce qu'on va documenter, pourquoi c'est important, ce qu'on va chercher dans le code. Destiné à un lecteur non-expert : rester simple, pas de jargon.

Exemple : "Nous allons maintenant documenter la vue d'ensemble. C'est la première chose que lira toute personne qui découvre le projet. Elle doit comprendre en quelques lignes ce que fait le produit, pour qui, et comment il s'articule avec l'extérieur."

**Étape B : Produire la section**
Écrire le contenu de la section.

**Étape C : Montrer le résultat**
Présenter ce qui a été produit. Pointer 1 à 2 choix importants faits pendant la rédaction, pour que l'utilisateur puisse réagir.

Exemple : "J'ai choisi de décrire le pipeline de synchronisation en 5 étapes plutôt qu'en 3 pour rendre le flux de données plus lisible. J'ai aussi ajouté le détail du mécanisme de skip parce qu'il a été introduit récemment et que c'est une règle que le code ne révèle pas immédiatement."

**Étape D : Demander validation**
Trois choix proposés :
1. "Je valide, on passe à la suivante"
2. "Je voudrais ajuster X"
3. "J'ai une question sur Y"

Tant que l'utilisateur n'a pas validé, rester sur la section en cours.

**Ordre des sections à produire** (dans les deux modes) :
1. Section 1 du document principal (Vue d'ensemble) en premier, car elle donne le contexte.
2. Section 2 (Architecture), car elle pose la carte mentale.
3. Section 3 (Règles métier) et 4 (Choix techniques), en parallèle si les mécanismes se recoupent.
4. Section 5 (Conditions d'exploitation).
5. Glossaire, construit au fil de la rédaction puis consolidé.
6. Document points d'attention, après que le principal est validé.

**Sauvegarde** :
- Doc principale : proposer `[date]-[nom-du-module]-documentation-technique.md`
- Points d'attention : proposer `[date]-[nom-du-module]-points-attention.md`
Demander le chemin de sauvegarde.

**Quand créer le fichier** : créer le fichier de la doc principale dès que la section 1 est validée par l'utilisateur, puis ajouter les sections suivantes par Edit. L'utilisateur peut alors relire dans son IDE en parallèle de la rédaction. Ne pas attendre la fin de la rédaction pour écrire dans le fichier, l'utilisateur perd la possibilité de naviguer dans le document à mesure qu'il grossit. Le fichier points d'attention peut être créé une fois la doc principale terminée et validée.

---

## 8. Relecture indépendante, en deux passes

Relire le texte produit, section par section. Ne pas se fier à ce qu'on pense avoir écrit.

### 8.1 Passe 1 : relecture structurelle (à faire en premier)

Avant de peaufiner les phrases, vérifier que la structure sert le propos.

**Questions à se poser** :
- Chaque section apporte-t-elle de l'information non triviale ? Si une section peut être résumée par "voir le code", la supprimer.
- Y a-t-il des redondances entre sections ? Un même item qui apparaît dans plusieurs sections doit être relocalisé vers son home unique selon la règle de placement (section 5 pour le document points d'attention). Pas de renvois entre sections, ils sont pénibles à lire. Le home choisi absorbe les angles des autres sections (exemple : un piège qui est aussi une dette réparable va en dette, et son piège opérationnel est décrit dans le "coût actuel").
- La progression est-elle respectée ? Un non-expert peut-il lire la section 1, puis 2, puis 3, et comprendre à chaque étape ?
- Le document principal contient-il quelque chose qui relève du jugement ou du point d'attention ? Si oui, déplacer vers le document séparé.
- Les titres de section sont-ils assez explicites pour qu'un lecteur comprenne d'entrée ce qu'il va y trouver ? Un titre comme "Fragilités" est trop vague. "Fragilités externes (causes hors de notre contrôle)" l'est davantage. Privilégier les titres qui disent quoi ET pourquoi c'est là.

**Décisions possibles** pour chaque section : garder / fusionner / relocaliser un item / raccourcir / supprimer / déplacer vers le doc points d'attention.

### 8.2 Passe 2 : relecture de prose (à faire après la structurelle)

**Exactitude factuelle**
- [ ] Chaque affirmation technique est-elle vérifiable dans le code ?
- [ ] Chaque chiffre est-il exact ? Recompter si nécessaire.
- [ ] Les termes techniques sont-ils corrects ? (ne pas écrire "REST" si c'est du RPC, ne pas écrire "score" si c'est un booléen)
- [ ] Les diagrammes reflètent-ils les connexions réelles ?
- [ ] Le document ne décrit-il que ce qui existe dans le code aujourd'hui ?
- [ ] Y a-t-il des suppositions non étayées (volumes, coûts, intentions) ? Les supprimer ou les déplacer vers les questions ouvertes.
- [ ] Si des données opérationnelles mesurables sont citées, sont-elles datées explicitement avec la fenêtre de mesure, et la source est-elle indiquée (commande ou procédure de récupération) ?

**Niveau de détail**
- [ ] Les chiffres d'implémentation sont-ils bien confinés à 1.4 ?
- [ ] Pour chaque règle métier et chaque choix technique, le mécanisme est-il décrit ou seulement le fait ?
- [ ] Pour les règles non triviales, les 4 champs sont-ils présents ? Pour les règles évidentes, le format condensé est-il assumé ?
- [ ] Y a-t-il des formulations creuses sans mécanique concrète ? Les traquer et réécrire.

**Séparation factuel / jugement**
- [ ] Le document principal est-il entièrement factuel ?
- [ ] Les points de fragilité, pièges et risques sont-ils tous dans le document séparé ?
- [ ] Le bus factor n'apparaît-il que dans le document séparé ?

**Ton**
- [ ] Y a-t-il des jugements de valeur ? Chercher "propre", "bien", "élégant", "intéressant", "bonne pratique".
- [ ] Y a-t-il des tirets longs ou simples dans les phrases (hors titres) ?
- [ ] Les termes techniques sont-ils expliqués à leur première occurrence ? Pièges fréquents : "headless", "fallback", "scraping", "cron", "bus factor", "ICU", "DST". Soit définir en parenthèses, soit reformuler avec un mot plus simple.
- [ ] Le ton est-il homogène ? Pas d'expression familière isolée ("ça mord", "y a", "ya plus qu'à") au milieu d'un texte par ailleurs neutre.
- [ ] L'usage du "tu" / "vous" est-il cohérent ? Le doc principal et le doc points d'attention doivent privilégier des formules impersonnelles ("Modifier X implique de vérifier Y") plutôt que "Si tu modifies X, vérifie aussi Y".
- [ ] Y a-t-il des mentions de profils de lecteur (CEO, CPO, CTO, auditeur) dans le document ? Les supprimer.

### 8.3 Présenter les corrections

Après les deux passes, résumer à l'utilisateur les corrections effectuées.

---

## 9. Calibrage

Adapter la longueur au contexte, pas au type de module :

| Contexte | Sections développées | Taille cible document principal |
|---|---|---|
| Synthèse rapide | 1 uniquement + glossaire | 100 à 200 lignes |
| Doc standard (onboarding, audit, reprise) | Toutes les sections | 400 à 700 lignes |
| Dossier réglementaire | Toutes + section 5 enrichie + références croisées | 700 à 1 000 lignes |
| Deep-dive sur un composant | 1, 2, 3, 4 focus sur le composant | 300 à 500 lignes |

Règle : 700 lignes pour le document principal est un plafond naturel. Au-delà, on dilue. Exceptions : dossiers réglementaires où le niveau de détail est exigé par la norme.

Document points d'attention : 150 à 300 lignes selon le projet.

---

## 10. Mode mise à jour

Quand une documentation existe déjà :

1. Lire les documents existants (principal et points d'attention).
2. Identifier les changements dans le code depuis la dernière mise à jour (git log depuis la date du document).
3. Proposer à l'utilisateur : mise à jour complète (refaire tout le skill) ou mise à jour incrémentale (ajuster uniquement les sections impactées).
4. Mettre à jour les sections concernées.
5. Mettre à jour la date dans l'en-tête.
6. Montrer un résumé des changements avant d'écrire.

---

## 11. Chaînage avec les autres skills de la famille

Une fois la doc principale et le doc points d'attention produits et validés, proposer à l'utilisateur les suites naturelles :

- **`/doc-technique-questions`** : pour préparer une fiche d'entretien avec le code owner ou un membre de l'équipe, afin de valider les hypothèses signalées et combler les angles morts. Particulièrement utile si la doc a été produite à partir du code seul, sans interview préalable.
- **`/doc-technique-review`** : pour faire relire la doc par une session indépendante (mode "self-review"), avec un rapport structuré sur cinq axes (couverture, exactitude, lisibilité, séparation factuel/jugement, adaptation au contexte). Plus rigoureux que la passe de relecture intégrée à ce skill.

Ces propositions ne sont pas automatiques. Les formuler explicitement à la fin, en laissant l'utilisateur choisir.

## 12. Chaînage avec l'audit réglementaire (optionnel)

Si la documentation est destinée à alimenter un dossier réglementaire (audit DMH, dossier technique, évaluation clinique), proposer `/audit-medical-doc` sur le document principal. L'audit vérifiera la conformité aux normes medtech (IEC 62304, ISO 14971, MDR 2017/745).

Ce chaînage n'est pas automatique. Proposer uniquement si le contexte le justifie.

---

## 13. Publication wiki (optionnel)

Si un wiki existe pour le projet, proposer la publication du document principal. Le document points d'attention reste interne et n'est pas publié. Procédure : cloner le wiki, copier le document principal, pull, commit, push.
