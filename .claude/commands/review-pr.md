---
allowed-tools: Bash, Read, Glob, Grep
argument-hint: [pr-number] | [branch] | --security | --performance
description: Review une Pull Request ou Merge Request avec analyse de code
---

# 🔎 Review PR/MR

Review de la PR/MR : $ARGUMENTS

## Contexte

- Platform : !`git remote -v | head -1`
- Current branch : !`git branch --show-current`
- PR/MR info : Détecter GitHub (gh) ou GitLab (glab)

## Détection de la plateforme

```bash
# GitHub
gh pr view $ARGUMENTS
gh pr diff $ARGUMENTS

# GitLab
glab mr view $ARGUMENTS
glab mr diff $ARGUMENTS
```

## Process de review

### 1. Récupérer les informations

```bash
# GitHub
gh pr view $ARGUMENTS --json title,body,author,files,additions,deletions

# GitLab
glab mr view $ARGUMENTS
```

### 2. Analyser les changements

```bash
# Diff complet
gh pr diff $ARGUMENTS

# Fichiers modifiés
gh pr view $ARGUMENTS --json files --jq '.files[].path'
```

### 3. Points de review

#### Code Quality
- [ ] Le code suit les conventions du projet
- [ ] Pas de code dupliqué
- [ ] Nommage clair et cohérent
- [ ] Complexité raisonnable
- [ ] Pas de code mort ou commenté

#### Architecture
- [ ] Respect des patterns du projet
- [ ] Séparation des responsabilités
- [ ] Dépendances appropriées
- [ ] Pas de couplage excessif

#### Tests
- [ ] Tests présents pour les nouvelles fonctionnalités
- [ ] Tests passent
- [ ] Coverage appropriée
- [ ] Tests lisibles et maintenables

#### Sécurité (`--security`)
- [ ] Pas d'injection SQL
- [ ] Pas de XSS
- [ ] Validation des entrées
- [ ] Gestion des secrets
- [ ] Authentification/autorisation correctes
- [ ] Pas de données sensibles exposées

#### Performance (`--performance`)
- [ ] Pas de requêtes N+1
- [ ] Utilisation appropriée du cache
- [ ] Pas de boucles inefficaces
- [ ] Pagination pour les listes
- [ ] Index DB appropriés

#### Documentation
- [ ] Code auto-documenté
- [ ] Commentaires pour la logique complexe
- [ ] README mis à jour si nécessaire
- [ ] CHANGELOG mis à jour

### 4. Format du rapport

```markdown
## 📋 Review Summary

**PR:** #$ARGUMENTS
**Author:** [author]
**Changes:** +X / -Y lines across Z files

### ✅ Approuvé / ⚠️ Changements demandés / ❌ Bloqué

### Points positifs
- 👍 [point positif 1]
- 👍 [point positif 2]

### Suggestions
- 💡 [suggestion 1] (fichier:ligne)
- 💡 [suggestion 2] (fichier:ligne)

### Problèmes à corriger
- 🔴 [problème critique] (fichier:ligne)
- 🟡 [problème mineur] (fichier:ligne)

### Questions
- ❓ [question 1]

### Tests
- [ ] Tests ajoutés/modifiés : Oui/Non
- [ ] Tests passent : Oui/Non
```

## Exemples

```bash
/review-pr 123             # Review PR #123
/review-pr feature/auth    # Review branch
/review-pr 123 --security  # Focus sécurité
/review-pr 123 --performance  # Focus performance
```

## Commandes utiles

```bash
# GitHub
gh pr checkout 123         # Checkout la PR localement
gh pr review 123 --approve # Approuver
gh pr review 123 --request-changes --body "..."
gh pr comment 123 --body "..."

# GitLab
glab mr checkout 123
glab mr approve 123
glab mr note 123 --message "..."
```
