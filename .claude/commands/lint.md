---
allowed-tools: Bash, Read, Glob, Write
argument-hint: [path] | --fix | --check | --staged
description: Lancer le linting et le formatage du code avec correction automatique
---

# 🔍 Lint & Format

Lancer le linting du projet : $ARGUMENTS

## Détection du projet

- PHP : @.php-cs-fixer.php ou @.php-cs-fixer.dist.php ou @phpstan.neon
- JS/TS : @eslint.config.js ou @.eslintrc.* ou @prettier.config.*
- Package scripts : @package.json
- Composer scripts : @composer.json

## Outils par stack

### PHP

```bash
# PHP-CS-Fixer (formatage)
php vendor/bin/php-cs-fixer fix                    # Fix all
php vendor/bin/php-cs-fixer fix src/               # Fix path
php vendor/bin/php-cs-fixer fix --dry-run          # Check only

# PHPStan (analyse statique)
php vendor/bin/phpstan analyse                     # Analyse
php vendor/bin/phpstan analyse src/                # Analyse path
php vendor/bin/phpstan analyse --level=max         # Max level

# Laravel Pint
./vendor/bin/pint                                  # Fix all
./vendor/bin/pint --test                          # Check only
./vendor/bin/pint src/                            # Fix path

# Rector (refactoring)
php vendor/bin/rector process                      # Process
php vendor/bin/rector process --dry-run            # Check only
```

### JavaScript / TypeScript

```bash
# ESLint
npx eslint .                                       # Check
npx eslint . --fix                                 # Fix
npx eslint src/                                    # Check path

# Prettier
npx prettier --check .                             # Check
npx prettier --write .                             # Fix
npx prettier --write src/                          # Fix path

# Both
npm run lint                                       # Via script
npm run lint:fix                                   # Via script
```

### Staged files (pre-commit)

```bash
# lint-staged
npx lint-staged

# Manual
git diff --cached --name-only --diff-filter=ACMR | grep '\.php$' | xargs php vendor/bin/php-cs-fixer fix
git diff --cached --name-only --diff-filter=ACMR | grep '\.[jt]sx?$' | xargs npx prettier --write
```

## Comportement

1. **Détecter** les outils de linting disponibles
2. **Adapter** selon les arguments :
   - Sans argument → `--fix` par défaut (corrige)
   - `--check` → vérification sans modification
   - `--fix` → correction automatique
   - `--staged` → fichiers staged seulement
   - `[path]` → chemin spécifique

3. **Exécuter** dans l'ordre recommandé :
   - Formatage (Prettier / PHP-CS-Fixer / Pint)
   - Linting (ESLint)
   - Analyse statique (PHPStan)

4. **Résumer** les corrections et erreurs restantes

## Exemples

```bash
/lint                      # Fix tout le projet
/lint --check              # Vérifier sans modifier
/lint src/                 # Fix un dossier
/lint --staged             # Fix fichiers staged
/lint src/Controller/      # Fix un dossier spécifique
```

## Configuration recommandée

### PHP-CS-Fixer (.php-cs-fixer.php)

```php
<?php
$finder = PhpCsFixer\Finder::create()
    ->in(__DIR__.'/src')
    ->in(__DIR__.'/tests');

return (new PhpCsFixer\Config())
    ->setRules([
        '@Symfony' => true,
        '@Symfony:risky' => true,
        'declare_strict_types' => true,
    ])
    ->setFinder($finder)
    ->setRiskyAllowed(true);
```

### ESLint (eslint.config.js)

```javascript
import eslint from '@eslint/js';
import tseslint from 'typescript-eslint';

export default tseslint.config(
  eslint.configs.recommended,
  ...tseslint.configs.recommended,
);
```

### Prettier (.prettierrc)

```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5"
}
```
