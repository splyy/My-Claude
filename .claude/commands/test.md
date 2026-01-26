---
allowed-tools: Bash, Read, Glob
argument-hint: [filter] | --coverage | --watch | --unit | --integration
description: Lancer les tests du projet avec détection automatique du framework
---

# 🧪 Run Tests

Lancer les tests du projet : $ARGUMENTS

## Détection du projet

- PHP Tests : @phpunit.xml ou @phpunit.xml.dist ou @pest.php
- JS Tests : @jest.config.js ou @jest.config.ts ou @vitest.config.ts
- Package manager : @package.json (scripts test)
- Composer : @composer.json (scripts test)

## Détection automatique

1. **Vérifier le type de projet** en lisant les fichiers de config
2. **Identifier le framework de test** :
   - PHP : PHPUnit, Pest
   - JS/TS : Jest, Vitest, Mocha, Playwright

3. **Exécuter avec les bons arguments** selon `$ARGUMENTS` :

### PHP (PHPUnit / Pest)

```bash
# Standard
php vendor/bin/phpunit
php vendor/bin/pest

# Avec filtre
php vendor/bin/phpunit --filter="$ARGUMENTS"
php vendor/bin/pest --filter="$ARGUMENTS"

# Coverage
php vendor/bin/phpunit --coverage-html coverage/
php vendor/bin/pest --coverage

# Groupe spécifique
php vendor/bin/phpunit --group=unit
php vendor/bin/phpunit --group=integration
```

### JavaScript / TypeScript (Jest / Vitest)

```bash
# Standard
npm run test
pnpm test
yarn test

# Avec filtre
npx jest --testNamePattern="$ARGUMENTS"
npx vitest run "$ARGUMENTS"

# Watch mode
npx jest --watch
npx vitest

# Coverage
npx jest --coverage
npx vitest run --coverage
```

### Laravel

```bash
php artisan test
php artisan test --filter="$ARGUMENTS"
php artisan test --coverage
```

### Symfony

```bash
php bin/phpunit
php bin/phpunit --filter="$ARGUMENTS"
symfony php vendor/bin/phpunit
```

## Comportement

1. **Détecter** le framework de test automatiquement
2. **Adapter** la commande selon les arguments :
   - Sans argument → lancer tous les tests
   - Avec texte → filtrer par nom
   - `--coverage` → générer le coverage
   - `--watch` → mode watch (JS uniquement)
   - `--unit` → tests unitaires seulement
   - `--integration` → tests d'intégration seulement

3. **Afficher** le résultat de façon claire
4. **En cas d'échec** → analyser les erreurs et suggérer des corrections

## Exemples

```bash
/test                      # Lancer tous les tests
/test UserService          # Filtrer par nom
/test --coverage           # Avec rapport de couverture
/test --watch              # Mode watch (JS)
/test --unit               # Tests unitaires seulement
```
