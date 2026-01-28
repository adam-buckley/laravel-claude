# PHP Code Style & Linting

## Overview

This skill covers code style enforcement and linting for PHP/Laravel applications using industry-standard tools.

## Tools

### Laravel Pint (Recommended)

Laravel Pint is the official PHP code style fixer built on PHP-CS-Fixer with Laravel-specific presets.

#### Installation
```bash
composer require laravel/pint --dev
```

#### Usage
```bash
# Fix all files
./vendor/bin/pint

# Preview changes without applying
./vendor/bin/pint --test

# Fix specific files/directories
./vendor/bin/pint app/Models
./vendor/bin/pint app/Http/Controllers/UserController.php

# Show verbose output
./vendor/bin/pint -v
```

#### Configuration (pint.json)
```json
{
    "preset": "laravel",
    "rules": {
        "simplified_null_return": true,
        "braces": {
            "allow_single_line_closure": true
        },
        "new_with_braces": {
            "anonymous_class": false,
            "named_class": true
        }
    },
    "exclude": [
        "bootstrap",
        "storage",
        "vendor"
    ]
}
```

#### Available Presets
- `laravel` - Laravel coding style (default)
- `psr12` - PSR-12 standard
- `symfony` - Symfony coding style
- `per` - PER Coding Style

### PHP_CodeSniffer (PHPCS)

Traditional PHP code sniffer for detecting coding standard violations.

#### Installation
```bash
composer require squizlabs/php_codesniffer --dev
```

#### Usage
```bash
# Check code
./vendor/bin/phpcs app/

# Fix automatically fixable issues
./vendor/bin/phpcbf app/

# Use specific standard
./vendor/bin/phpcs --standard=PSR12 app/
```

#### Configuration (phpcs.xml)
```xml
<?xml version="1.0"?>
<ruleset name="Laravel">
    <description>Laravel coding standard</description>

    <file>app</file>
    <file>config</file>
    <file>database</file>
    <file>routes</file>
    <file>tests</file>

    <exclude-pattern>bootstrap/*</exclude-pattern>
    <exclude-pattern>storage/*</exclude-pattern>
    <exclude-pattern>vendor/*</exclude-pattern>

    <arg name="colors"/>
    <arg value="sp"/>

    <rule ref="PSR12"/>

    <!-- Additional rules -->
    <rule ref="Generic.Arrays.DisallowLongArraySyntax"/>
    <rule ref="Generic.Formatting.SpaceAfterCast"/>
</ruleset>
```

### PHPStan (Static Analysis)

Static analysis tool that finds bugs without running code.

#### Installation
```bash
composer require phpstan/phpstan --dev
composer require larastan/larastan --dev  # Laravel extension
```

#### Usage
```bash
# Run analysis
./vendor/bin/phpstan analyse

# Specify level (0-9, higher = stricter)
./vendor/bin/phpstan analyse --level=5
```

#### Configuration (phpstan.neon)
```neon
includes:
    - vendor/larastan/larastan/extension.neon

parameters:
    paths:
        - app/
        - config/
        - routes/

    level: 5

    excludePaths:
        - vendor/

    ignoreErrors:
        - '#Call to an undefined method Illuminate\\Database\\Eloquent\\Builder#'

    checkMissingIterableValueType: false
```

#### PHPStan Levels
| Level | Checks |
|-------|--------|
| 0 | Basic checks, unknown classes, functions, methods |
| 1 | Possibly undefined variables, unknown magic methods |
| 2 | Unknown methods on all expressions |
| 3 | Return types, types assigned to properties |
| 4 | Basic dead code checking |
| 5 | Checking types of arguments |
| 6 | Report missing typehints |
| 7 | Report partially wrong union types |
| 8 | Report nullable issues |
| 9 | Strictest level |

### Psalm (Alternative Static Analysis)

Another powerful static analysis tool with good Laravel support.

#### Installation
```bash
composer require vimeo/psalm --dev
composer require psalm/plugin-laravel --dev
```

#### Configuration (psalm.xml)
```xml
<?xml version="1.0"?>
<psalm
    errorLevel="4"
    resolveFromConfigFile="true"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns="https://getpsalm.org/schema/config"
    xsi:schemaLocation="https://getpsalm.org/schema/config vendor/vimeo/psalm/config.xsd"
>
    <projectFiles>
        <directory name="app"/>
        <ignoreFiles>
            <directory name="vendor"/>
        </ignoreFiles>
    </projectFiles>

    <plugins>
        <pluginClass class="Psalm\LaravelPlugin\Plugin"/>
    </plugins>
</psalm>
```

## IDE Integration

### VS Code Extensions
- **PHP Intelephense** - Intelligent code completion and analysis
- **PHP CS Fixer** - Format on save
- **PHPStan** - Inline error highlighting

### VS Code Settings
```json
{
    "php.validate.enable": true,
    "php.suggest.basic": false,
    "[php]": {
        "editor.formatOnSave": true,
        "editor.defaultFormatter": "junstyle.php-cs-fixer"
    },
    "php-cs-fixer.executablePath": "${workspaceFolder}/vendor/bin/pint",
    "php-cs-fixer.config": "${workspaceFolder}/pint.json"
}
```

### PhpStorm
- Settings → PHP → Quality Tools → Laravel Pint
- Settings → Editor → Code Style → PHP → Set from → PSR-12

## Composer Scripts

Add to `composer.json`:
```json
{
    "scripts": {
        "lint": "./vendor/bin/pint --test",
        "lint:fix": "./vendor/bin/pint",
        "analyse": "./vendor/bin/phpstan analyse",
        "check": [
            "@lint",
            "@analyse"
        ]
    }
}
```

Usage:
```bash
composer lint      # Check style
composer lint:fix  # Fix style
composer analyse   # Static analysis
composer check     # Run all checks
```

## CI/CD Integration

### GitHub Actions
```yaml
name: Code Quality

on: [push, pull_request]

jobs:
  code-quality:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: '8.3'
          coverage: none

      - name: Install dependencies
        run: composer install --no-progress --prefer-dist

      - name: Check code style
        run: ./vendor/bin/pint --test

      - name: Run static analysis
        run: ./vendor/bin/phpstan analyse --error-format=github
```

## Common Style Rules

### PSR-12 Key Points
- 4 spaces for indentation (no tabs)
- Lines should be 120 characters or less
- Opening braces on same line for classes/methods
- One blank line after namespace declaration
- Visibility must be declared on all properties and methods
- `declare(strict_types=1)` at top of files (optional but recommended)

### Laravel-Specific Conventions
- Use `$request->validated()` instead of `$request->all()`
- Type-hint dependencies in constructors
- Use return type declarations
- Prefer `collect()` over `new Collection()`
- Use string interpolation over concatenation for simple cases

## Pre-Commit Hook

Create `.git/hooks/pre-commit`:
```bash
#!/bin/sh

# Run Pint on staged PHP files
STAGED_FILES=$(git diff --cached --name-only --diff-filter=ACM | grep '\.php$')

if [ -n "$STAGED_FILES" ]; then
    echo "Running Laravel Pint..."
    ./vendor/bin/pint --test $STAGED_FILES

    if [ $? -ne 0 ]; then
        echo "Code style issues found. Run './vendor/bin/pint' to fix."
        exit 1
    fi
fi

exit 0
```

Make executable:
```bash
chmod +x .git/hooks/pre-commit
```
