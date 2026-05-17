---
name: doc-technique-review
description: Relit une documentation technique existante (produite par /doc-technique ou par un tiers) et produit un rapport de critique structuré sur six axes (couverture, exactitude, lisibilité, séparation factuel/jugement, adaptation au contexte, densité et concision). Invoquer quand on reçoit une doc à valider, quand on prépare une diffusion à un auditeur, ou avant de signer un livrable produit par un prestataire.
---

# Review d'une documentation technique

Relit une documentation technique existante et produit un rapport de critique structuré, avec une liste actionable de corrections à apporter.

**Pré-requis** : lire d'abord `C:\Users\monta\.claude\skills\doc-technique\principes-communs.md` pour le vocabulaire, la structure de référence, les règles de ton et les niveaux de confiance.

**Argument** :
- Chemin vers une doc principale, OU
- Chemin vers une doc principale + chemin vers un doc points d'attention, OU
- Chemin vers une doc + chemin vers le dépôt de code (privilégié pour la vérification croisée).

Si rien n'est fourni, demander.

---

## 1. Pourquoi ce skill existe

Une doc technique peut être :
- **Produite par soi** (avec `/doc-technique`) : utile de la relire à froid avant diffusion.
- **Produite par un prestataire ou un tiers** : doit être validée avant signature.
- **Reçue dans le cadre d'un audit, d'une due diligence, d'un onboarding** : doit être évaluée pour décider si elle suffit ou si on demande des compléments.
- **Vieillie** : la doc d'il y a 18 mois ne reflète plus le code, à confronter pour identifier les zones obsolètes.

Dans les quatre cas, on a besoin d'une critique structurée, pas d'un avis général. Ce skill produit cette critique.

---

## 2. Six axes d'évaluation

Pour chaque axe, parcourir le document et noter les écarts. Chaque écart devient une ligne du rapport.

### Axe 1 : Couverture

La doc couvre-t-elle les sections attendues ? Comparer à la structure de référence définie dans `principes-communs.md`.

**Vérifier** :
- Les 5 sections du doc principal sont-elles présentes (Vue d'ensemble, Architecture, Règles métier, Choix techniques, Conditions d'exploitation) ?
- Le glossaire est-il présent ?
- Si un doc points d'attention existe, ses 6 sections sont-elles présentes (Pièges, Fragilités externes, Couplages cachés, Dette technique, Bus factor, Limites et absences) ?
- Y a-t-il des sections manquantes ? Y a-t-il des sections vides ou trop pauvres (moins de 3 lignes) ?
- Les sous-sections clés sont-elles toutes là (1.3 Chiffres clés, 5.1 Dépendances et licences, etc.) ?

### Axe 2 : Exactitude factuelle

C'est l'axe critique. Si possible, croiser avec le code.

**Vérifier** :
- Chaque chiffre est-il vérifiable dans le code ? Recompter si nécessaire (lignes de code, nombre de fichiers, nombre de contributeurs, fréquence des cron, etc.).
- Chaque affirmation technique est-elle observable dans le code ?
- Les noms de fichiers, de fonctions, de classes mentionnés existent-ils encore (renommages, suppressions) ?
- Les dépendances mentionnées avec leur version sont-elles à jour vs le `package.json`, `Gemfile`, `csproj`, `update.cs` ?
- Les diagrammes reflètent-ils les connexions réelles ?
- Y a-t-il des affirmations qui contredisent le code (ex : "migration terminée" alors que des appels obsolètes existent encore) ?

Pour les hypothèses signalées (niveau "Hypothèse" ou "à confirmer") : les noter, ne pas les compter comme des erreurs.

### Axe 3 : Lisibilité et accessibilité

La doc est-elle compréhensible par sa cible ?

