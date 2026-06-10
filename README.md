# Plugin-copilot-camm-bac

Plugin Copilot pour l'intégralité de Camm Bac

Les plugins sont des packages installables qui étendent l'interface de ligne de commande GitHub Copilot avec des agents, des compétences, des hooks et des intégrations réutilisables.

# Qui peut utiliser cette fonctionnalité ?

L'interface de ligne de commande GitHub Copilot est disponible avec tous les abonnements Copilot. Si vous recevez Copilot d'une organisation, la stratégie d'utilisation de l'interface de ligne de commande Copilot doit être activée dans les paramètres de cette organisation.

Dans cet article :

Les plugins permettent de distribuer des fonctionnalités personnalisées pour l'interface de ligne de commande. Vous pouvez utiliser un plugin pour ajouter un ensemble de fonctionnalités préconfigurées à l'interface de ligne de commande Copilot.

# Qu'est-ce qu'un plugin ?

Un package distribuable qui étend les fonctionnalités de l'interface de ligne de commande Copilot.

Un ensemble de composants dans une seule unité installable. Que contiennent les plugins ?

Un plugin peut contenir tout ou partie des composants suivants :

## Agents personnalisés

Assistants IA spécialisés (fichiers \*.agent.md dans agents/)

## Skills

Fonctionnalités discrètes (sous-répertoires skills dans skills/, contenant un fichier SKILL.md)

## Hooks

Gestionnaires d’événements qui interceptent le comportement des agents (un fichier hooks.json à la racine du plugin ou dans hooks/)

## Configurations du serveur MCP

Intégrations du protocole MCP (un fichier .mcp.json à la racine du plugin ou dans .github/)

Configurations du serveur LSP — Intégrations du protocole LSP (un fichier lsp.json à la racine du plugin ou dans .github/)

# Pourquoi utiliser des plugins ?

Les plugins offrent les avantages suivants :

Réutilisation entre projets
Standardisation de la configuration de l'interface de ligne de commande (CLI) au sein de l'équipe
Partage d'expertise (par exemple, en mettant à disposition les compétences d'un expert Rails ou Kubernetes)
Encapsulation des configurations complexes de serveurs MCP

# Où trouver des plugins ?

Vous pouvez installer des plugins depuis :

Une place de marché
Un dépôt
Un répertoire local
Une place de marché est un espace où les développeurs peuvent publier, découvrir, installer et gérer des plugins. C'est un peu comme une boutique d'applications, mais pour les plugins.

## Exemples de places de marché :

copilot-plugins (ajouté par défaut)
awesome-copilot (ajouté par défaut)
claude-code-plugins
claudeforge-marketplace
Pour en savoir plus sur l'ajout de places de marché et l'installation de plugins depuis celles-ci, consultez la section « Recherche et installation de plugins pour l'interface de ligne de commande GitHub Copilot ».

Les administrateurs d'entreprise peuvent définir des normes de plugins applicables aux utilisateurs du plan Copilot de l'entreprise, notamment en spécifiant des places de marché et des plugins supplémentaires installés automatiquement pour les utilisateurs de l'interface de ligne de commande Copilot (CLI). Voir À propos des normes de plugins gérées par l'entreprise.

# Comparaison des plugins et de la configuration manuelle

Toute fonctionnalité pouvant être ajoutée via un plugin peut également l'être en configurant manuellement la CLI Copilot, par exemple en ajoutant des profils d'agent personnalisés ou des serveurs MCP. Cependant, les plugins offrent plusieurs avantages par rapport à la configuration manuelle :

Fonctionnalité : Configuration manuelle dans un dépôt / Plugin

# Portée : Dépôt unique / Tout projet

# Partage : Copier/coller manuel de la commande `/plugin install`

# Gestion des versions : Historique Git / Versions des places de marché

# Découverte : Recherche dans les dépôts / Navigation dans les places de marché
