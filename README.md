# 📚 Claude Code

> Guide complet pour maîtriser **Claude Code**, l'assistant IA en ligne de commande d'Anthropic.

---

## 📑 Sommaire

- [⚙️ Configuration](#️-configuration)
  - [Scopes](#scopes)
  - [Priorité](#priorité)
  - [Architecture des fichiers](#architecture-des-fichiers)
- [🎯 Concepts](#-concepts)
  - [🔄 Modes](#-modes)
  - [📝 CLAUDE.md](#-claudemd)
  - [🤖 SubAgents](#-subagents)
  - [✨ Skills](#-skills)
  - [🪝 Hooks](#-hooks)
  - [⌨️ Commands](#️-commands)
  - [🔌 MCP](#-mcp)
  - [📦 Plugins](#-plugins)
  - [🛠️ Tools](#️-tools)
  - [🧠 Context / Memory](#-context--memory)
- [🎪 Méthodologies](#-méthodologies)
  - [BMAD Method](#bmad-method)
  - [Task Master](#task-master)
- [📖 Aller plus loin](#-aller-plus-loin)

---

## ⚙️ Configuration

### Scopes

Claude Code utilise un système de configuration à plusieurs niveaux (scopes), du plus global au plus spécifique.

| Scope | Portée | Partagé | Emplacement |
|-------|--------|---------|-------------|
| **Managed** | Machine entière | Non | `/Library/Application Support/ClaudeCode/` (macOS) |
| **User** | Tous vos projets | Non | `~/.claude/` |
| **Project** | Équipe du projet | Oui (git) | `monProjet/.claude/` |
| **Local** | Vous seul, ce projet | Non (gitignored) | `monProjet/.claude/*.local.*` |

#### Managed

Configuration système déployée par les administrateurs IT. Priorité maximale, ne peut pas être surchargée.

- macOS : `/Library/Application Support/ClaudeCode/managed-settings.json`
- Linux/WSL : `/etc/claude-code/managed-settings.json`

#### User

Configuration personnelle globale, appliquée à tous vos projets.

```
~/.claude/settings.json
```

#### Project

Configuration partagée avec l'équipe, versionnée dans git.

```
monProjet/.claude/settings.json
```

#### Local

Configuration personnelle pour un projet spécifique, ignorée par git.

```
monProjet/.claude/settings.local.json
```

### Priorité

Lorsqu'un même paramètre existe dans plusieurs scopes, le plus spécifique l'emporte :

```
Managed > Local > Project > User
```

> **Exemple :** Un outil `allow` dans **User** mais `deny` dans **Project** sera interdit.

### Architecture des fichiers

**Configuration globale (`~/.claude/`) :**

```
~/.claude/
├── settings.json      # Configuration globale
├── CLAUDE.md          # Instructions globales
├── commands/          # Commandes personnalisées
├── agents/            # SubAgents personnalisés
└── statuslines/       # Scripts de barre de statut
```

**Configuration projet (`monProjet/`) :**

```
monProjet/
├── .mcp.json                  # Serveurs MCP (partagé)
├── CLAUDE.md                  # Instructions projet (partagé)
└── .claude/
    ├── settings.json          # Configuration équipe (git)
    ├── settings.local.json    # Configuration perso (gitignored)
    ├── CLAUDE.local.md        # Instructions perso (gitignored)
    ├── commands/              # Commandes projet
    ├── agents/                # SubAgents projet
    ├── skills/                # Skills projet
    └── hooks/                 # Hooks projet
```

---

## 🎯 Concepts

Vue d'ensemble des concepts clés de Claude Code.

| Concept | Nature | Utilité |
|---------|--------|---------|
| [🔄 Modes](#-modes) | Modes d'exécution | Contrôler l'autonomie de Claude |
| [📝 CLAUDE.md](#-claudemd) | Fichier d'instructions | Contexte persistant du projet |
| [🤖 SubAgents](#-subagents) | Agents spécialisés | Déléguer des tâches complexes |
| [✨ Skills](#-skills) | Templates de prompts | Standardiser des workflows |
| [🪝 Hooks](#-hooks) | Scripts automatiques | Réagir aux événements |
| [⌨️ Commands](#️-commands) | Commandes slash | Raccourcis pour prompts fréquents |
| [🔌 MCP](#-mcp) | Protocole externe | Connecter APIs et services |
| [📦 Plugins](#-plugins) | Extensions | Packager et partager |
| [🛠️ Tools](#️-tools) | Outils intégrés | Interagir avec l'environnement |
| [🧠 Context](#-context--memory) | Mémoire de session | Gérer le contexte |

---

### 🔄 Modes

Claude Code propose trois modes d'exécution, accessibles via `Shift+Tab`.

| Mode | Indicateur | Comportement |
|------|------------|--------------|
| **Normal** | _(aucun)_ | Confirmation requise avant chaque action |
| **Auto-Accept** | `⏵⏵ accept edits on` | Éditions de fichiers auto-acceptées |
| **Plan** | `⏸ plan mode on` | Lecture seule — analyse et planification |

**Cycle de navigation :**

```
Normal → Shift+Tab → Auto-Accept → Shift+Tab → Plan → Shift+Tab → Normal
```

#### Plan Mode

Mode recherche et planification en lecture seule. Claude observe, analyse et propose un plan sans modifier de fichiers.

**Outils disponibles :**
- `Read`, `Glob`, `Grep` — Lecture de fichiers
- `Task` — Agents de recherche
- `WebFetch`, `WebSearch` — Analyse web
- `TodoRead`, `TodoWrite` — Gestion des tâches
- `AskUserQuestion` — Clarification des besoins

**Workflow recommandé :**
1. Activer Plan Mode (`Shift+Tab` ×2)
2. Claude explore le codebase et pose des questions
3. Claude génère un plan structuré
4. Valider le plan
5. Passer en Auto-Accept pour l'exécution

#### Auto-Accept Mode

Accepte automatiquement les éditions de fichiers. Idéal après validation d'un plan.

> ⚠️ **Note :** Seules les éditions sont auto-acceptées, pas toutes les actions.

---

### 📝 CLAUDE.md

Fichier markdown chargé automatiquement au démarrage de chaque session. Il donne à Claude le contexte de votre projet.

**Emplacement :** `CLAUDE.md` ou `.claude/CLAUDE.md`

**Exemple :**

```markdown
# Mon Projet

## Stack
- Node.js + TypeScript
- PostgreSQL

## Conventions
- 2 espaces d'indentation
- Variables en camelCase

## Commandes
- Tests : `npm run test`
- Build : `npm run build`
```

---

### 🤖 SubAgents

Agents autonomes spécialisés avec leur propre contexte, outils et modèle.

**Emplacement :** `.claude/agents/mon-agent.md`

**Exemple :**

```yaml
---
name: reviewer
description: Revue de code qualité et sécurité
tools: Read, Grep, Glob
model: sonnet
---

Tu es un expert en revue de code.

1. Analyser les changements avec `git diff`
2. Évaluer qualité et sécurité
3. Produire un rapport (critique, warning, suggestion)
```

**Invocation :**

```
> Utilise l'agent reviewer pour analyser mes changements
```

---

### ✨ Skills

Templates de prompts réutilisables, invocables via `/nom-du-skill` ou automatiquement par Claude.

**Emplacement :** `.claude/skills/mon-skill/SKILL.md`

**Exemple :**

```yaml
---
name: fix-issue
description: Corriger un issue GitHub
argument-hint: [numéro-issue]
allowed-tools: Read, Grep, Edit, Bash
---

Corrige l'issue GitHub #$ARGUMENTS :

1. Lire la description de l'issue
2. Localiser le code concerné
3. Appliquer le correctif
4. Lancer les tests
```

**Invocation :**

```
/fix-issue 123
```

---

### 🪝 Hooks

Scripts exécutés automatiquement sur des événements du cycle de vie de Claude.

**Configuration :** `.claude/settings.json` (clé `hooks`)

**Événements disponibles :**

| Événement | Déclencheur |
|-----------|-------------|
| `SessionStart` | Démarrage de session |
| `UserPromptSubmit` | Envoi d'un prompt |
| `PreToolUse` | Avant l'exécution d'un outil |
| `PostToolUse` | Après l'exécution d'un outil |
| `Stop` | Fin de session |

**Exemple — Formatage automatique :**

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "npx prettier --write $CLAUDE_FILE_PATH"
          }
        ]
      }
    ]
  }
}
```

---

### ⌨️ Commands

Commandes slash personnalisées (fusionnées avec les Skills).

**Emplacement :** `.claude/commands/ma-commande.md`

**Exemple :**

```markdown
---
name: deploy
description: Déployer l'application
---

Déployer $ARGUMENTS :

1. Lancer les tests : `npm run test:ci`
2. Build production : `npm run build`
3. Push et tag release
```

**Invocation :**

```
/deploy production
```

---

### 🔌 MCP

**Model Context Protocol** — Connecte Claude à des serveurs externes (bases de données, APIs, SaaS).

**Configuration :** `.mcp.json` (racine du projet)

**Exemple :**

```json
{
  "mcpServers": {
    "postgres": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@bytebase/dbhub"],
      "env": {
        "DSN": "postgresql://user:pass@localhost:5432/mydb"
      }
    },
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    }
  }
}
```

**Utilisation :**

```
> Quels sont les 10 derniers utilisateurs inscrits ?
> Montre-moi les issues ouvertes sur GitHub
```

---

### 📦 Plugins

Packages distribuables regroupant skills, agents, hooks et serveurs MCP.

**Structure :**

```
mon-plugin/
├── .claude-plugin/
│   └── plugin.json    # Manifeste
├── skills/
├── agents/
└── .mcp.json
```

**Manifeste (`.claude-plugin/plugin.json`) :**

```json
{
  "name": "mon-plugin",
  "description": "Plugin d'audit de code",
  "version": "1.0.0"
}
```

**Utilisation :**

```bash
claude --plugin-dir ./mon-plugin
```

---

### 🛠️ Tools

Outils intégrés à Claude Code pour interagir avec votre environnement.

| Outil | Description |
|-------|-------------|
| `Read` | Lire un fichier |
| `Write` | Créer ou écraser un fichier |
| `Edit` | Modifier une partie d'un fichier |
| `Glob` | Trouver des fichiers par pattern |
| `Grep` | Rechercher dans le contenu |
| `Bash` | Exécuter des commandes shell |
| `WebFetch` | Récupérer du contenu web |
| `WebSearch` | Rechercher sur le web |
| `Task` | Lancer des sous-agents |

> Claude choisit automatiquement les outils selon votre demande.

---

### 🧠 Context / Memory

Gestion du contexte de conversation. Le contexte est limité et se compacte automatiquement.

**Commandes utiles :**

| Commande | Action |
|----------|--------|
| `/context` | Afficher l'utilisation du contexte |
| `/compact` | Compacter manuellement |
| `/clear` | Réinitialiser la session |

**Bonnes pratiques :**
- Utiliser `CLAUDE.md` pour le contexte persistant
- Découper les tâches complexes en sessions courtes
- Utiliser les SubAgents pour isoler les traitements lourds

---

## 🎪 Méthodologies

Approches structurées pour le développement assisté par IA.

### BMAD Method

**Breakthrough Method for Agile AI-Driven Development**

Méthodologie de planification où des agents spécialisés collaborent pour produire des spécifications détaillées avant le code.

**Principe clé — Document Sharding :**

Au lieu de fournir un PRD de 50 pages, BMAD découpe la documentation en fragments atomiques facilement digestibles par l'IA. Cela évite les hallucinations et maintient la cohérence.

**Workflow :**

| Étape | Agent | Livrable |
|-------|-------|----------|
| 1 | **Analyst** | Analyse des besoins |
| 2 | **PM** | PRD détaillé |
| 3 | **Architect** | Architecture technique |
| 4 | **Scrum Master** | User stories hyper-détaillées |
| 5 | **Dev** | Implémentation |

**Avantages :**
- Réduit les erreurs de contexte
- Optimise les coûts API
- Produit des specs réutilisables

**Ressources :**
- [BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) — Repo officiel
- [BMAD-AT-CLAUDE](https://github.com/24601/BMAD-AT-CLAUDE) — Port pour Claude Code

---

### Task Master

**Claude Task Master**

Système de gestion de tâches piloté par IA qui transforme des prompts en PRD structuré, découpé en tâches séquencées.

**Fonctionnalités :**

| Feature | Description |
|---------|-------------|
| **PRD Parsing** | Génère des tâches depuis un document de requirements |
| **Séquençage** | Détermine dépendances et priorités |
| **Analyse de complexité** | Identifie les tâches à découper |
| **Multi-modèles** | Différents modèles pour différents jobs |

**Impact :** Réduit jusqu'à 90% des erreurs en structurant le travail.

**Ressources :**
- [claude-task-master](https://github.com/eyaltoledano/claude-task-master) — Repo officiel
- [Tutorial](https://github.com/eyaltoledano/claude-task-master/blob/main/docs/tutorial.md)

---

### BMAD + Task Master

Ces approches sont complémentaires :

| Méthode | Excellence |
|---------|------------|
| **BMAD** | Planification stratégique (PRD, architecture) |
| **Task Master** | Gestion opérationnelle des tâches |

Combinés : BMAD produit les specs, Task Master les transforme en tâches exécutables.

---

## 📖 Aller plus loin

### 📚 Documentation de ce dépôt

- **[Guide Avancé](./AVANCE.md)** — Permissions, modèles, CLI, optimisation des coûts
- **[Settings Templates](./settings/README.md)** — Configurations prêtes à l'emploi par stack
- **[Agents](./.claude/agents/README.md)** — SubAgents spécialisés (backend, Symfony, Laravel, Next.js)
- **[Commands](./.claude/commands/README.md)** — Commandes slash (/commit, /test, /lint, /review-pr)
- **[Skills](./.claude/skills/README.md)** — Templates de prompts (code-review, debugging)
- **[Hooks](./.claude/hooks/README.md)** — Scripts automatiques (conventional commits)

### 🔗 Ressources externes

- [Documentation officielle Claude Code](https://docs.anthropic.com/claude-code)
- [GitHub Claude Code](https://github.com/anthropics/claude-code)
