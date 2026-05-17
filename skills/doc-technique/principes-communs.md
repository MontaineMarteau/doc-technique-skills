# Principes communs à la famille doc-technique

Ce fichier est partagé par les trois skills `doc-technique`, `doc-technique-questions` et `doc-technique-review`. Il contient le vocabulaire, la structure de référence, les règles de ton et les conventions communes.

**Quand modifier ce fichier** : à chaque fois qu'un principe transversal change (structure de la doc, vocabulaire, format des règles, etc.). Toutes les évolutions à faire dans les trois skills passent d'abord par ici.

**Quand consulter ce fichier** : au démarrage de chaque skill de la famille, pour s'assurer d'utiliser le même vocabulaire et la même structure.

---

## Vocabulaire

| Terme | Définition |
|---|---|
| **doc principale** | Le document factuel et neutre, partageable à l'extérieur. 5 sections + glossaire. |
| **doc points d'attention** (ou doc PA) | Le document interne, qui contient pièges, fragilités, dette, bus factor, limites. 6 sections. À ne pas diffuser sans filtrage. |
| **règle métier** | Comportement non trivial du code qui ne se déduit pas en moins de 5 minutes de lecture. |
| **choix technique notable** | Décision d'implémentation où une alternative raisonnable existait. |
| **chiffres clés** | Tableau dans la section 1.3 du doc principal. Uniquement des chiffres observables. |
| **niveau de confiance** | Étiquette appliquée à une affirmation : Fait, Interprétation ou Hypothèse. |
| **angle mort** | Information manquante dans le code seul, qui nécessite une interview ou un document externe pour être obtenue. |
| **bus factor** | Nombre minimum de personnes dont la disparition bloquerait l'avancée du projet. |

---

## Structure de la doc principale

Un seul titre, 5 sections progressives. L'ordre est pensé pour qu'un lecteur découvre le projet du plus général au plus détaillé.

```
1. Vue d'ensemble
   1.1 Présentation (en une phrase, scénario, place dans l'écosystème)
   1.2 Valeur et cas d'usage
   1.3 Chiffres clés
   1.4 Stack technique

2. Architecture technique
   2.1 Vue logique
   2.2 [Pipeline ou composant majeur 1]
   2.3 [Pipeline ou composant majeur 2]
   2.N Déploiement et infrastructure

3. Règles métier
   Regroupées en familles si > 6-7 règles (3.1, 3.2, etc.).

4. Choix techniques notables
   3 à 5 choix. Format : Le problème, Le choix fait, L'alternative, La conséquence.

5. Conditions d'exploitation
   5.1 Dépendances et licences
   5.2 Sécurité et conformité
   5.3 Processus qualité

Annexe : Glossaire
```

**Important** : pas de section "Limitations" dans la vue d'ensemble. Les limites du produit vivent dans la section 6 du doc points d'attention. Une section "Limitations" placée trop tôt se transforme presque toujours en analyse mal placée.

## Structure du doc points d'attention

```
1. Pièges à connaître avant de modifier le code
   10 à 15 pièges. Format : description, conséquence si ignoré, comment éviter.

2. Fragilités externes
   Causes hors de notre contrôle (site tiers qui change son HTML, etc.).
   Classées par sévérité : critique, moyen, mineur.

3. Couplages cachés
   "Modifier X implique de vérifier Y, Z, W".

4. Dette technique mesurable
   Ce qu'on peut réparer. Format : Décision actuelle, Coût actuel, Alternative, Coût estimé.

5. Bus factor et concentration des compétences
   Personnes et compétences. Définir "bus factor" en ouverture.

6. Limites et absences
   6.1 Limites produit assumées (choix de périmètre)
   6.2 Limites fonctionnelles non compensées
   6.3 Absences techniques
```

**Règle de placement : un item, un seul home. Pas de renvois entre sections.** Si un sujet est à la fois un piège et une dette réparable, il va en dette technique, et le piège opérationnel est décrit dans le "coût actuel".

---

## Niveaux de confiance

Pour chaque affirmation produite ou évaluée, distinguer trois niveaux :

- **Fait** : observable directement dans le code ou dans un document du dépôt.
- **Interprétation** : déduit du code mais pas explicite. À signaler dans le texte si doute.
- **Hypothèse** : incertain, à confirmer avec l'équipe. À signaler explicitement comme tel.

Règle d'or : si l'information n'est pas observable ou déductible du code, ne pas l'écrire dans la doc principale. Préférer le silence à la supposition. Pour `doc-technique-questions`, ces hypothèses sont au contraire une matière première : ce sont les angles morts à interroger.

---

## Données opérationnelles observées

Au-delà du code, un projet peut exposer des données opérationnelles mesurables, utiles à la doc. Exemples courants : historique d'exécution de pipelines CI/CD, dashboards de supervision, logs d'incident, tickets de support, statistiques d'usage, rapports de vulnérabilité de dépendances. Quand ces données sont accessibles, elles transforment des estimations en faits observés.

