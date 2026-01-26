# ✨ Skills

> Templates de prompts réutilisables pour standardiser les workflows.

## Skills disponibles

| Skill | Description |
|-------|-------------|
| **documentation-templates** | Templates README, API docs, ADR, changelog |
| **code-review** | Checklist et guidelines pour review de code |
| **debugging** | Workflow systématique de debugging |

## Usage

### Invocation manuelle

```bash
/documentation-templates
/code-review
/debugging
```

### Invocation automatique

Claude peut invoquer un skill automatiquement s'il est pertinent pour la tâche.

## Structure d'un skill

```
.claude/skills/
└── mon-skill/
    └── SKILL.md
```

```yaml
---
name: mon-skill
description: Description pour Claude
allowed-tools: Read, Glob, Grep
---

# Titre

Contenu du skill...
```

## Différence Skill vs Command

| Aspect | Skill | Command |
|--------|-------|---------|
| **Invocation** | Auto ou manuel | Manuel seulement |
| **Structure** | Dossier + SKILL.md | Fichier .md |
| **Usage** | Templates, guidelines | Actions, workflows |

## Créer un nouveau skill

1. Créer dossier `.claude/skills/mon-skill/`
2. Créer `SKILL.md` avec frontmatter
3. Écrire le contenu (templates, guidelines)
4. Tester avec `/mon-skill`

## Bonnes pratiques

1. **Contenu actionnable** - Pas juste de la théorie
2. **Exemples concrets** - Code, commandes, outputs
3. **Structure claire** - Sections numérotées, tableaux
4. **Concis** - Aller à l'essentiel