**Vérifier** :
- Les termes techniques sont-ils expliqués à leur première occurrence ? Pièges fréquents : "headless", "fallback", "scraping", "cron", "bus factor", "ICU", "DST", "CDN".
- Le ton est-il homogène (pas d'expression familière isolée comme "ça mord", "y a", "ya plus qu'à") ?
- L'usage de "tu" / "vous" est-il cohérent ? Privilégier les formules impersonnelles.
- Y a-t-il des tirets longs (—) ou simples (-) dans les phrases (hors titres) ?
- La table des matières est-elle cliquable ?
- Les schémas ASCII sont-ils lisibles (verticaux par défaut, horizontaux uniquement si plus parlant) ?
- La progression du général au particulier est-elle respectée (un non-expert peut-il lire 1, puis 2, puis 3) ?

### Axe 4 : Séparation factuel / jugement

Le doc principal doit être strictement factuel. Le doc PA absorbe tout ce qui est nuancé.

**Vérifier dans le doc principal** :
- Y a-t-il des jugements de valeur ? Chercher "propre", "bien", "élégant", "intéressant", "bonne pratique", "innovant", "performant".
- Y a-t-il des recommandations ou des "à améliorer" qui devraient être dans le doc PA ?
- Y a-t-il une section "Limitations" placée dans la vue d'ensemble (à déplacer en section 6 du doc PA) ?
- Y a-t-il des suppositions non étayées (volumes, coûts, intentions des contributeurs) ?

**Vérifier dans le doc PA** :
- Chaque item a-t-il un seul home (pas de redondance entre "piège" et "fragilité") ?
- Le bus factor est-il défini en ouverture pour un lecteur non initié ?
- Les fragilités externes sont-elles bien externes (cause hors contrôle) et non confondues avec de la dette technique réparable ?

### Axe 5 : Adaptation au contexte

À qui s'adresse cette doc, et est-elle calibrée pour ce public ?

**Identifier le contexte** :
- Onboarding nouveau dev → la doc doit être pédagogique, glossaire enrichi, parcours de lecture suggéré
- Audit interne → focus sur la traçabilité et les angles morts identifiés
- Due diligence externe → exactitude maximale, preuves référencées, red flags reconnus
- Dossier réglementaire → références croisées vers les normes (IEC, ISO, MDR), traçabilité requirement → code

**Vérifier l'adéquation** :
- Le niveau de détail est-il cohérent avec le contexte (ni trop léger pour un audit, ni trop dense pour un onboarding) ?
- Les sections critiques pour le contexte sont-elles bien développées ?
- Y a-t-il des sections inutiles pour ce contexte qui pourraient être condensées ?

### Axe 6 : Densité et concision

La doc est-elle aussi courte qu'elle peut l'être sans perdre d'information utile ? Cet axe est souvent le plus négligé, et c'est lui qui fait la différence entre une doc lue et une doc abandonnée à la moitié.

**Vérifier les redondances** :
- Un même sujet est-il abordé dans plusieurs sections sans valeur ajoutée ? (ex : une règle décrite à la fois en section 3 et en section 4)
- Une même fragilité est-elle présente à la fois dans "Pièges" et dans "Fragilités externes" du doc PA ? (rappel de la règle "un item, un seul home")
- Le glossaire répète-t-il des définitions déjà données dans le corps du document ?
- Les diagrammes répètent-ils ce qui est dit dans le texte autour ?

**Vérifier la longueur inutile** :
- Y a-t-il des paragraphes qui paraphrasent le code (visible par `ls` ou par lecture du fichier en moins de 5 minutes) ?
- Y a-t-il des listes interminables (15 items quand 8 suffisent) ? Appliquer le test "si je supprime cet item, est-ce que quelqu'un perd 2 heures à comprendre ?".
- Y a-t-il des formulations creuses : "permet de X", "gère X", "prend en charge X" sans la mécanique concrète qui suit ?
- Y a-t-il des répétitions structurelles (chaque règle commence par "Cette règle décrit comment...") qui pourraient être supprimées ?
- Y a-t-il des transitions inutiles ("Comme nous l'avons vu plus haut...", "Nous allons maintenant aborder...") ?

**Mesurer la densité par section** :
- Compter le ratio nombre de lignes vs nombre d'informations non triviales.
- Une section 3 qui fait 100 lignes pour 4 règles utiles est mal calibrée. Une section 4 qui fait 30 lignes pour 5 choix bien décrits est dense.
- Comparer les longueurs cibles du calibrage du skill `doc-technique` (section 9) à la longueur réelle.

**Test final ligne par ligne** :
Pour chaque ligne du document, vérifier qu'elle satisfait au moins l'un des deux tests centraux (définis dans `principes-communs.md`) :
1. Un lecteur compétent ne peut pas trouver cette information dans le code en moins de 5 minutes.
2. Cette information aide à comprendre, décider ou éviter un piège.

Toute ligne qui ne satisfait ni l'un ni l'autre est candidate à la suppression.

---

## 3. Format du rapport produit

```markdown
# Review de [nom du document], rapport

> **Document relu** : [chemin]
> **Date de review** : [date]
> **Code croisé** : [oui, chemin / non]
> **Verdict global** : [acceptable en l'état / acceptable avec corrections mineures / corrections majeures requises / à reprendre]

## Résumé exécutif

3 à 5 lignes. Le verdict, les points forts, les écarts majeurs.

## Forces

3 à 5 points positifs concrets et factuels. Pas de flatterie. Si la doc n'a pas de force notable, ne pas inventer.

## Écarts par axe

### Axe 1, Couverture

| Sévérité | Section | Écart | Suggestion |
|---|---|---|---|
| critique | Section 5.1 | Tableau des dépendances absent | Ajouter le tableau avec versions et licences |
| moyen | Section 2.3 | Sous-section "Déploiement" trop pauvre (2 lignes) | Détailler les workflows CI/CD et la stratégie de rollback |

### Axe 2, Exactitude factuelle

| Sévérité | Endroit | Écart | Vérification |
|---|---|---|---|
| critique | Section 1.3 | "5 contributeurs" vs `git log` qui en montre 8 | Recompter avec `git log --format="%an" --all | sort -u | wc -l` |

### Axe 3, Lisibilité et accessibilité

| Sévérité | Endroit | Écart | Correction |
|---|---|---|---|
| moyen | Section 2.2, ligne 45 | Terme "headless" non expliqué | Ajouter "(navigateur sans interface graphique)" |

### Axe 4, Séparation factuel / jugement

| Sévérité | Endroit | Écart | Action |
|---|---|---|---|
| moyen | Section 1.3 | "Pas de tests" formulé comme limitation produit dans le doc principal | Déplacer en section 6.3 du doc PA |

### Axe 5, Adaptation au contexte

[Tableau ou paragraphes selon le volume]

### Axe 6, Densité et concision

| Sévérité | Endroit | Écart | Suggestion |
|---|---|---|---|
| moyen | Section 3.2 et 4 | La règle de skip est décrite deux fois | Garder la version de la section 3.2, supprimer ou réduire en 4 |
| moyen | Section 2.4 | 25 lignes pour décrire un mécanisme trivial (html2canvas + FileSaver) | Réduire à 5 à 8 lignes |
| mineur | Plusieurs sections | Phrases d'ouverture redondantes ("Cette règle décrit...") | Supprimer |

## Questions ouvertes

Sujets que la doc ne tranche pas et qu'il vaut la peine de creuser via `/doc-technique-questions`.

## Recommandations priorisées

Liste numérotée, du plus critique au plus mineur. Format : "Action concrète + endroit du document".

1. [Action 1]
2. [Action 2]
...
```

---

## 4. Niveaux de sévérité

- **Critique** : compromet l'usage du document. Une erreur factuelle, une section absente, un jugement caché. À corriger avant diffusion.
- **Moyen** : nuit à la lecture mais ne fausse pas la compréhension. Une explication manquante, une formulation à ajuster, un schéma peu clair.
- **Mineur** : amélioration cosmétique. Une typo, un tiret hors titre, une formulation qui pourrait être plus directe.

Ne pas inventer des écarts pour gonfler le rapport. Si une catégorie n'a aucun écart, l'écrire explicitement : "Aucun écart identifié sur cet axe".

---

## 5. Verdict global

Cinq possibilités, à choisir en fonction du nombre et de la sévérité des écarts identifiés :

- **Acceptable en l'état** : 0 critique, 0 à 3 moyens, mineurs tolérés.
- **Acceptable avec corrections mineures** : 0 critique, jusqu'à 5 moyens.
- **À condenser** : pas d'erreur factuelle, structure correcte, mais axe 6 (densité) très dégradé. Le document est juste mais trop long, ce qui le rend peu lisible. Verdict spécifique pour signaler que le travail est de réduction, pas de complément.
- **Corrections majeures requises** : 1 à 3 critiques, ou plus de 5 moyens. Le document est utilisable mais doit être amendé avant diffusion.
- **À reprendre** : plus de 3 critiques, ou écarts structurels (sections absentes, ton entièrement inadapté). Le document ne peut pas être amendé section par section, il doit être réécrit.

Le verdict est apparent dans l'en-tête du rapport pour qu'un lecteur en saisisse l'enjeu en 5 secondes.

---

## 6. Déroulement

1. **Collecte** : lire le ou les documents à reviewer. Si le code est fourni, en faire une lecture rapide pour la vérification croisée.
2. **Lecture des principes communs** : ouvrir `principes-communs.md` pour s'aligner sur la structure et le vocabulaire de référence.
3. **Identification du contexte** : qui est la cible du document ? Onboarding, audit, DD, réglementaire ? Demander à l'utilisateur si ce n'est pas évident.
4. **Parcours par axe** : appliquer les 5 axes d'évaluation, noter chaque écart avec sa sévérité.
5. **Vérification croisée code (si code fourni)** : pour les écarts d'exactitude, ouvrir le fichier mentionné et confirmer.
6. **Rédaction du rapport** : appliquer le format de la section 3.
7. **Verdict** : choisir parmi les 4 possibilités.
8. **Présentation à l'utilisateur** : montrer le rapport, demander si certains écarts sont à requalifier (par exemple, un terme jugé non expliqué peut être en fait évident pour le public cible).
9. **Sauvegarde** : proposer `[date]-review-[nom-du-document].md`. Demander le chemin.

---

## 7. Mode "self-review"

Quand l'utilisateur vient juste de produire une doc avec `/doc-technique` et invoque `/doc-technique-review` immédiatement après, c'est un mode self-review. Particularités :

- Le code et la doc sont disponibles immédiatement.
- Les écarts d'exactitude sont peu probables (la doc vient d'être produite à partir du code), mais à vérifier quand même sur les chiffres.
- Les écarts de couverture, lisibilité, séparation factuel/jugement et ton sont les plus probables.
- Le verdict est rarement "à reprendre" : on cherche surtout les corrections moyennes et mineures.

