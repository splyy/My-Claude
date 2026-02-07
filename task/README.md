# Task System - Guide Complet

> Transformer Claude Code en chef de projet autonome grace au systeme de Tasks natif.

---

## Table des Matieres

1. [Le Concept](#-le-concept)
2. [Les Outils Natifs](#-les-outils-natifs-cheat-sheet)
3. [Les Fichiers de Config](#-les-fichiers-de-config)
4. [Comment Utiliser](#-comment-utiliser)
5. [Les 10 Workflows](#-les-10-workflows-one-shot)
6. [Mode Autonome](#-mode-autonome-god-mode)
7. [Bonnes Pratiques](#-bonnes-pratiques)

---

## Le Concept

### Avant (Chat classique)

Tu demandes -> Il code -> Tu testes -> Ca plante -> Tu corriges -> Boucle infinie.
**C'est toi le Manager.**

### Maintenant (Tasks)

Tu donnes une spec -> Il cree un plan de taches -> Il gere les dependances -> Il execute -> Il valide.
**C'est lui le Manager.**

### Pourquoi c'est puissant

Le systeme Task est **natif** a Claude Code. Contrairement a un chat qui perd le fil, l'objet Task reste ancre dans la session. Claude sait :
- Ou il en est (status de chaque tache)
- Ce qu'il a deja fait (taches `completed`)
- Ce qui bloque la suite (dependances `blockedBy`)

---

## Les Outils Natifs (Cheat-Sheet)

Claude Code dispose de 4 outils natifs pour gerer les taches.

### TaskCreate - L'Architecte

Cree une nouvelle tache. Toutes les taches sont creees avec le status `pending`.

| Parametre     | Requis | Description                                                    |
|---------------|--------|----------------------------------------------------------------|
| `subject`     | Oui    | Titre bref en forme imperative ("Fix auth bug")                |
| `description` | Oui    | Description detaillee, criteres d'acceptation                  |
| `activeForm`  | Non    | Forme au present continu pour le spinner ("Fixing auth bug")   |
| `metadata`    | Non    | Metadonnees arbitraires (objet cle/valeur)                     |

**Exemple dans un prompt :**
```
Cree une tache "Setup Database Schema" avec comme description
"Creer les tables Users et Projects avec Supabase".
```

### TaskUpdate - Le Manager

Met a jour une tache existante. C'est ici qu'on gere les **dependances**.

| Parametre      | Requis | Description                                              |
|----------------|--------|----------------------------------------------------------|
| `taskId`       | Oui    | L'ID de la tache a modifier                              |
| `status`       | Non    | `pending` -> `in_progress` -> `completed` (ou `deleted`) |
| `subject`      | Non    | Nouveau titre                                            |
| `description`  | Non    | Nouvelle description                                     |
| `activeForm`   | Non    | Nouvelle forme present continu                           |
| `addBlocks`    | Non    | IDs des taches que celle-ci bloque                       |
| `addBlockedBy` | Non    | IDs des taches qui bloquent celle-ci                     |
| `owner`        | Non    | Nom de l'agent proprietaire                              |
| `metadata`     | Non    | Metadonnees a fusionner (null pour supprimer une cle)    |

**Cycle de vie :** `pending` -> `in_progress` -> `completed`

**Dependances :** Une tache avec `blockedBy` non resolu ne peut pas etre reclamee.

### TaskGet - L'Inspecteur

Recupere les details complets d'une tache par son ID.

### TaskList - Le Dashboard

Liste toutes les taches avec leur status, owner et dependances. A utiliser :
- En debut de session pour reprendre le contexte
- Apres avoir termine une tache pour trouver la suivante
- Pour verifier l'avancement global

---

## Les Fichiers de Config

Ce dossier contient **3 fichiers templates** a copier dans ton projet.

### Structure

```
task/
├── README.md          # Ce guide
├── CLAUDE.md          # Template d'instructions projet (Task Protocol)
├── .claudeignore      # Template de fichiers a ignorer
└── .clauderc.json     # Exemple de config (non officiel, voir note)
```

### Fichier 1 : `CLAUDE.md` - Le Cerveau

> **Destination :** Racine de ton projet (`monProjet/CLAUDE.md`)

Ce fichier force Claude a utiliser le Task Protocol. Il contient :
- Le **role** de Claude (Tech Lead)
- Le **protocole Task** obligatoire (4 regles)
- Le **Tech Stack** (a personnaliser selon ton projet)

**Les 4 regles du protocole :**
1. **Initialisation** - Toujours commencer par `TaskList`
2. **Planification** - Jamais de code sans `TaskCreate` d'abord
3. **Atomicite** - Sous-taches de moins de 15 minutes
4. **Validation** - `completed` seulement si ca compile ET les tests passent

**Personnalisation :** Modifie la section `Tech Stack` selon ton projet :

```markdown
## Tech Stack
- Frontend: Next.js 14+ (App Router)
- Backend: Supabase / Node.js
- Styling: TailwindCSS
```

### Fichier 2 : `.claudeignore` - Le Focus

> **Destination :** Racine de ton projet (`monProjet/.claudeignore`)

Empeche Claude de lire des fichiers inutiles (economise des tokens = de l'argent).

```
node_modules/
.git/
.next/
dist/
coverage/
package-lock.json
yarn.lock
*.log
.env.local
```

Adapte selon ton projet (ex : ajouter `vendor/` pour PHP, `__pycache__/` pour Python).

### Fichier 3 : `.clauderc.json` - Exemple Conceptuel

> **Attention :** Ce fichier n'est **pas un format officiel** de Claude Code.
> Il illustre une configuration ideale. En pratique, les permissions Claude Code
> se gerent via `settings.json` (voir ci-dessous).

**Configuration reelle des permissions** (`~/.claude/settings.json` ou `.claude/settings.json`) :

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test)",
      "Bash(npm run build)",
      "Bash(npx jest*)"
    ],
    "deny": [
      "Bash(rm -rf*)",
      "Bash(git push*)"
    ]
  }
}
```

Voir le dossier `settings/` du depot principal pour des templates complets.

---

## Comment Utiliser

### Installation rapide

```bash
# Depuis la racine de ce depot (my-claude/)

# 1. Copier le CLAUDE.md dans ton projet
cp task/CLAUDE.md /chemin/vers/monProjet/CLAUDE.md

# 2. Copier le .claudeignore
cp task/.claudeignore /chemin/vers/monProjet/.claudeignore

# 3. (Optionnel) Personnaliser le Tech Stack dans CLAUDE.md
# Edite la section "Tech Stack" selon ton projet
```

### Premier lancement

Une fois les fichiers copies, lance Claude Code dans ton projet :

```bash
cd /chemin/vers/monProjet
claude
```

Claude lira automatiquement le `CLAUDE.md` et appliquera le Task Protocol.

### Exemple de session type

```
Toi:    "Analyse le projet et cree un plan de taches pour ajouter l'authentification Google."

Claude: [Utilise TaskList pour verifier l'etat existant]
        [Cree les taches via TaskCreate avec dependances]
        [Commence l'execution tache par tache]
        [Met a jour chaque tache via TaskUpdate]
```

### Commandes utiles en cours de session

| Tu dis...                                      | Claude fait...                      |
|------------------------------------------------|-------------------------------------|
| "Ou en es-tu ?"                                | `TaskList` -> resume l'avancement   |
| "Passe a la tache suivante"                    | `TaskUpdate` completed + suivante   |
| "Ajoute une tache pour les tests"              | `TaskCreate` avec description       |
| "Cette tache bloque la mise en prod"           | `TaskUpdate` avec `addBlocks`       |
| "Supprime cette tache, on en a plus besoin"    | `TaskUpdate` status `deleted`       |

---

## Les 10 Workflows One-Shot

Prompts prets a l'emploi. Copie-colle directement dans Claude Code.

### Architecture & Setup

**1. Micro-SaaS Builder**
```
Analyse spec.md. Utilise TaskCreate pour generer un plan de developpement
complet (DB > API > Front). Cree des dependances (blockedBy) pour que
le Front ne commence pas avant l'API. Execute le plan.
```

**2. Onboarding Nouveau Projet**
```
Je viens de cloner ce repo. Cree une tache "Onboarding" : analyse
l'architecture, cree un CLAUDE.md resume et installe les dependances.
Dis-moi quand c'est pret.
```

**3. Migration de Stack**
```
On passe de CSS a Tailwind. Cree une tache par composant.
Migre-les un par un. Ne passe au suivant que si le composant
actuel compile.
```

### Developpement

**4. Feature Branch Autonome**
```
Je veux ajouter l'auth Google. Cree une tache "Auth Setup".
Decoupe-la en sous-taches (Config, Callback, UI).
Execute et ne reviens vers moi que quand tout fonctionne.
```

**5. Tests Unitaires**
```
Pour chaque fichier dans /core, cree une tache "Write Tests".
Genere des tests Jest pour atteindre 80% de couverture.
```

**6. Documentation Auto-Update**
```
Parcours tout le dossier /src. Cree une tache "Update Docs".
Pour chaque fichier modifie recemment, mets a jour le README.md
et la doc technique.
```

### Qualite & Maintenance

**7. Bug Fix Loop (TDD)**
```
Cree une tache "Fix Login Bug".
1. Ecris un test qui reproduit le bug (il doit fail).
2. Corrige le code.
3. Valide que le test passe.
Update la tache.
```

**8. Refactoring Massif**
```
Ce code est sale. Cree une tache "Refactor Utils".
Deplace les fonctions dupliquees dans un fichier shared.
Verifie que rien n'est casse. Update.
```

**9. Audit de Securite**
```
Cree une tache "Security Audit". Scanne les fichiers pour trouver
des cles API en dur ou des injections SQL potentielles.
Liste chaque vulnerabilite comme une sous-tache bloquante.
```

**10. Release Manager**
```
Cree une tache "Release v1.0".
1. Run tous les tests.
2. Build le projet.
3. Si succes, genere le Changelog base sur les commits.
4. Prepare le commit de version.
```

---

## Mode Autonome ("God Mode")

> **Attention :** Ce mode permet a Claude d'executer des commandes sans confirmation.
> A utiliser uniquement dans un **environnement isole** (dossier de test, conteneur Docker).

Pour un maximum d'autonomie, pre-autorise les commandes courantes dans ton `settings.json` :

```json
{
  "permissions": {
    "allow": [
      "Read",
      "Write",
      "Edit",
      "Bash(npm test*)",
      "Bash(npm run*)",
      "Bash(npx *)",
      "Bash(node *)",
      "Bash(git add*)",
      "Bash(git commit*)",
      "Bash(git status)",
      "Bash(git diff*)",
      "Bash(git log*)",
      "Bash(mkdir*)",
      "Bash(ls*)"
    ],
    "deny": [
      "Bash(rm -rf /)*",
      "Bash(git push --force*)",
      "Bash(curl*|*sh)"
    ]
  }
}
```

Ou lance Claude Code directement avec le flag :

```bash
# Accepter toutes les permissions (dangereux !)
claude --dangerously-skip-permissions
```

Combine avec le **Workflow n.1** pour un developpeur autonome complet.

---

## Bonnes Pratiques

### A faire

- **Toujours commencer** par `TaskList` en debut de session
- **Decouper** les taches en morceaux atomiques (< 15 min)
- **Utiliser les dependances** (`addBlockedBy`) pour l'ordre d'execution
- **Valider** chaque tache (tests + compilation) avant de la marquer `completed`
- **Personnaliser** le `CLAUDE.md` avec ton vrai Tech Stack

### A eviter

- Ne pas creer de taches trop larges ("Faire tout le backend")
- Ne pas ignorer les status (passer directement de `pending` a `completed`)
- Ne pas activer le God Mode sur un projet en production
- Ne pas oublier de mettre a jour `.claudeignore` selon ton ecosysteme

### Integration avec le depot my-claude

Ce kit Task fonctionne avec les autres elements du depot :

| Element                | Synergie avec Tasks                                    |
|------------------------|--------------------------------------------------------|
| `/commit`              | Commiter apres chaque tache `completed`                |
| `/test`                | Valider avant de marquer une tache `completed`         |
| `/lint`                | S'assurer du formatage a chaque etape                  |
| `explore-and-plan`     | Creer le plan de taches initial (Workflow EPCT)        |
| Agents (backend, etc.) | Deleguer les sous-taches a des agents specialises      |

---

## Differences avec le contenu Notion original

> Ce README corrige quelques imprecisions du document source.

| Point                 | Notion (original)                    | Realite Claude Code                              |
|-----------------------|--------------------------------------|--------------------------------------------------|
| Nom du champ titre    | `title`                              | `subject`                                        |
| Status possibles      | `pending, running, completed, failed`| `pending, in_progress, completed, deleted`       |
| Dependances           | Sur `TaskCreate`                     | Sur `TaskUpdate` (apres creation)                |
| `.clauderc.json`      | Fichier de config officiel           | N'existe pas - utiliser `settings.json`          |
| `auto_approve`        | Dans `.clauderc.json`                | Via `settings.json` (`permissions.allow`) ou CLI |
