---
name: code-review
description: Templates et guidelines pour review de code. Checklist, commentaires constructifs, patterns.
allowed-tools: Read, Glob, Grep
---

# 🔍 Code Review Guidelines

> Templates et bonnes pratiques pour des reviews de code efficaces.

---

## 1. Checklist de review

### ✅ Fonctionnalité
- [ ] Le code fait ce qu'il est censé faire
- [ ] Les edge cases sont gérés
- [ ] Les erreurs sont correctement traitées
- [ ] La logique métier est correcte

### 🏗️ Architecture
- [ ] Respect des patterns du projet
- [ ] Séparation des responsabilités (SRP)
- [ ] Dépendances appropriées (pas de couplage fort)
- [ ] Cohérence avec l'architecture existante

### 📖 Lisibilité
- [ ] Nommage clair et explicite
- [ ] Fonctions courtes et focalisées
- [ ] Complexité raisonnable (pas de nested hell)
- [ ] Code auto-documenté

### 🧪 Tests
- [ ] Tests unitaires présents
- [ ] Tests d'intégration si nécessaire
- [ ] Coverage appropriée
- [ ] Tests lisibles et maintenables

### 🔒 Sécurité
- [ ] Validation des entrées utilisateur
- [ ] Pas d'injection (SQL, XSS, etc.)
- [ ] Gestion appropriée des secrets
- [ ] Authentification/autorisation correctes

### ⚡ Performance
- [ ] Pas de requêtes N+1
- [ ] Utilisation du cache si approprié
- [ ] Pas d'opérations bloquantes inutiles
- [ ] Complexité algorithmique raisonnable

---

## 2. Format des commentaires

### Préfixes recommandés

| Préfixe | Signification | Action requise |
|---------|---------------|----------------|
| `🔴 blocker:` | Problème critique | Doit être corrigé |
| `🟡 suggestion:` | Amélioration possible | À considérer |
| `🟢 nitpick:` | Détail mineur | Optionnel |
| `❓ question:` | Besoin de clarification | Réponse attendue |
| `💡 idea:` | Idée pour le futur | Pour discussion |
| `👍 praise:` | Bon travail | Encouragement |

### Exemples de commentaires

```markdown
🔴 blocker: Cette requête SQL est vulnérable à l'injection.
Utiliser des prepared statements :
```php
$stmt = $pdo->prepare("SELECT * FROM users WHERE id = ?");
$stmt->execute([$id]);
```

---

🟡 suggestion: Cette logique pourrait être extraite dans un service dédié
pour améliorer la testabilité et la réutilisation.

---

🟢 nitpick: Préférer `const` à `let` ici puisque la variable n'est pas réassignée.

---

❓ question: Quelle est la raison de ce timeout de 30s ?
Est-ce documenté quelque part ?

---

👍 praise: Excellente gestion des erreurs avec des messages explicites !
```

---

## 3. Anti-patterns à détecter

### Code smells

| Smell | Description | Solution |
|-------|-------------|----------|
| **Long method** | Fonction > 30 lignes | Extraire en sous-fonctions |
| **God class** | Classe qui fait tout | Découper en classes spécialisées |
| **Feature envy** | Méthode utilisant trop une autre classe | Déplacer la méthode |
| **Magic numbers** | Valeurs hardcodées | Utiliser des constantes |
| **Deep nesting** | if/for imbriqués > 3 niveaux | Early return, extraire |

### Security issues

| Issue | Exemple | Fix |
|-------|---------|-----|
| **SQL Injection** | `"SELECT * FROM users WHERE id = " + id` | Prepared statements |
| **XSS** | `innerHTML = userInput` | Escape ou textContent |
| **CSRF** | Form sans token | Ajouter CSRF token |
| **Secrets exposés** | `apiKey = "sk-..."` | Variables d'environnement |

### Performance issues

| Issue | Symptôme | Fix |
|-------|----------|-----|
| **N+1 queries** | Boucle avec requête | Eager loading |
| **Missing index** | Recherche lente | Ajouter index DB |
| **Memory leak** | Event listeners non nettoyés | Cleanup on unmount |
| **Blocking I/O** | Lecture sync de fichiers | Async/await |

---

## 4. Templates de réponse

### Approval

```markdown
## ✅ Approved

Très bon travail ! Le code est propre et bien testé.

### Points positifs
- 👍 Bonne séparation des responsabilités
- 👍 Tests complets avec edge cases
- 👍 Documentation claire

### Suggestions mineures (optionnel)
- 💡 Ligne 42 : pourrait utiliser destructuring
```

### Request changes

```markdown
## ⚠️ Changes requested

Quelques points à adresser avant merge.

### À corriger
- 🔴 **Sécurité** (ligne 23) : Validation manquante sur l'input utilisateur
- 🔴 **Tests** : Pas de test pour le cas d'erreur

### Suggestions
- 🟡 Ligne 45 : Extraire cette logique dans un helper

### Questions
- ❓ Ligne 67 : Pourquoi ce timeout spécifique ?
```

### Comment only

```markdown
## 💬 Comments

Pas de blockers, juste quelques réflexions.

### Observations
- 💡 Cette approche pourrait poser des problèmes de scale
- ❓ As-tu considéré utiliser X au lieu de Y ?
```

---

## 5. Bonnes pratiques reviewer

1. **Être constructif** - Proposer des solutions, pas juste critiquer
2. **Être spécifique** - Pointer les lignes exactes
3. **Être humble** - "Je suggère" plutôt que "Tu dois"
4. **Être rapide** - Reviewer dans les 24h
5. **Être complet** - Une seule passe, tous les commentaires

---

> **Rappel** : Le but est d'améliorer le code ET de faire grandir l'équipe.
