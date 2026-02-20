# n8n Workflow Builder

Ce projet permet à Claude de créer, valider et gérer des workflows n8n de haute qualité via le serveur MCP n8n et des Skills dédiées.

## Environnement

- **Instance n8n** : [mapey.app.n8n.cloud](https://mapey.app.n8n.cloud/)
- **Serveur MCP n8n** : [czlonkowski/n8n-mcp](https://github.com/czlonkowski/n8n-mcp)
- **Serveur MCP GitHub** : [github/github-mcp-server](https://github.com/github/github-mcp-server)
- **Skills** : voir section [Available Skills](#available-skills) ci-dessous

### Configuration de l'accès n8n

La clé API n8n doit être définie via variable d'environnement (ne jamais stocker dans ce fichier) :

```bash
export N8N_API_KEY="<votre_clé_api>"
export N8N_BASE_URL="https://mapey.app.n8n.cloud"
```

> **Sécurité** : Ajoutez ces variables dans votre `.env` local (non versionné) ou dans les secrets de votre gestionnaire de configuration.

## Installation des Serveurs MCP

Les serveurs MCP sont configurés dans `.claude/settings.json` (déjà présent dans ce repo). Ils démarrent automatiquement quand Claude Code ouvre ce projet.

### Prérequis

- **Node.js** (pour n8n-mcp via npx) : `node --version` doit retourner v18+
- **Docker** (pour GitHub MCP) : `docker --version` doit fonctionner

### 1. Configurer les variables d'environnement

```bash
cp .env.example .env
# Éditez .env et remplissez N8N_API_KEY et GITHUB_TOKEN
```

### 2. Tester n8n-mcp

```bash
N8N_API_URL=https://mapey.app.n8n.cloud N8N_API_KEY=<votre_clé> npx n8n-mcp
# Doit démarrer sans erreur (Ctrl+C pour quitter)
```

### 3. Tester le GitHub MCP

```bash
docker pull ghcr.io/github/github-mcp-server
# Vérifier que l'image se télécharge correctement
```

### 4. Relancer Claude Code

Fermez et rouvrez Claude Code dans ce répertoire — les serveurs MCP démarreront automatiquement.

## Outils MCP Disponibles

### Documentation & Découverte

| Outil | Rôle |
|-------|------|
| `tools_documentation` | Accéder à la documentation des outils MCP |
| `search_nodes` | Recherche plein texte sur 1 084+ nœuds (filtre : core / community / verified) |
| `get_node` | Détails d'un nœud (modes : minimal / standard / full) |
| `validate_node` | Valider la configuration d'un nœud |
| `validate_workflow` | Validation complète du workflow (inclut les vérifications AI Agent) |
| `search_templates` | Recherche parmi 2 709+ templates (mots-clés / nœuds / tâche / métadonnées) |
| `get_template` | Récupérer le JSON complet d'un template |

### Gestion des Workflows

| Outil | Rôle |
|-------|------|
| `n8n_create_workflow` | Créer un nouveau workflow |
| `n8n_get_workflow` | Récupérer un workflow existant |
| `n8n_update_workflow` | Mise à jour complète d'un workflow |
| `n8n_update_partial_workflow` | Mise à jour partielle d'un workflow |
| `n8n_delete_workflow` | Supprimer un workflow |
| `n8n_list_workflows` | Lister tous les workflows |
| `n8n_validate_workflow` | Valider avant déploiement |

### Gestion des Exécutions

| Outil | Rôle |
|-------|------|
| `n8n_test_workflow` | Tester / déclencher un workflow |
| `n8n_list_executions` | Historique des exécutions |
| `n8n_get_execution` | Détails d'une exécution |
| `n8n_delete_execution` | Supprimer un enregistrement d'exécution |

## Available Skills

Les Skills suivantes s'activent automatiquement selon le contexte :

### n8n Skills — [czlonkowski/n8n-skills](https://github.com/czlonkowski/n8n-skills)

1. **n8n Expression Syntax** — Patterns `{{}}` corrects et accès aux variables
2. **n8n MCP Tools Expert** — Utilisation efficace des outils du serveur MCP
3. **n8n Workflow Patterns** — 5 approches architecturales éprouvées
4. **n8n Validation Expert** — Interpréter et résoudre les erreurs de validation
5. **n8n Node Configuration** — Configuration des nœuds selon l'opération
6. **n8n Code JavaScript** — JavaScript dans les nœuds Code
7. **n8n Code Python** — Python avec les limitations spécifiques n8n

### Frontend Design Skill — [anthropics/claude-code](https://github.com/anthropics/claude-code/blob/main/plugins/frontend-design/skills/frontend-design/SKILL.md)

8. **Frontend Designer** — Conception UI/UX, composants React, CSS, accessibilité et meilleures pratiques design

## Processus de Construction de Workflows

### 1. Comprendre les Besoins
- Clarifier l'objectif et le déclencheur du workflow
- Identifier les intégrations requises et le flux de données
- Définir la stratégie de gestion des erreurs

### 2. Chercher des Templates en Premier
```
search_templates → Trouver des workflows similaires
get_template     → Obtenir le JSON comme point de départ
```

### 3. Rechercher les Nœuds
```
search_nodes → Trouver les nœuds appropriés
get_node     → Obtenir les détails de configuration
```

### 4. Construire de Façon Incrémentale
- Commencer par le nœud déclencheur (trigger)
- Ajouter les nœuds un par un
- Valider après chaque ajout

### 5. Valider Avant le Déploiement
```
validate_workflow → Vérifier les erreurs
Corriger les problèmes → Re-valider
```

### 6. Tester
```
n8n_test_workflow → Exécuter avec des données de test
Vérifier les sorties → Ajuster si nécessaire
```

## Règles de Sécurité

- **NE JAMAIS modifier directement les workflows en production** — Toujours travailler sur des copies
- **NE JAMAIS déployer sans validation** — Utiliser `validate_workflow` au préalable
- **NE JAMAIS sauter les tests** — Toujours tester avec des données réalistes
- **NE JAMAIS utiliser des valeurs par défaut à l'aveugle** — Configurer les paramètres explicitement

## Standards de Qualité

### Avant la Création
- Rechercher des templates pour des patterns existants
- Comprendre toutes les configurations de nœuds requises
- Planifier la stratégie de gestion des erreurs

### Pendant la Construction
- Valider les nœuds au fur et à mesure de leur ajout
- Utiliser la syntaxe d'expression n8n correcte
- Suivre les patterns de workflow établis

### Avant le Déploiement
- Exécuter `validate_workflow` avec le profil strict
- Tester avec des données représentatives
- Vérifier que la gestion des erreurs fonctionne

## Patterns de Workflows

Utiliser ces 5 patterns éprouvés comme fondations architecturales :

1. **Webhook Processing** — Déclencheur externe → Traitement → Réponse
2. **HTTP API Integration** — Récupération de données → Transformation → Stockage/Envoi
3. **Database Operations** — Requête → Traitement → Mise à jour
4. **AI Workflows** — Entrée → Traitement IA → Gestion de la sortie
5. **Scheduled Tasks** — Déclencheur Cron → Traitement par lot → Rapport

## Référence de Syntaxe des Expressions

```javascript
// Accéder aux données d'entrée
{{ $json.fieldName }}

// Accéder à la sortie d'un nœud précédent
{{ $('NodeName').item.json.field }}

// Accéder à tous les items d'un nœud
{{ $('NodeName').all() }}

// Logique conditionnelle
{{ $json.status === 'active' ? 'oui' : 'non' }}

// Date / heure
{{ $now.toISO() }}
{{ $today.format('yyyy-MM-dd') }}

// Corps d'une requête webhook
{{ $json.body.fieldName }}

// Accès aux métadonnées d'exécution
{{ $execution.id }}
{{ $workflow.name }}
```

## Erreurs Courantes à Éviter

- Utiliser des expressions `{{ }}` à l'intérieur des nœuds Code (utiliser des variables à la place)
- Oublier `$json.body` pour accéder aux données d'un webhook
- Ne pas gérer les valeurs vides / null
- Sauter la validation avant le déploiement
- Modifier directement des workflows en production
- Utiliser des valeurs par défaut sans vérification explicite
- Ne pas tester avec des données représentatives du cas réel
