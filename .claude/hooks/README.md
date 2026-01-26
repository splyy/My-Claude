# 🪝 Hooks

> Scripts exécutés automatiquement sur les événements du cycle de vie de Claude Code.

## Hooks disponibles

| Fichier | Événement | Description |
|---------|-----------|-------------|
| `conventional-commits.py` | PreToolUse | Valide le format Conventional Commits |

## Événements

| Événement | Déclencheur | Usage typique |
|-----------|-------------|---------------|
| `SessionStart` | Démarrage de session | Init, checks |
| `UserPromptSubmit` | Envoi d'un prompt | Validation |
| `PreToolUse` | Avant un outil | Bloquer/modifier |
| `PostToolUse` | Après un outil | Format, lint |
| `Stop` | Fin de session | Cleanup |

## Configuration

Dans `.claude/settings.json` :

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "python3 .claude/hooks/conventional-commits.py"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "npx prettier --write $CLAUDE_FILE_PATH 2>/dev/null || true"
          }
        ]
      }
    ]
  }
}
```

## Variables disponibles

| Variable | Description |
|----------|-------------|
| `$CLAUDE_TOOL_NAME` | Nom de l'outil (Read, Write, Bash...) |
| `$CLAUDE_TOOL_INPUT` | Input JSON de l'outil |
| `$CLAUDE_TOOL_OUTPUT` | Output de l'outil (PostToolUse) |
| `$CLAUDE_FILE_PATH` | Chemin du fichier concerné |

## Créer un hook

### Structure de réponse (pour bloquer)

```python
import json
import sys

# Lire l'input
input_data = json.load(sys.stdin)
tool_name = input_data.get("tool_name", "")
tool_input = input_data.get("tool_input", {})

# Logique de validation
if should_block:
    output = {
        "hookSpecificOutput": {
            "hookEventName": "PreToolUse",
            "permissionDecision": "deny",
            "permissionDecisionReason": "Raison du blocage"
        }
    }
    print(json.dumps(output))
    sys.exit(0)

# Autoriser
sys.exit(0)
```

## Exemples de hooks

### Auto-format PHP (PostToolUse)

```bash
# Dans settings.json
{
  "PostToolUse": [
    {
      "matcher": "Write|Edit",
      "hooks": [
        {
          "type": "command",
          "command": "if [[ \"$CLAUDE_FILE_PATH\" == *.php ]]; then php vendor/bin/php-cs-fixer fix \"$CLAUDE_FILE_PATH\" --quiet; fi"
        }
      ]
    }
  ]
}
```

### Bloquer rm -rf (PreToolUse)

```python
#!/usr/bin/env python3
import json, sys, re

data = json.load(sys.stdin)
cmd = data.get("tool_input", {}).get("command", "")

if re.search(r'rm\s+-rf\s+/', cmd):
    print(json.dumps({
        "hookSpecificOutput": {
            "hookEventName": "PreToolUse",
            "permissionDecision": "deny",
            "permissionDecisionReason": "rm -rf est interdit"
        }
    }))

sys.exit(0)
```

### Notification après commit (PostToolUse)

```bash
{
  "PostToolUse": [
    {
      "matcher": "Bash",
      "hooks": [
        {
          "type": "command",
          "command": "if echo \"$CLAUDE_TOOL_INPUT\" | grep -q 'git commit'; then echo '✅ Commit effectué'; fi"
        }
      ]
    }
  ]
}
```
