# doc-technique-skills

Skills et commandes pour Claude Code qui permettent de produire de la documentation technique à partir du code source d'un dépôt, sans être développeur.

Cette boîte à outils accompagne le talk **"Créer la doc technique à partir du code, sans être dev ni embêter les devs"**, donné au meetup IA Innovateurs de Toulouse le 21 mai 2026.

> Voir le talk en ligne : **[montaine-consulting.fr/talks/doc-sans-dev](https://montaine-consulting.fr/talks/doc-sans-dev/)**

## L'idée en deux phrases

Quand on n'est pas développeur et qu'on a besoin de comprendre un code (PM, auditeur, nouveau arrivant, investisseur en due diligence), on perd des heures en allers-retours avec les devs. Ces skills permettent à un agent IA d'aller lire le code à votre place et de produire une documentation factuelle, structurée et lisible, en une fraction du temps.

## Ce qu'il y a dans ce repo

### Skills (dossier `skills/`)

| Skill | Ce qu'il fait |
|-------|---------------|
| `doc-technique` | Produit une documentation technique factuelle d'un dépôt, en 5 sections (vue d'ensemble, architecture, règles métier, choix techniques, conditions d'exploitation). Plus un document séparé pour les points d'attention. |
| `doc-technique-questions` | Génère une fiche de questions ciblées à poser au code owner pour combler les angles morts (intentions, décisions historiques) que le code seul ne révèle pas. |
| `doc-technique-review` | Relit une documentation technique existante (produite par `doc-technique` ou par un tiers) et produit un rapport de critique structuré sur six axes. C'est le "skill jumeau d'audit" mentionné dans le talk. |

### Commands (dossier `commands/`)

| Commande | Ce qu'elle fait |
|----------|-----------------|
| `chronologie-repo` | Produit une chronologie R&D structurée à partir de l'historique Git d'un dépôt. Pensée pour des lecteurs non techniques (auditeur fiscal, expert CIR/CII). C'est le skill utilisé dans la démo du talk. |

## Installation pour Claude Code

Ces fichiers sont prévus pour être utilisés avec [Claude Code](https://claude.com/claude-code).

1. Clonez ce repo.
2. Copiez les dossiers dans votre installation Claude Code :
   - `skills/*` vers `~/.claude/skills/` (Linux / Mac) ou `C:\Users\<vous>\.claude\skills\` (Windows)
   - `commands/*` vers `~/.claude/commands/` ou `C:\Users\<vous>\.claude\commands\`
3. Relancez Claude Code. Les skills apparaissent automatiquement.
4. Invoquez en chat : `/doc-technique`, `/chronologie-repo`, etc.

## Utilisation rapide

```
/doc-technique <chemin-vers-un-depot-git-local>
/chronologie-repo <chemin-vers-un-depot-git-local>
/doc-technique-review <chemin-vers-une-doc-existante>
/doc-technique-questions <chemin-vers-un-depot> [<chemin-vers-doc>]
```

Sans argument, le skill vous demandera le chemin.

## Adapter à votre contexte

Ces skills sortent d'un contexte précis : une PM en MedTech qui documente des dépôts pour des dossiers réglementaires. Vous trouverez donc des références à des chemins absolus Windows et à un dossier `5. Documents de travail/`.

Adaptez sans complexe :
- Modifiez les chemins pour votre OS et votre arborescence.
- Renommez les sections selon votre vocabulaire métier.
- Ajustez le ton et la structure pour votre public cible.

Ces skills valent ce qu'ils valent. Ils vaudront davantage retravaillés pour votre propre besoin.

## Les deux principes du talk

1. **Discovery d'abord, skill ensuite. Jamais l'inverse.** Avant de figer un skill, produisez 2 ou 3 fois le livrable à la main. Le skill est la cristallisation d'une méthode éprouvée, pas un point de départ.

2. **Pour chaque skill qui produit, un skill qui vérifie.** Ici : `doc-technique` produit, `doc-technique-review` audite. Les deux tournent en boucle jusqu'à convergence. Vous ne relisez plus que ce que l'audit remonte.

## Licence

[CC-BY 4.0](LICENSE) : utilisez, modifiez, diffusez, y compris pour un usage commercial. Citez simplement l'auteur et un lien vers ce repo.

## Auteur

Montaine Marteau, Chief Product Technical Officer à temps partagé, spécialisée MedTech.

- Site : [montaine-consulting.fr](https://montaine-consulting.fr)
- GitHub : [@MontaineMarteau](https://github.com/MontaineMarteau)

Si vous testez ces skills sur votre propre code, j'aimerais beaucoup avoir vos retours.
