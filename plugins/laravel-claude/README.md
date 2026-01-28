# WARNING

> This plugin was like 95% made with Claude prompts, it is untested and purely made to help me understand SDD. Use at your own risk.

# Laravel Claude Plugin

A comprehensive Claude Code plugin for Laravel/PHP development with Vue and Inertia support.

## Features

- **11 Specialized Agents** for architecture, frontend, testing, security, and more
- **5 Knowledge Skills** covering best practices, Eloquent, testing, and code style
- **3 Commands** for artisan, testing, and code review
- **3 Rule Sets** for coding standards, security, and commit practices

## Installation

```bash
/plugin marketplace add https://github.com/adam-buckley/laravel-claude
/plugin install laravel-claude
```

## Laravel Boost MCP Server

[Laravel Boost](https://laravel.com/ai/boost) is an AI-powered development assistant that provides essential context and structure for generating high-quality, Laravel-specific code. It integrates with Claude Code via MCP (Model Context Protocol) to give you access to 15 specialized development tools.

### Boost Tools

| Tool | Description |
|------|-------------|
| **Application Info** | Reads PHP & Laravel versions, database engine, ecosystem packages, and Eloquent models |
| **Database Schema** | Inspects and reads complete database schema with intelligent analysis |
| **Database Queries** | Executes queries against your database directly from Claude |
| **Route Inspector** | Inspects and analyzes application routes |
| **Artisan Commands** | Lists and inspects available Artisan commands |
| **Tinker Integration** | Executes code within Laravel application context |
| **Configuration Access** | Gets configuration values and available keys |
| **Documentation Search** | Queries Laravel's documentation API fine-tuned to installed packages |
| **Error Tracking** | Reads application logs, browser errors, and tracks encountered issues |

### Installation

#### Step 1: Install Laravel Boost

In your Laravel project directory:

```bash
composer require laravel/boost --dev
```

#### Step 2: Run the Boost Installer

```bash
php artisan boost:install
```

#### Step 3: Configure Claude Code

Add the Boost MCP server to your Claude Code configuration. Edit your project's `.claude/settings.json` or `~/.claude/settings.json`:

```json
{
  "mcpServers": {
    "laravel-boost": {
      "command": "php",
      "args": ["artisan", "boost:mcp"],
      "cwd": "/path/to/your/laravel/project"
    }
  }
}
```

Replace `/path/to/your/laravel/project` with the absolute path to your Laravel application.

#### Step 4: Verify Installation

Restart Claude Code and run `/mcp` to verify the Laravel Boost server is connected. You should see the Boost tools available.

### Usage

Once configured, Claude Code will automatically have access to your Laravel application context, including:

- Database schema and queries
- Route definitions
- Configuration values
- Application logs
- Artisan commands
- Laravel documentation

This enables more accurate code generation and debugging assistance specific to your Laravel application.

## Agents

### Architecture & Planning

| Agent | Model | Description |
|-------|-------|-------------|
| **laravel-architect** | Opus | Design system architecture, plan features, make architectural decisions |
| **api-designer** | Opus | Design RESTful APIs, authentication, resources, versioning |

### Frontend Development

| Agent | Model | Description |
|-------|-------|-------------|
| **vue-inertia-frontend** | Sonnet | Build Vue.js/Inertia components, pages, ensure DRY principles |
| **ui-designer** | Sonnet | Create wireframes, mockups, user flows, component designs |

### Code Quality

| Agent | Model | Description |
|-------|-------|-------------|
| **testing-expert** | Sonnet | Write tests, TDD workflow, factories, mocking, coverage |
| **code-reviewer** | Opus | Deep code review for security, performance, best practices |
| **refactoring-expert** | Sonnet | Extract services/actions, modernize code, reduce technical debt |

### Database & Backend

| Agent | Model | Description |
|-------|-------|-------------|
| **database-expert** | Sonnet | Schema design, migrations, query optimization, indexing |

### Debugging & Security

| Agent | Model | Description |
|-------|-------|-------------|
| **debugger** | Sonnet | Troubleshoot errors, analyze stack traces, fix bugs |
| **security-auditor** | Opus | OWASP Top 10 audits, vulnerability scanning, security review |

### Documentation

| Agent | Model | Description |
|-------|-------|-------------|
| **docs-expert** | Haiku | Create READMEs, changelogs, PHPDoc comments |

## Commands

| Command | Description |
|---------|-------------|
| `/artisan` | Run Laravel Artisan commands quickly |
| `/test` | Run tests with Pest or PHPUnit |
| `/review` | Review code for best practices and security |

## Skills

The plugin includes knowledge bases for:

| Skill | Topics Covered |
|-------|----------------|
| **Laravel Best Practices** | Controllers, actions, service container, events, Form Requests |
| **Eloquent Patterns** | Models, relationships, scopes, eager loading, optimization |
| **Testing Strategies** | Feature tests, unit tests, mocking, factories, database testing |
| **PHP Code Style** | Laravel Pint, PHPStan, PHP_CodeSniffer, static analysis, CI/CD |
| **Frontend Code Style** | ESLint, Prettier, TypeScript, Stylelint, Vue 3 style guide |

## Rules

Enforced coding standards and practices:

| Rule | Description |
|------|-------------|
| **Coding Standards** | PSR-12, naming conventions, type declarations, Laravel patterns |
| **Security Rules** | Authorization, validation, SQL injection prevention, XSS, CSRF |
| **Commit Often** | Small frequent commits, message format, workflow integration |

## Directory Structure

```
plugins/laravel-claude/
├── .claude-plugin/
│   └── plugin.json
├── agents/
│   ├── api-designer.md
│   ├── code-reviewer.md
│   ├── database-expert.md
│   ├── debugger.md
│   ├── docs-expert.md
│   ├── laravel-architect.md
│   ├── refactoring-expert.md
│   ├── security-auditor.md
│   ├── testing-expert.md
│   ├── ui-designer.md
│   └── vue-inertia-frontend.md
├── commands/
│   ├── artisan.md
│   ├── review.md
│   └── test.md
├── skills/
│   ├── eloquent-patterns.md
│   ├── frontend-code-style.md
│   ├── laravel-best-practices.md
│   ├── php-code-style.md
│   └── testing-strategies.md
├── rules/
│   ├── coding-standards.md
│   ├── commit-often.md
│   └── security-rules.md
└── README.md
```

## Agent Models

The plugin uses different Claude models based on task complexity:

- **Opus** - Complex reasoning tasks (architecture, API design, security audits, code review)
- **Sonnet** - Implementation tasks (coding, testing, debugging, refactoring)
- **Haiku** - Quick tasks (documentation)

## License

MIT
