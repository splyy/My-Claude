# 🤖 CLAUDE.md

> Instructions pour Claude Code travaillant dans ce dépôt.

---

## 📋 Présentation

Ce dépôt est une **bibliothèque de templates de configuration Claude Code** — une collection réutilisable de settings, agents, commands, skills et hooks.

**Objectif :** Copier ces configurations vers `~/.claude/` (User) ou `.claude/` (Project).

---

## 📁 Structure du dépôt

```
my-claude/
├── README.md                   # 📚 Documentation des concepts Claude Code
├── AVANCE.md                   # 🚀 Guide avancé (permissions, CLI, coûts)
├── CLAUDE.md                   # 🤖 Ce fichier (instructions projet)
│
├── settings/                   # ⚙️ Templates de configuration
│   ├── README.md              # Documentation des settings
│   ├── settings.json          # Configuration de base
│   ├── common/                # PHP, Node ecosystems
│   ├── frameworks/            # Symfony, Laravel, Next.js
│   ├── infra/                 # Docker, Elasticsearch, Makefile
│   ├── platforms/             # GitHub, GitLab
│   └── ide/                   # VSCode, PHPStorm
│
├── .claude/
│   ├── agents/                # 🤖 SubAgents spécialisés
│   │   ├── README.md
│   │   ├── backend-expert.md
│   │   ├── symfony-expert.md
│   │   ├── laravel-expert.md
│   │   ├── nextjs-expert.md
│   │   ├── php-developer.md
│   │   └── api-expert.md
│   │
│   ├── commands/              # ⌨️ Commandes slash
│   │   ├── README.md
│   │   ├── commit.md
│   │   ├── test.md
│   │   ├── lint.md
│   │   ├── review-pr.md
│   │   ├── add-changelog.md
│   │   ├── ci-setup.md
│   │   ├── create-architecture-documentation.md
│   │   ├── create-onboarding-guide.md
│   │   └── explore-and-plan.md
│   │
│   ├── skills/                # ✨ Templates de prompts
│   │   ├── README.md
│   │   ├── documentation-templates/
│   │   ├── code-review/
│   │   └── debugging/
│   │
│   └── hooks/                 # 🪝 Scripts automatiques
│       ├── README.md
│       └── conventional-commits.py
│
└── .mcp.json                  # 🔌 Configuration MCP (GitHub)
```

---

## 🤖 Agents disponibles

| Agent | Spécialité |
|-------|------------|
| `backend-expert` | Architecture API, microservices, DB, performance |
| `symfony-expert` | Symfony 6/7, API-Platform, Doctrine, Messenger |
| `laravel-expert` | Laravel 11, Eloquent, Artisan, packages officiels |
| `nextjs-expert` | Next.js 15/16, App Router, Server Components |
| `php-developer` | PHP 8.3+ moderne, OOP, PSR, testing |
| `api-expert` | REST, GraphQL, OpenAPI, versioning |

---

## ⌨️ Commands disponibles

| Command | Description |
|---------|-------------|
| `/commit` | Commits conventionnels avec emoji et atomic splitting |
| `/test` | Lancer les tests (auto-détection framework) |
| `/lint` | Linting et formatage avec auto-fix |
| `/review-pr` | Review une PR/MR GitHub/GitLab |
| `/add-changelog` | Générer/mettre à jour CHANGELOG |
| `/ci-setup` | Configurer pipeline CI/CD |
| `/create-architecture-documentation` | Générer docs d'architecture |
| `/create-onboarding-guide` | Créer guide d'onboarding |
| `/explore-and-plan` | Workflow EPCT |

---

## ✨ Skills disponibles

| Skill | Description |
|-------|-------------|
| `documentation-templates` | Templates README, API docs, ADR |
| `code-review` | Checklist et guidelines de review |
| `debugging` | Workflow systématique de debugging |

---

## ⚙️ Settings par couche

```
settings/
├── settings.json           # 🔧 Base (git, fichiers, deny rules)
├── common/
│   ├── settings_php.json   # 🐘 Composer, PHPUnit, PHPStan, PHP-CS-Fixer, Pest
│   └── settings_node.json  # 📦 npm, pnpm, yarn, TypeScript, ESLint, Jest
├── frameworks/
│   ├── settings_symfony.json  # 🎵 Symfony + API-Platform (+ hook auto-format)
│   ├── settings_laravel.json  # 🔴 Laravel + Sail (+ hook auto-format)
│   └── settings_nextjs.json   # ⚛️ Next.js + React (+ hook Prettier)
├── infra/
│   ├── settings_docker.json
│   ├── settings_elasticsearch.json
│   └── settings_makefile.json
├── platforms/
│   ├── settings_github.json
│   └── settings_gitlab.json
└── ide/
    ├── settings_vscode.json
    └── settings_phpstorm.json
```

### Composition recommandée

| Projet | Configs à combiner |
|--------|-------------------|
| **API Symfony** | base + php + symfony + docker + github + vscode |
| **API Laravel** | base + php + laravel + docker + github + vscode |
| **App Next.js** | base + node + nextjs + github + vscode |

---

## 🚀 Utilisation

### Copier les settings

```bash
# Copier le base
cp settings/settings.json ~/.claude/settings.json

# Ou pour un projet
cp settings/settings.json monProjet/.claude/settings.json
# Puis fusionner les settings spécifiques
```

### Copier agents/commands/skills

```bash
# Vers User (global)
cp -r .claude/* ~/.claude/

# Vers Project (spécifique)
cp -r .claude/* monProjet/.claude/
```

---

## 🌐 Langues

| Élément | Langue |
|---------|--------|
| Documentation (`*.md`) | Français |
| Code, configs, prompts d'agents | Anglais |

---

## 📖 Documentation

- [README.md](./README.md) — Concepts fondamentaux Claude Code
- [AVANCE.md](./AVANCE.md) — Guide avancé (permissions, modèles, CLI)
- [settings/README.md](./settings/README.md) — Documentation des settings
