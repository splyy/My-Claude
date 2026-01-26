---
name: debugging
description: Workflow et techniques de debugging. Approche systématique, outils, patterns de résolution.
allowed-tools: Read, Glob, Grep, Bash
---

# 🐛 Debugging Workflow

> Approche systématique pour identifier et résoudre les bugs.

---

## 1. Workflow DERIVE

### D - Define (Définir)
**Quel est exactement le problème ?**
- Message d'erreur exact
- Comportement attendu vs observé
- Étapes pour reproduire
- Environnement (OS, version, config)

### E - Examine (Examiner)
**Où se situe le problème ?**
- Stack trace
- Logs
- Derniers changements (git diff)
- Inputs/outputs

### R - Reproduce (Reproduire)
**Peut-on reproduire de façon fiable ?**
- Créer un cas minimal
- Identifier les conditions
- Automatiser si possible

### I - Isolate (Isoler)
**Quelle partie du code est responsable ?**
- Binary search dans le code
- Désactiver des parties
- Tester composants individuellement

### V - Verify (Vérifier)
**La correction fonctionne-t-elle ?**
- Tester le fix
- Vérifier les régressions
- Tester les edge cases

### E - Explain (Expliquer)
**Pourquoi le bug existait-il ?**
- Documenter la cause
- Ajouter un test de régression
- Partager l'apprentissage

---

## 2. Outils par stack

### PHP

```bash
# Xdebug (step debugging)
php -dxdebug.mode=debug script.php

# Dump & die
dd($variable);          # Laravel
dump($variable);        # Symfony

# Logs
tail -f var/log/dev.log         # Symfony
tail -f storage/logs/laravel.log # Laravel

# Profiling
php -dxdebug.mode=profile script.php
# Analyser avec QCacheGrind/KCacheGrind

# Memory
php -d memory_limit=-1 script.php
```

### JavaScript/TypeScript

```javascript
// Console
console.log(variable);
console.table(array);
console.trace();
console.time('label'); /* code */ console.timeEnd('label');

// Debugger
debugger; // Breakpoint dans le code

// Node.js
node --inspect script.js
node --inspect-brk script.js  // Break au démarrage
```

```bash
# Chrome DevTools
chrome://inspect

# VS Code debugging
# Créer .vscode/launch.json
```

### Base de données

```sql
-- Expliquer une requête
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'test@example.com';

-- Logs PostgreSQL
SET log_statement = 'all';

-- MySQL slow query log
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 1;
```

### Docker

```bash
# Logs d'un container
docker logs -f container_name

# Shell dans un container
docker exec -it container_name sh

# Inspecter
docker inspect container_name
```

---

## 3. Techniques de debugging

### Binary Search

```
1. Le bug est-il dans la première moitié ou la seconde ?
2. Répéter jusqu'à trouver la ligne exacte
```

**Avec git bisect :**
```bash
git bisect start
git bisect bad                 # Commit actuel est bugué
git bisect good abc123         # Ce commit était OK
# Git checkout un commit au milieu
# Tester et marquer good/bad
git bisect good  # ou bad
# Répéter jusqu'à trouver le commit coupable
git bisect reset
```

### Rubber Duck Debugging

1. Expliquer le problème à voix haute
2. Décrire ce que le code est censé faire
3. Décrire ce qu'il fait réellement
4. Souvent, l'explication révèle le bug

### Print Debugging (stratégique)

```php
// Numéroter les checkpoints
error_log("DEBUG 1: Entering function");
error_log("DEBUG 2: Variable = " . json_encode($var));
error_log("DEBUG 3: After condition");
```

### Minimal Reproduction

1. Copier le code problématique isolément
2. Supprimer tout ce qui n'est pas nécessaire
3. Garder le minimum qui reproduit le bug
4. Le bug devient souvent évident

---

## 4. Patterns de bugs courants

### Off-by-one

```javascript
// ❌ Bug
for (let i = 0; i <= array.length; i++) // <= au lieu de <

// ✅ Fix
for (let i = 0; i < array.length; i++)
```

### Null/Undefined

```javascript
// ❌ Bug
const name = user.profile.name; // profile peut être null

// ✅ Fix
const name = user?.profile?.name ?? 'Unknown';
```

### Race condition

```javascript
// ❌ Bug
let data;
fetchData().then(result => { data = result; });
console.log(data); // undefined

// ✅ Fix
const data = await fetchData();
console.log(data);
```

### N+1 Query

```php
// ❌ Bug - N+1 queries
$posts = Post::all();
foreach ($posts as $post) {
    echo $post->author->name; // 1 query par post
}

// ✅ Fix - Eager loading
$posts = Post::with('author')->get();
```

### Mutation inattendue

```javascript
// ❌ Bug
function addItem(array, item) {
    array.push(item); // Modifie l'original
    return array;
}

// ✅ Fix
function addItem(array, item) {
    return [...array, item]; // Nouvelle copie
}
```

---

## 5. Questions à se poser

### Quand le bug apparaît

- [ ] Toujours ou parfois ?
- [ ] Sur quel environnement ?
- [ ] Après quelle action ?
- [ ] Depuis quand ? (quel commit)

### Ce qui a changé

- [ ] Dernier déploiement ?
- [ ] Nouvelle dépendance ?
- [ ] Changement de config ?
- [ ] Données différentes ?

### Ce qui fonctionne

- [ ] Autres fonctionnalités similaires ?
- [ ] Même code ailleurs ?
- [ ] En local vs production ?

---

## 6. Checklist post-fix

- [ ] Le bug original est corrigé
- [ ] Pas de régression introduite
- [ ] Test de régression ajouté
- [ ] Cause root documentée
- [ ] Code review effectuée
- [ ] Monitoring en place

---

> **Rappel** : Les meilleurs debuggers comprennent le code avant de le modifier.
