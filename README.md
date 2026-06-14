# Plugin-copilot-camm-bac

Plugin-copilot-camm-bac est un helper de configuration AI copilot pour l'equipe de Camm Bac

Les plugins sont des packages installables qui étendent l'interface de ligne de commande GitHub Copilot avec des agents, des compétences, des hooks et des intégrations réutilisables.

Nouveau avec Copilot ? [suivre la doc](https://awesome-copilot.github.com/learning-hub/cli-for-beginners/)

# 💡Prerequis

0. Utiliser `copilot cli` ( dans VSCode ou pas ) plutot que le chat copilot pour des raison de performances.
1. dans un terminal copilot :
   avant de commencer les étapes activer les permissions avec `/yolo` ou `/allow-all`
   çà permettra a copilot de travailer en [autopilot](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/autopilot)
   pour eviter les arrets prompts.- ( best practices github !)
2. Pour que l'experience du plugin fonctionne bien , il faut au prealable
   creer un fichier `.github/copilot-instructions` qui va centraliser le context de l'application .
3. On peut le creer via le fichier `/init`. Copilot va parcourir la code base si existante puis creer le context.
4. install skill-creator d'antropic
5. lancer un [`/plan`](https://docs.github.com/en/copilot/how-tos/copilot-cli/cli-best-practices#2-plan-before-you-code)

## Best Practices

1.  Explore -> Plan(autopilot) -> Ask confirmation -> implement->review
2.  Write code -> screen result-> Iterer sur le feedback
3.  Partage sa session dans un gist perso github : `/share gist session`
4.  travailler en parrallele :

```/fleet actions a faire sur le projet el parallele:
    1. lance le skill camm-bac:review sur la code base
    2. lance le skill camm-bac:test pour test pass sur la code base
	  3. lance le skill camm-bac:docker pour conteneur le projet
```

5. Copilot CLI sessions history, Use `/session` or `/context` et `/review` apres une large iteraion et `/diff` pour inspecter les chnagesç
6. lancer le skill d'iteration autonome permettant le raffinage du code avant revue `/loop`

# Installation

- ajout de la marketplace
  `/plugin marketplace add  michaelsoisson-ext-dev/plugin-copilot-camm-bac`

- voir les plugins disponibles
  `/plugin marketplace browse orange-camm-bac`

- Install du plugin concerné
  `/plugin install camm-bac@orange-camm-bac`

- Update du plugin concerné

  `/plugin update camm-bac@orange-camm-bac`

- commande `/skills` pour lister les compétences installées.
- commande `/skills reload ` apres un update du plugin dans la session

- commande `/agent` pour choisir le nouvel agent 'ai-backend-js-dev.

## Path d'installation

Les skill \*-camm-bac sont installés via Copilot (pas via .agents) :

`/home/michael/.copilot/installed-plugins/orange-camm-bac/camm-bac/skills/code-camm-bac/SKILL.md`

Ce plugin orange-camm-bac est chargé différemment des skills installé dans .agents/skills/ c'est pourquoi il apparait avec le type plugin dans la liste des skills disponibles.

## 🚀 Usage

Le plugin a été crée avec l'aide de l'instruction copilot starter ( awesome copilot )

2. Testez un skill :

| Dans VS Code ou votre éditeur compatible Copilot

Ouvrez un fichier et demandez :

`/camm-bac:code Create a user management component`

3. Utilisez un agent :

Demandez un agent spécialisé

````

@backend-dev-camm-bac Build a new authentication endpoint
@reviewer-camm-bac Review this pull request code
@debugger-dev-camm-bac Debug this timeout error

```

# Qu'est-ce qu'un plugin ?

Un package distribuable qui étend les fonctionnalités de l'interface de ligne de commande Copilot.

Un ensemble de composants dans une seule unité installable. Que contiennent les plugins ?

Un plugin peut contenir tout ou partie des composants suivants :

## Agents personnalisés

Persona ou [assistants IA spécialisés](https://awesome-copilot.github.com/learning-hub/agents-and-subagents/) (fichiers \*.agent.md dans agents/)

## Skills

[Tâche dédiée](https://awesome-copilot.github.com/learning-hub/creating-effective-skills/) ou Fonctionnalités discrètes (sous-répertoires skills dans skills/, contenant un fichier SKILL.md)

la bonne de pratique est d'utiliser le [skill creator](https://github.com/anthropics/skills/blob/main/skills/skill-creator/SKILL.md) d'antropic pour creer les skills

## Hooks

[Gestionnaires d’événements](https://awesome-copilot.github.com/learning-hub/automating-with-hooks/) qui interceptent le comportement des agents (un fichier hooks.json à la racine du plugin ou dans hooks/)

Essential Slash Commands
These commands work in interactive mode. Start with just these six - they cover 90% of daily use:

Command What It Does When to Use
/help Show all available commands When you forget a command
/clear Clear conversation and start fresh When switching topics
/plan Plan your work out before coding For more complex features
/research Deep research using GitHub and web sources When you need to investigate a topic before coding
/model Show or switch AI model When you want to change the AI model
/exit End the session When you’re done

## Configurations du serveur MCP

Intégrations du protocole MCP (un fichier .mcp.json à la racine du plugin ou dans .github/)

Configurations du serveur LSP — Intégrations du protocole LSP (un fichier lsp.json à la racine du plugin ou dans .github/)

# Pourquoi utiliser des plugins ?

Les plugins offrent les avantages suivants :

Réutilisation entre projets
Standardisation de la configuration de l'interface de ligne de commande (CLI) au sein de l'équipe
Partage d'expertise (par exemple, en mettant à disposition les compétences d'un expert Rails ou Kubernetes)
Encapsulation des configurations complexes de serveurs MCP

📚 Où trouver des plugins ?

Vous pouvez installer des plugins depuis :

- [awsome copilot](https://awesome-copilot.github.com/)
- [awsome copilot plugins](https://awesome-copilot.github.com/plugins/)
- claude-code-plugins
  Une place de marché est un espace où les développeurs peuvent publier, découvrir, installer et gérer des plugins. C'est un peu comme une boutique d'applications, mais pour les plugins.

### Apprentissage:

- [Hub awesome copilot](https://awesome-copilot.github.com/learning-hub/)
- [Workshop copilot](https://copilot-dev-days.github.io/)
- [Workshop copilot video](https://www.youtube.com/playlist?list=PL0lo9MOBetEGEAvoSZiPwZ7FqVEQcJWrM)

### Comparaison des plugins et de la configuration manuelle

Toute fonctionnalité pouvant être ajoutée via un plugin peut également l'être en configurant manuellement la CLI Copilot, par exemple en ajoutant des profils d'agent personnalisés ou des serveurs MCP. Cependant, les plugins offrent plusieurs avantages par rapport à la configuration manuelle :

Fonctionnalité : Configuration manuelle dans un dépôt / Plugin

---
```
````