**Règle d'usage** :
- Les inclure uniquement quand elles sont réellement accessibles. Ne jamais inventer une donnée "réaliste".
- Les dater explicitement dans le texte. Exemple : "Sur 56 jours au 2026-04-17, disponibilité de 91%". Le lecteur sait alors que le chiffre est à rafraîchir.
- Citer la source de mesure et, si possible, la commande ou procédure qui permet de la régénérer. Exemple : "Extrait via `gh run list` le 2026-04-17".
- Préférer une fenêtre temporelle explicite à une valeur globale non bornée ("sur les 6 derniers mois" plutôt que "en général").

**Où placer ces données** :
- **Doc principale 1.3 (Chiffres clés)** : les agrégats headline (disponibilité, fréquence d'incidents, durée moyenne d'exécution).
- **Doc principale 5.3 (Processus qualité)** : les détails des mesures opérationnelles (distribution des durées, clusters d'échec, taux de vulnérabilités ouvertes).
- **Doc PA section 2 (Fragilités externes)** : remplacer les "probabilités estimées" par des "probabilités observées" quand un incident passé a matérialisé la fragilité.

**Si ces données n'existent pas ou ne sont pas accessibles** : le signaler dans la doc (plutôt que de laisser un silence ambigu), et le noter comme angle mort à interroger dans `doc-technique-questions`. Ne pas bloquer la production de la doc pour autant.

---

## Règles de ton (s'appliquent aux trois skills)

- Langage accessible. Chaque terme technique est expliqué à sa première occurrence, ou évité quand un mot plus simple suffit. Pièges fréquents : "headless", "fallback", "scraping", "cron", "bus factor", "ICU", "DST".
- Ton factuel. Pas de jugement de valeur ("propre", "élégant", "bien pensé", "bonne pratique", "intéressant"). Décrire ce qu'un choix permet ou implique, le lecteur tire ses conclusions.
- Pas de tiret long ni simple dans les phrases (utiliser virgule, point ou parenthèses). Autorisé dans les titres.
- Pas d'emojis.
- "Vous" dans les documents partageables, jamais "tu". Privilégier les formules impersonnelles ("Modifier X implique de vérifier Y") plutôt que "Si tu modifies X".
- Pas de mention des profils de lecteur dans le document produit (jamais "pour un CEO lire telle section").
- Schémas ASCII en disposition verticale par défaut. Disposition horizontale autorisée si plus parlante (cycle, comparaison côte à côte).
- Table des matières cliquable obligatoire dans les documents produits.

---

## Format des règles métier (utilisé dans `doc-technique` et lu par les autres skills)

**Format condensé (privilégier)** :
```
### [Nom de la règle, court et descriptif]
[Description en 2 à 4 lignes : ce que fait la règle, pourquoi, mécanisme.]
Voir [fichier ou fonction où le code se trouve].
```

**Format détaillé (pour règles avec cas limites non triviaux)** :
Garder le même style visuel que le format condensé : paragraphes fluides, pas de titres en gras de type "Ce que fait : / Pourquoi : / Comment : / Cas limites :". Pour une règle complexe, écrire 2 ou 3 paragraphes.

---

## Format des choix techniques

```
### Choix X : [nom]
**Le problème** : [ce que ce choix adresse]
**Le choix fait** : [ce qui a été implémenté]
**L'alternative** : [ce qui aurait pu être fait, pourquoi non retenu]
**La conséquence** : [ce que ça implique pour le système, factuel, observable]
```

3 à 5 choix majeurs. Pas de spéculation sur "qui a décidé" ou "quand", sauf si documenté dans un commit, une PR, un issue.

---

## Critère central de chaque ligne

Chaque ligne d'un document produit (doc principale, doc PA, fiche de questions, rapport de review) doit satisfaire l'un des deux tests :

1. Un lecteur compétent ne peut pas trouver cette information dans le code en moins de 5 minutes.
2. Cette information aide à comprendre, décider ou éviter un piège.

Si ni l'un ni l'autre, supprimer.

---

## Chaînage entre skills

```
              Code source du dépôt
                       │
                       ▼
              ┌──────────────────────┐
              │   doc-technique      │
              └──────────────────────┘
                       │
                       ▼
              ┌──────────────────────┐
              │ doc-technique-       │
              │ questions            │
              └──────────────────────┘
                       │
                       ▼
                Réponses interview
                       │
                       ▼
              ┌──────────────────────┐
              │   doc-technique      │
              │   en mode mise à jour│
              └──────────────────────┘
                       │
                       ▼
              ┌──────────────────────┐
              │ doc-technique-       │
              │ review               │
              └──────────────────────┘
```

Chaque skill termine en proposant le suivant naturel, sans l'imposer.
