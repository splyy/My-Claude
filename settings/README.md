# ⚙️ Settings Templates

> Templates de configuration Claude Code organisés par couches.

---

## 📁 Structure

```
settings/
├── settings.json              # 🔧 Base (git, fichiers, deny rules)
├── common/
│   ├── settings_php.json      # 🐘 Écosystème PHP complet
│   └── settings_node.json     # 📦 Écosystème Node complet
├── frameworks/
│   ├── settings_symfony.json  # 🎵 Symfony + API-Platform
│   ├── settings_laravel.json  # 🔴 Laravel + Sail
│   └── settings_nextjs.json   # ⚛️ Next.js + React
├── infra/
│   ├── settings_docker.json   # 🐳 Docker + FrankenPHP
│   ├── settings_elasticsearch.json
│   └── settings_makefile.json
├── platforms/
│   ├── settings_github.json   # 🐙 GitHub CLI
│   └── settings_gitlab.json   # 🦊 GitLab CLI
└── ide/
    ├── settings_vscode.json   # 💻 VS Code
    └── settings_phpstorm.json # 🧠 PHPStorm / IntelliJ
```

---

## 🚀 Utilisation

### Principe de composition

Les configs sont conçues pour être **combinées** selon vos besoins :

```
Base + Common + Framework + Infra + Platform + IDE
```

### Exemple : Projet Symfony

Fusionner dans votre `.claude/settings.json` :

1. `settings.json` (base)
2. `common/settings_php.json`
3. `frameworks/settings_symfony.json`
4. `infra/settings_docker.json`
5. `platforms/settings_github.json`
6. `ide/settings_vscode.json`

### Exemple : Projet Next.js

Fusionner :

1. `settings.json` (base)
2. `common/settings_node.json`
3. `frameworks/settings_nextjs.json`
4. `platforms/settings_github.json`
5. `ide/settings_vscode.json`

---

## 📋 Contenu par fichier

### 🔧 Base (`settings.json`)

| Catégorie | Commandes |
|-----------|-----------|
| **Git** | status, log, diff, branch, show, stash, fetch, pull, tag |
| **Fichiers** | ls, pwd, which, cat, head, tail, wc, tree, find, grep |
| **Deny** | rm -rf, git push --force, git reset --hard, chmod 777 |

---

### 🐘 PHP (`common/settings_php.json`)

| Outil | Commandes |
|-------|-----------|
| **Composer** | require, install, update, remove, dump-autoload, outdated, show, validate, run |
| **PHPUnit** | vendor/bin/phpunit |
| **PHPStan** | analyse, clear-result-cache |
| **PHP-CS-Fixer** | fix, check, describe |
| **Pest** | pest, --filter, --coverage |
| **Rector** | vendor/bin/rector |
| **PHP CLI** | -v, -m, -i, -l |

---

### 📦 Node (`common/settings_node.json`)

| Outil | Commandes |
|-------|-----------|
| **npm** | run, install, ci, update, outdated, audit, ls, init, test, start, build |
| **pnpm** | run, install, add, remove, update, test, build, dev |
| **yarn** | run, install, add, remove, test, build, dev |
| **npx** | tsc, ts-node, eslint, prettier, jest, vitest |

**Deny :** npm/pnpm/yarn publish

---

### 🎵 Symfony (`frameworks/settings_symfony.json`)

| Catégorie | Commandes |
|-----------|-----------|
| **Console** | bin/console (tous), cache:clear, cache:warmup |
| **Symfony CLI** | console, server, check, local, var |
| **Doctrine** | doctrine:* |
| **API-Platform** | api:* |
| **Autres** | make:*, debug:*, messenger:*, translation:* |

**Deny :** doctrine:database:drop --force, doctrine:schema:drop --force

**Hook :** Auto-format PHP avec php-cs-fixer après Write/Edit

---

### 🔴 Laravel (`frameworks/settings_laravel.json`)

| Catégorie | Commandes |
|-----------|-----------|
| **Artisan** | serve, make:*, migrate:*, db:*, queue:*, cache:*, config:*, route:* |
| **Sail** | ./vendor/bin/sail |
| **Packages** | breeze, jetstream, horizon, telescope, nova, livewire |
| **Frontend** | npm run dev/build, npx vite |

**Deny :** migrate:fresh --force, db:wipe --force

**Hook :** Auto-format PHP avec Pint ou php-cs-fixer après Write/Edit

---

### ⚛️ Next.js (`frameworks/settings_nextjs.json`)

| Catégorie | Commandes |
|-----------|-----------|
| **Next.js** | dev, build, start, lint, info, create-next-app |
| **React** | create-react-app, react-scripts, vite |
| **UI** | tailwindcss, shadcn, shadcn-ui |
| **DB** | prisma, drizzle-kit |

**Deny :** prisma migrate reset --force, prisma db push --force-reset

**Hook :** Auto-format avec Prettier après Write/Edit

---

### 🐳 Docker (`infra/settings_docker.json`)

| Catégorie | Commandes |
|-----------|-----------|
| **Compose** | docker compose, docker-compose |
| **Containers** | ps, logs, exec, build, images, pull, inspect, stats, top, cp |
| **Network** | network |
| **FrankenPHP** | run, php-server, php-cli |

**Deny :** system prune -a, rm -f $(docker ps -aq), volume prune -f

---

### 🐙 GitHub (`platforms/settings_github.json`)

| Catégorie | Commandes |
|-----------|-----------|
| **PR** | list, view, create, checkout, diff, merge, review |
| **Issues** | list, view, create, close |
| **Repo** | view, clone |
| **CI** | run list/view/watch, workflow |
| **Autres** | api, release, gist, search, browse |

**Deny :** repo delete, auth logout, api -X DELETE

---

### 🦊 GitLab (`platforms/settings_gitlab.json`)

| Catégorie | Commandes |
|-----------|-----------|
| **MR** | list, view, create, checkout, diff, merge, approve |
| **Issues** | list, view, create, close |
| **CI** | list, view, trace, status |
| **Autres** | api, release, snippet, search |

**Deny :** repo delete, auth logout, api --method DELETE

---

### 💻 VS Code (`ide/settings_vscode.json`)

| Commandes |
|-----------|
| code, --diff, --goto, --new-window, --reuse-window, --add |
| --install-extension, --list-extensions, --disable-extension |
| -r, -n, -g, --wait, --status |

---

### 🧠 PHPStorm (`ide/settings_phpstorm.json`)

| Commandes |
|-----------|
| phpstorm, --line, --column, diff, merge, format, inspect |
| idea (alias), webstorm |

---

## 🔗 Liens

- [Documentation principale](../README.md)
- [Guide avancé](../AVANCE.md)
