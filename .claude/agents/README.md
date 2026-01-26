# 🤖 Agents

> SubAgents spécialisés pour déléguer des tâches complexes.

## Agents disponibles

| Agent | Spécialité | Model |
|-------|------------|-------|
| **backend-expert** | Architecture API, microservices, DB, performance | sonnet |
| **symfony-expert** | Symfony 6/7, API-Platform, Doctrine, Messenger | sonnet |
| **laravel-expert** | Laravel 11, Eloquent, Artisan, packages officiels | sonnet |
| **nextjs-expert** | Next.js 15/16, App Router, Server Components, React | sonnet |
| **php-developer** | PHP 8.3+ moderne, OOP, PSR, testing | sonnet |
| **api-expert** | REST, GraphQL, OpenAPI, versioning, auth | sonnet |

## Usage

### Via prompt naturel

```
> Utilise l'agent symfony-expert pour implémenter cette feature
```

### Via Task tool

L'agent est invoqué automatiquement par Claude via le tool `Task` avec le `subagent_type` approprié.

## Structure d'un agent

```yaml
---
name: mon-agent
description: Description pour Claude (quand utiliser cet agent)
tools: Read, Write, Edit, Bash, Grep, Glob
model: sonnet  # ou opus, haiku
---

# Titre

Instructions détaillées pour l'agent...
```

## Paramètres

| Paramètre | Description | Requis |
|-----------|-------------|--------|
| `name` | Identifiant unique | ✅ |
| `description` | Quand utiliser cet agent | ✅ |
| `tools` | Outils autorisés | ✅ |
| `model` | Modèle Claude (sonnet/opus/haiku) | ❌ |

## Bonnes pratiques

1. **Description claire** - Indiquer précisément quand utiliser l'agent
2. **Tools minimaux** - N'autoriser que les outils nécessaires
3. **Instructions structurées** - Sections claires, exemples de code
4. **Model approprié** - Haiku pour simple, Sonnet pour standard, Opus pour complexe

## Créer un nouvel agent

1. Créer `.claude/agents/mon-agent.md`
2. Ajouter le frontmatter YAML
3. Écrire les instructions détaillées
4. Tester avec un prompt ciblé

## Agents recommandés par projet

| Type de projet | Agents suggérés |
|----------------|-----------------|
| **API Symfony** | backend-expert, symfony-expert, api-expert |
| **API Laravel** | backend-expert, laravel-expert, api-expert |
| **App Next.js** | nextjs-expert |
| **Full-stack Symfony** | symfony-expert, php-developer |
| **Full-stack Laravel** | laravel-expert, php-developer |
