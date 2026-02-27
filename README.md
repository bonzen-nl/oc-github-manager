# GitHub Manager — OpenClaw Skill

**Autonoom GitHub repository-, project- en issue-management voor OpenClaw.**

Volledige controle over GitHub-operaties: repos aanmaken, Projects V2 Kanban-borden inrichten, issues beheren, pull requests tracken — alles geautomatiseerd en geïntegreerd met Software-Architect.

---

## 🎯 Wat doet GitHub Manager?

GitHub Manager is een **autonome orchestrator-skill** die het volledige GitHub-ecosysteem beheert voor OpenClaw-projecten:

- **Repository Management** — Maak GitHub repositories aan, kloon ze lokaal, beheer branches
- **GitHub Projects V2** — Kanban-borden (To Do → In Progress → Done) met custom fields
- **Issue & Story Management** — Automatisch Nederlandse issues uit user stories
- **Pull Request Management** — PR création, commenting, status tracking
- **Project Status Sync** — Bidirectionele sync via `.mavy/project_status.json`
- **Cost Tracking** — Integratie met Software-Architect's `token_usage.db`

### 🔄 Architectuur

```
OpenClaw Skill Ecosystem
│
├─ Software-Architect (Orchestrator)
│  ├─ Project routing & cost tracking
│  └─ API gateway
│
└─ github_manager (Autonomous Executor)
   ├─ GitHub REST API wrapper
   ├─ GraphQL mutations (Projects V2)
   ├─ .mavy/project_status.json sync
   └─ Telegram notifications
```

**Workflow:**

```
User Request
    ↓
Software-Architect (analyzeert project)
    ↓
github_manager (voert uit)
    ├─ Create repo
    ├─ Create project board
    ├─ Create issues
    ├─ Sync status
    └─ Log to token_usage.db
    ↓
Telegram Alert (Bob op hoogte)
```

---

## 📦 Afhankelijkheden

### Systeemvereisten
- **Python:** 3.8+
- **Git:** 2.30+
- **GitHub Account:** Met Personal Access Token (PAT)

### Python Dependencies

```
requests>=2.28.0          # GitHub REST API calls
python-dotenv>=0.20.0     # .env variable loading
```

### GitHub Configuration

**Vereiste scopes in Personal Access Token:**
- `repo` — Full control of repositories
- `workflow` — GitHub Actions workflows
- `project` — GitHub Projects management
- `write:discussion` — Write to discussions