Dans ce mode, présenter le rapport en se concentrant sur les corrections actionables, et proposer de les appliquer directement au document via `Edit`.

---

## 8. Chaînage avec les autres skills

- Si la review identifie des **questions ouvertes** ou des **hypothèses non confirmées**, proposer `/doc-technique-questions` pour préparer une interview de validation.
- Si la review aboutit à "à reprendre", proposer `/doc-technique` en mode complet pour redémarrer.
- Si la review aboutit à "corrections mineures", proposer d'appliquer les corrections directement, puis de relancer `/doc-technique-review` pour vérifier.

---

## 9. Calibrage selon le contexte

| Contexte | Effort attendu | Sévérité d'attention |
|---|---|---|
| Self-review après production | Rapide, focus sur la passe 2 du skill `doc-technique` | Tolérance aux mineurs, vigilance sur le ton et les chiffres |
| Review d'une doc d'un prestataire | Approfondi, croiser systématiquement avec le code | Vigilance maximale sur l'exactitude |
| Review en due diligence | Très approfondi, demander des preuves pour chaque affirmation | Aucune tolérance sur l'exactitude, vigilance sur ce qui n'est pas dit |
| Review d'une doc vieillie | Focus sur l'écart entre doc et code actuel | Identifier les zones obsolètes plutôt que les écarts de forme |
