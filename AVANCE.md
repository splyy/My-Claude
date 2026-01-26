# 🚀 Guide Avancé Claude Code

> Aller plus loin avec Claude Code : permissions, modèles, CLI et optimisation.

**Prérequis :** Avoir lu le [README.md](./README.md) pour les concepts fondamentaux.

---

## 📑 Sommaire

- [🔐 Permissions](#-permissions)
- [🧩 Modèles](#-modèles)
- [💻 Commandes intégrées](#-commandes-intégrées)
- [🔑 Variables d'environnement](#-variables-denvironnement)
- [⌨️ Raccourcis clavier](#️-raccourcis-clavier)
- [🤖 Mode Headless / CI](#-mode-headless--ci)
- [💰 Gestion des coûts](#-gestion-des-coûts)
- [📖 Ressources](#-ressources)

---

## 🔐 Permissions

Système de contrôle d'accès aux outils via `allow` et `deny` dans `settings.json`.

### Structure

```json
{
  "permissions": {
    "allow": [
      "Read",
      "Bash(git status)",
      "Bash(npm run:*)"
    ],
    "deny": [
      "Bash(rm -rf /)",
      "Bash(git push --force:*)"
    ]
  }
}
```

### Syntaxe des patterns

| Pattern | Description | Exemple |
|---------|-------------|---------|
| `Tool` | Outil complet | `Read`, `Write` |
| `Bash(cmd)` | Commande exacte | `Bash(git status)` |
| `Bash(cmd:*)` | Commande + arguments | `Bash(git log:*)` |
| `Bash(cmd *)` | Commande + arguments (alt) | `Bash(npm run *)` |

### Priorité des règles

1. Les règles `deny` sont **toujours prioritaires** sur `allow`
2. L'ordre d'évaluation suit la priorité des scopes :

```
Managed > Local > Project > User
```

### Bonnes pratiques

| Pratique | Raison |
|----------|--------|
| Commencer restrictif | Élargir selon les besoins réels |
| Toujours `deny` les destructives | `rm -rf`, `--force`, `--hard` |
| Patterns spécifiques | Éviter les autorisations trop larges |

---

## 🧩 Modèles

Claude Code supporte plusieurs modèles avec différents compromis performance/coût.

### Modèles disponibles

| Modèle | Caractéristiques | Usage recommandé |
|--------|------------------|------------------|
| **Haiku** | Très rapide, économique | Recherche, tâches simples |
| **Sonnet** | Équilibré | Développement courant |
| **Opus** | Puissant, coûteux | Architecture, tâches complexes |
| **Opus 4.5** | Le plus avancé | Plan Mode amélioré |

### Changer de modèle

```
/model
```

Menu interactif pour sélectionner le modèle.

### Configuration par défaut

```json
{
  "model": "claude-sonnet-4-20250514"
}
```

### Opus 4.5 Plan Mode

Le modèle Opus 4.5 offre un Plan Mode enrichi :

- Questions de clarification interactives
- Planification structurée détaillée
- Meilleure gestion des architectures complexes

Accessible via `/model` → option 4.

---

## 💻 Commandes intégrées

Commandes slash natives de Claude Code (distinctes des commands personnalisées).

### 📋 Session

| Commande | Description |
|----------|-------------|
| `/help` | Afficher l'aide |
| `/clear` | Réinitialiser la conversation |
| `/compact` | Compacter le contexte |
| `/context` | Voir l'utilisation du contexte |

### ⚙️ Configuration

| Commande | Description |
|----------|-------------|
| `/model` | Changer de modèle |
| `/config` | Ouvrir la configuration |
| `/permissions` | Gérer les permissions |

### 🧭 Navigation

| Commande | Description |
|----------|-------------|
| `/vim` | Activer le mode vim |
| `/status` | Afficher le statut git |

### 🐛 Debug

| Commande | Description |
|----------|-------------|
| `/bug` | Signaler un bug |
| `/terminal-setup` | Configurer le terminal |

---

## 🔑 Variables d'environnement

Variables disponibles dans les hooks, skills et commands.

### Variables Claude

| Variable | Description | Contexte |
|----------|-------------|----------|
| `$ARGUMENTS` | Arguments de la commande | Commands, Skills |
| `$CLAUDE_FILE_PATH` | Chemin du fichier concerné | Hooks |
| `$CLAUDE_TOOL_NAME` | Nom de l'outil appelé | Hooks |
| `$CLAUDE_TOOL_INPUT` | Input JSON de l'outil | Hooks |
| `$CLAUDE_TOOL_OUTPUT` | Output de l'outil | Hooks (Post) |

### Exemple — Hook

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Modifié: $CLAUDE_FILE_PATH'"
          }
        ]
      }
    ]
  }
}
```

### Exemple — Command

```markdown
---
name: search
description: Recherche dans le code
---

Rechercher "$ARGUMENTS" dans le codebase.
```

---

## ⌨️ Raccourcis clavier

### Navigation des modes

| Raccourci | Action |
|-----------|--------|
| `Shift+Tab` | Cycle : Normal → Auto → Plan |
| `Escape` | Annuler l'action en cours |
| `Ctrl+C` | Interrompre Claude |

### Édition du prompt

| Raccourci | Action |
|-----------|--------|
| `Ctrl+A` | Début de ligne |
| `Ctrl+E` | Fin de ligne |
| `Ctrl+U` | Effacer la ligne |
| `Ctrl+W` | Effacer le mot précédent |
| `Ctrl+L` | Clear terminal |

### Mode Vim

Activer avec `/vim`. Supporte les commandes vim standard pour l'édition du prompt.

---

## 🤖 Mode Headless / CI

Exécuter Claude Code en mode non-interactif pour l'automatisation.

### Usage basique

```bash
claude --print "Analyse ce fichier" < input.txt
```

### Options principales

| Option | Description |
|--------|-------------|
| `--print` / `-p` | Mode non-interactif |
| `--output-format json` | Sortie JSON |
| `--max-turns N` | Limite les itérations |
| `--allowedTools` | Outils autorisés |
| `--model` | Modèle à utiliser |

### Exemple — GitHub Actions

```yaml
- name: Code Review
  run: |
    claude --print "Review les changements de cette PR" \
      --allowedTools Read,Grep,Glob \
      --max-turns 5
```

### Variables d'environnement CI

| Variable | Description |
|----------|-------------|
| `ANTHROPIC_API_KEY` | Clé API (obligatoire) |
| `CLAUDE_CODE_USE_BEDROCK` | Utiliser AWS Bedrock |
| `CLAUDE_CODE_USE_VERTEX` | Utiliser Google Vertex |

---

## 💰 Gestion des coûts

Optimiser l'utilisation des tokens et maîtriser les coûts API.

### Facteurs de coût

| Facteur | Impact |
|---------|--------|
| **Modèle** | Opus >> Sonnet > Haiku |
| **Contexte** | Plus long = plus coûteux |
| **Turns** | Chaque aller-retour consomme |

### Stratégies d'optimisation

#### 1. Choisir le bon modèle

| Tâche | Modèle recommandé |
|-------|-------------------|
| Recherche, exploration | Haiku |
| Développement courant | Sonnet |
| Architecture complexe | Opus |

#### 2. Gérer le contexte

- `/compact` régulièrement pour réduire
- Nouvelles sessions pour nouvelles tâches
- `/context` pour surveiller l'usage

#### 3. Planifier avant d'exécuter

- Plan Mode réduit les erreurs coûteuses
- BMAD Method : front-load la planification

#### 4. SubAgents ciblés

- Isoler les tâches dans des agents dédiés
- Spécifier le modèle par agent :

```yaml
model: haiku  # Pour les tâches simples
```

#### 5. CLAUDE.md efficace

- Contexte persistant = moins de répétitions
- Concis mais complet

### Surveiller l'usage

```
/context
```

Affiche l'utilisation actuelle et les tokens consommés.

---

## 📖 Ressources

### Documentation officielle

- [Claude Code Docs](https://docs.anthropic.com/claude-code)
- [GitHub Claude Code](https://github.com/anthropics/claude-code)

### Méthodologies

- [BMAD Method](https://github.com/bmad-code-org/BMAD-METHOD)
- [Claude Task Master](https://github.com/eyaltoledano/claude-task-master)

### Ce dépôt

| Document | Description |
|----------|-------------|
| [README.md](./README.md) | Concepts fondamentaux Claude Code |
| [CLAUDE.md](./CLAUDE.md) | Instructions projet et structure du dépôt |
| [settings/](./settings/README.md) | Templates de configuration par stack |
| [agents/](./.claude/agents/README.md) | SubAgents spécialisés |
| [commands/](./.claude/commands/README.md) | Commandes slash personnalisées |
| [skills/](./.claude/skills/README.md) | Templates de prompts |
| [hooks/](./.claude/hooks/README.md) | Scripts automatiques |
