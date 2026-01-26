# ⌨️ Commands

> Commandes slash personnalisées pour automatiser les workflows.

## Commands disponibles

| Command | Description | Arguments |
|---------|-------------|-----------|
| `/commit` | Commits conventionnels avec emoji | `[message]`, `--no-verify`, `--amend` |
| `/test` | Lancer les tests (auto-detect) | `[filter]`, `--coverage`, `--watch` |
| `/lint` | Linting et formatage | `[path]`, `--fix`, `--check`, `--staged` |
| `/review-pr` | Review une PR/MR | `[number]`, `--security`, `--performance` |
| `/add-changelog` | Générer/mettre à jour CHANGELOG | `[version]`, `[type] [description]` |
| `/ci-setup` | Configurer CI/CD | `--github-actions`, `--gitlab-ci` |
| `/create-architecture-documentation` | Docs d'architecture | `--c4-model`, `--adr` |
| `/create-onboarding-guide` | Guide d'onboarding | `--developer`, `--comprehensive` |
| `/explore-and-plan` | Workflow EPCT | _(aucun)_ |

## Usage

```bash
/commit                    # Commit intelligent
/test UserService          # Tests filtrés
/lint --fix                # Lint et fix
/review-pr 123             # Review PR #123
```

## Structure d'une command

```yaml
---
allowed-tools: Bash, Read, Write, Edit
argument-hint: [arg1] | --option
description: Description courte de la commande
---

# Titre

Instructions pour Claude...

$ARGUMENTS contient les arguments passés.
```

## Paramètres

| Paramètre | Description | Requis |
|-----------|-------------|--------|
| `allowed-tools` | Outils que la command peut utiliser | ✅ |
| `argument-hint` | Aide sur les arguments | ❌ |
| `description` | Description pour `/help` | ✅ |

## Créer une nouvelle command

1. Créer `.claude/commands/ma-command.md`
2. Ajouter le frontmatter YAML
3. Écrire les instructions avec `$ARGUMENTS`
4. Tester avec `/ma-command test`

## Exemples de patterns

### Détection automatique

```markdown
## Détection du projet

- PHP : @composer.json (si existe)
- Node : @package.json (si existe)
- Config : !`ls *.config.* 2>/dev/null`
```

### Exécution conditionnelle

```markdown
## Comportement

1. Si PHP → `php vendor/bin/phpunit`
2. Si Node → `npm test`
3. Sinon → demander clarification
```

### Avec arguments

```markdown
Filtrer par : $ARGUMENTS

Si vide → tout exécuter
Si texte → filtrer par ce texte
Si --flag → comportement spécial
```