[GitHub PAT Generator](https://github.com/settings/tokens)

---

## ⚡ Quickstart

### 1. Installatie

```bash
# Clone de repository
git clone https://github.com/bonzen-nl/oc-github-manager
cd oc-github-manager

# Maak virtual environment
python3 -m venv .venv
source .venv/bin/activate

# Installeer dependencies
pip install -r requirements.txt
```

### 2. Configuratie

```bash
# Kopieer template
cp .env.example .env

# Vul in:
# GITHUB_PAT=ghp_xxxxxxxxxxxx
# TELEGRAM_CHAT_ID=123456789
```

### 3. Test Connection

```bash
./github-manager test
# Output: ✅ GitHub API authenticated (user_id: MDQ6VXNlcjI0MTg0NDY=)
```

---

## 🚀 Gebruik

### Repository Initialiseren

```bash
./github-manager init \
  --repo-name mijn-project \
  --repo-description "Beschrijving in Nederlands" \
  --owner bonzen-nl \
  --local-path /tmp/mijn-project
```

**Wat gebeurt er:**
- ✅ Maak GitHub repository aan
- ✅ Clone lokaal naar `/tmp/mijn-project`
- ✅ Initialiseer `.mavy/project_status.json`
- ✅ Eerste commit & push

### GitHub Project V2 Board

```bash
./github-manager project create \
  --repo mijn-project \
  --owner bonzen-nl \
  --project-name "Sprint 1: MVP"
```

**Kanban kolommen:**
- To Do
- In Progress
- Done

### Issue Aanmaken

```bash
./github-manager issue create \
  --repo mijn-project \
  --owner bonzen-nl \
  --title "Implementeer gebruikersauthenticatie" \
  --description "Als gebruiker wil ik kunnen inloggen met email/wachtwoord" \
  --project-id PRJV2_xxxx \
  --column "To Do"
```

**Automatisch:**
- ✅ Issue in Nederlands
- ✅ Toegevoegd aan project board
- ✅ Synced in `.mavy/project_status.json`

### Pull Request

```bash
./github-manager pr create \
  --repo mijn-project \
  --owner bonzen-nl \
  --title "Voeg OAuth2-authenticatie toe" \
  --description "Implementeert Google & GitHub login" \
  --branch feature/oauth2
```

---

## 🏗️ Projectstructuur

```
oc-github-manager/
├── SKILL.md                          # Skill documentatie
├── github-manager                    # Bash wrapper (activates venv)
├── github-manager.py                 # CLI entry point
├── .env.example                      # Configuration template
├── .gitignore                        # Git security
├── LICENSE                           # MIT
├── config/
│   └── github_manager.json           # Settings
├── scripts/
│   ├── github_api.py                 # REST + GraphQL API
│   ├── project_status_manager.py     # .mavy/ sync
│   └── dutch_content.py              # NL validation
├── references/
│   └── api-spec.md                   # GitHub API reference
└── .venv/                            # Virtual environment
```

---

## 🔗 Sub-Projecten & Integraties

GitHub Manager is onderdeel van het **OpenClaw Skills Ecosystem**. Gerelateerde repositories:

### Master Hub
- **[oc-overzicht](https://github.com/bonzen-nl/oc-overzicht)** — Centrale index van alle skills

### Gerelateerde Skills
- **[oc-software-architect](https://github.com/bonzen-nl/oc-software-architect)** — Orchestrator die github_manager oproept
- **[oc-server-status](https://github.com/bonzen-nl/oc-server-status)** — Server monitoring (kan statussen rapporteren via GitHub issues)
- **[oc-ram-guardian](https://github.com/bonzen-nl/oc-ram-guardian)** — RAM monitoring (alerts via GitHub issues)
- **[oc-openclaw-expert](https://github.com/bonzen-nl/oc-openclaw-expert)** — RAG system voor OpenClaw docs

### Integration Points

**Software-Architect → github_manager:**
- Architect analyzeert project scope
- Roept github_manager aan voor repo-setup
- Logt kosten naar `token_usage.db`
- Stuurt Telegram alerts

**github_manager → .mavy/project_status.json:**
```json
{
  "project_id": "PRJV2_abc123",
  "project_name": "Mijn Project",
  "repository": {
    "name": "mijn-project",
    "owner": "bonzen-nl",
    "url": "https://github.com/bonzen-nl/mijn-project"
  },
  "kanban_board": {
    "to_do": 5,
    "in_progress": 2,
    "done": 8
  },
  "last_updated": "2026-02-27T18:30:00Z"
}
```

---

## 🔐 Veiligheid

### Environment Variables

**NOOIT commiten:**
```bash
.env                   # Contains GITHUB_PAT
secrets.json           # API credentials
credentials.json       # OAuth tokens
```

**Veilig voor publiek:**
```bash
.env.example           # Template zonder waarden
```

### Token Management

- GitHub PAT gelezen van `/Users/bonzen/.openclaw/workspace/.env`
- Nooit logged, nooit in output
- `.gitignore` beschermt alle secrets
- 600-permission bestand op Mac Mini

---

## 📚 Volledige Documentatie

Voor geavanceerde gebruikers en API-reference:

- **[SKILL.md](./SKILL.md)** — Skill-definitie en workflows
- **[references/api-spec.md](./references/api-spec.md)** — GitHub REST & GraphQL API details
- **[config/github_manager.json](./config/github_manager.json)** — Configuration options

---

## 🧪 Testing

```bash
# Test GitHub connection
./github-manager test

# Dry-run repo creation (plan only)
./github-manager init \
  --repo-name test-repo \
  --repo-description "Test" \
  --dry-run
```

---

## 🐛 Troubleshooting

### "GitHub API Unauthorized"
- Controleer GITHUB_PAT in `.env`
- Verify token scopes: `repo, workflow, project, write:discussion`
- Token verlopen? Genereer nieuwe via [GitHub Settings](https://github.com/settings/tokens)

### "Repository already exists"
- Repo bestaat al onder `bonzen-nl/repo-name`
- Gebruik ander naam of use `--overwrite` flag

### "GraphQL mutation failed"
- Projects V2 API vereist enterprise/pro plan
- Check: `github.com/{owner}/{repo}/projects`

---

## 🤝 Bijdragen

Issues, PRs, en feedback welkom!

[Open een issue](https://github.com/bonzen-nl/oc-github-manager/issues)

---

## 📝 Licentie

MIT © 2026 Bonzen

---

## 📬 Ondersteuning

- **GitHub Issues:** [oc-github-manager/issues](https://github.com/bonzen-nl/oc-github-manager/issues)
- **Integration Help:** Zie [oc-software-architect](https://github.com/bonzen-nl/oc-software-architect) voor orchestrator-integratie

---

**Onderdeel van:** [OpenClaw Skills Suite](https://github.com/bonzen-nl/oc-overzicht)

**Laat gehost en beheerd door:** Software-Architect + github_manager (autonoom)
