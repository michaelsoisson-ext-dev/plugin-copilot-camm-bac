# Plugin-copilot-camm-bac

Plugin-copilot-camm-bac est un helper de configuration AI copilot pour l'equipe de Camm Bac

Les plugins sont des packages installables qui étendent l'interface de ligne de commande GitHub Copilot avec des agents, des compétences, des hooks et des intégrations réutilisables.

Nouveau avec Copilot ? [suivre la doc](https://awesome-copilot.github.com/learning-hub/cli-for-beginners/)

# Installation

dans un terminal copilot :

- ajout de la marketplace
  `/plugin marketplace add  michaelsoisson-ext-dev/plugin-copilot-camm-bac `

- voir les plugins disponibles
  `/plugin marketplace browse orange-camm-bac`

- Install du plugin concerné
  `/plugin install camm-bac@orange-camm-bac`

- commande `/skills` pour lister les compétences installées.

- commande `/agent` pour choisir le nouvel agent 'ai-backend-js-dev.

## 🚀 Usage

Le plugin a été crée avec l'aide de l'instruction copilot starter ( awesome copilot )

2. Testez un skill :

| Dans VS Code ou votre éditeur compatible Copilot

Ouvrez un fichier et demandez :

`@code-camm-bac Create a user management component`

3. Utilisez un agent :

Demandez un agent spécialisé

```
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

## Hooks

[Gestionnaires d’événements](https://awesome-copilot.github.com/learning-hub/automating-with-hooks/) qui interceptent le comportement des agents (un fichier hooks.json à la racine du plugin ou dans hooks/)

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
- [Hub apprentissage](https://awesome-copilot.github.com/learning-hub/)

Une place de marché est un espace où les développeurs peuvent publier, découvrir, installer et gérer des plugins. C'est un peu comme une boutique d'applications, mais pour les plugins.

## Exemples de places de marché :

copilot-plugins (ajouté par défaut)
awesome-copilot (ajouté par défaut)
claude-code-plugins
claudeforge-marketplace
Pour en savoir plus sur l'ajout de places de marché et l'installation de plugins depuis celles-ci, consultez la section « Recherche et installation de plugins pour l'interface de ligne de commande GitHub Copilot ».

Les administrateurs d'entreprise peuvent définir des normes de plugins applicables aux utilisateurs du plan Copilot de l'entreprise, notamment en spécifiant des places de marché et des plugins supplémentaires installés automatiquement pour les utilisateurs de l'interface de ligne de commande Copilot (CLI). Voir À propos des normes de plugins gérées par l'entreprise.

### Comparaison des plugins et de la configuration manuelle

Toute fonctionnalité pouvant être ajoutée via un plugin peut également l'être en configurant manuellement la CLI Copilot, par exemple en ajoutant des profils d'agent personnalisés ou des serveurs MCP. Cependant, les plugins offrent plusieurs avantages par rapport à la configuration manuelle :

Fonctionnalité : Configuration manuelle dans un dépôt / Plugin

---

### Prochaines étapes

1.  Setup Copilot dans VS Code : Installer l'extension GitHub Copilot
2.  Explorer les instructions : Lire .github/copilot-instructions.md
3.  Personnaliser : Adapter les instructions vos conventions
4.  Ajouter au team : Partager avec vos collegues
