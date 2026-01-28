# Frontend Code Style & Linting

## Overview

This skill covers code style enforcement and linting for Vue.js/JavaScript/TypeScript frontend applications in Laravel projects.

## Tools

### ESLint

The standard JavaScript/TypeScript linter with Vue.js support.

#### Installation
```bash
npm install -D eslint @eslint/js
npm install -D eslint-plugin-vue
npm install -D @vue/eslint-config-typescript  # For TypeScript
npm install -D @vue/eslint-config-prettier    # Prettier integration
```

#### Configuration (eslint.config.js) - Flat Config
```javascript
import js from '@eslint/js'
import pluginVue from 'eslint-plugin-vue'
import vueTsEslintConfig from '@vue/eslint-config-typescript'
import skipFormatting from '@vue/eslint-config-prettier/skip-formatting'

export default [
    {
        name: 'app/files-to-lint',
        files: ['**/*.{ts,mts,tsx,vue,js,mjs,cjs}'],
    },
    {
        name: 'app/files-to-ignore',
        ignores: ['**/dist/**', '**/vendor/**', '**/node_modules/**', '**/public/**'],
    },
    js.configs.recommended,
    ...pluginVue.configs['flat/recommended'],
    ...vueTsEslintConfig(),
    skipFormatting,
    {
        rules: {
            // Vue specific
            'vue/multi-word-component-names': 'off',
            'vue/no-v-html': 'warn',
            'vue/require-default-prop': 'error',
            'vue/require-prop-types': 'error',
            'vue/component-definition-name-casing': ['error', 'PascalCase'],
            'vue/component-name-in-template-casing': ['error', 'PascalCase'],
            'vue/custom-event-name-casing': ['error', 'camelCase'],
            'vue/define-macros-order': ['error', {
                order: ['defineProps', 'defineEmits']
            }],
            'vue/block-order': ['error', {
                order: ['script', 'template', 'style']
            }],

            // General
            'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
            'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'off',
            'no-unused-vars': 'off',
            '@typescript-eslint/no-unused-vars': ['error', {
                argsIgnorePattern: '^_',
                varsIgnorePattern: '^_'
            }],
        }
    }
]
```

#### Legacy Configuration (.eslintrc.cjs)
```javascript
module.exports = {
    root: true,
    env: {
        browser: true,
        es2021: true,
        node: true,
    },
    extends: [
        'eslint:recommended',
        'plugin:vue/vue3-recommended',
        '@vue/eslint-config-typescript',
        '@vue/eslint-config-prettier/skip-formatting',
    ],
    parserOptions: {
        ecmaVersion: 'latest',
        sourceType: 'module',
    },
    rules: {
        'vue/multi-word-component-names': 'off',
        'vue/no-v-html': 'warn',
    },
}
```

#### Usage
```bash
# Check files
npx eslint resources/js/

# Fix automatically
npx eslint resources/js/ --fix

# Check specific files
npx eslint resources/js/Pages/Dashboard.vue
```

### Prettier

Code formatter for consistent styling.

#### Installation
```bash
npm install -D prettier prettier-plugin-tailwindcss
```

#### Configuration (.prettierrc)
```json
{
    "semi": false,
    "singleQuote": true,
    "tabWidth": 4,
    "trailingComma": "es5",
    "printWidth": 100,
    "bracketSameLine": false,
    "vueIndentScriptAndStyle": false,
    "singleAttributePerLine": true,
    "plugins": ["prettier-plugin-tailwindcss"]
}
```

#### Ignore File (.prettierignore)
```
node_modules
vendor
public
bootstrap
storage
*.blade.php
```

#### Usage
```bash
# Check formatting
npx prettier --check "resources/js/**/*.{js,ts,vue}"

# Fix formatting
npx prettier --write "resources/js/**/*.{js,ts,vue}"
```

### TypeScript

Static type checking for JavaScript.

#### Installation
```bash
npm install -D typescript vue-tsc
```

#### Configuration (tsconfig.json)
```json
{
    "compilerOptions": {
        "target": "ES2020",
        "useDefineForClassFields": true,
        "module": "ESNext",
        "lib": ["ES2020", "DOM", "DOM.Iterable"],
        "skipLibCheck": true,
        "moduleResolution": "bundler",
        "allowImportingTsExtensions": true,
        "resolveJsonModule": true,
        "isolatedModules": true,
        "noEmit": true,
        "jsx": "preserve",
        "strict": true,
        "noUnusedLocals": true,
        "noUnusedParameters": true,
        "noFallthroughCasesInSwitch": true,
        "paths": {
            "@/*": ["./resources/js/*"]
        }
    },
    "include": [
        "resources/js/**/*.ts",
        "resources/js/**/*.tsx",
        "resources/js/**/*.vue"
    ],
    "exclude": ["node_modules"]
}
```

#### Usage
```bash
# Type check
npx vue-tsc --noEmit

# Watch mode
npx vue-tsc --noEmit --watch
```

### Stylelint

Linter for CSS/SCSS/PostCSS.

#### Installation
```bash
npm install -D stylelint stylelint-config-standard stylelint-config-recommended-vue
```

#### Configuration (.stylelintrc.json)
```json
{
    "extends": [
        "stylelint-config-standard",
        "stylelint-config-recommended-vue"
    ],
    "rules": {
        "at-rule-no-unknown": [
            true,
            {
                "ignoreAtRules": ["tailwind", "apply", "variants", "responsive", "screen", "layer"]
            }
        ],
        "selector-class-pattern": null,
        "no-descending-specificity": null
    }
}
```

#### Usage
```bash
npx stylelint "resources/css/**/*.css" "resources/js/**/*.vue"
```

## Package.json Scripts

```json
{
    "scripts": {
        "dev": "vite",
        "build": "vite build",
        "lint": "eslint resources/js/",
        "lint:fix": "eslint resources/js/ --fix",
        "format": "prettier --write \"resources/js/**/*.{js,ts,vue}\"",
        "format:check": "prettier --check \"resources/js/**/*.{js,ts,vue}\"",
        "typecheck": "vue-tsc --noEmit",
        "style": "stylelint \"resources/css/**/*.css\" \"resources/js/**/*.vue\"",
        "check": "npm run lint && npm run typecheck && npm run format:check"
    }
}
```

## IDE Integration

### VS Code Extensions
- **Vue - Official** (Vue.volar) - Vue 3 language support
- **ESLint** - Linting integration
- **Prettier** - Code formatting
- **Tailwind CSS IntelliSense** - Tailwind autocomplete
- **TypeScript Vue Plugin (Volar)** - TypeScript support in Vue

### VS Code Settings (.vscode/settings.json)
```json
{
    "editor.formatOnSave": true,
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": "explicit"
    },
    "[vue]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[typescript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[javascript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "eslint.validate": [
        "javascript",
        "typescript",
        "vue"
    ],
    "typescript.tsdk": "node_modules/typescript/lib",
    "vue.server.hybridMode": true
}
```

### Recommended Extensions (.vscode/extensions.json)
```json
{
    "recommendations": [
        "Vue.volar",
        "dbaeumer.vscode-eslint",
        "esbenp.prettier-vscode",
        "bradlc.vscode-tailwindcss",
        "Vue.vscode-typescript-vue-plugin"
    ]
}
```

## CI/CD Integration

### GitHub Actions
```yaml
name: Frontend Quality

on: [push, pull_request]

jobs:
  frontend:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run ESLint
        run: npm run lint

      - name: Check formatting
        run: npm run format:check

      - name: Type check
        run: npm run typecheck

      - name: Build
        run: npm run build
```

## Vue 3 Style Guide

### Priority A: Essential (Error Prevention)

```vue
<!-- Always use multi-word component names -->
<!-- Bad -->
<template><Todo /></template>

<!-- Good -->
<template><TodoItem /></template>

<!-- Always define props with types -->
<script setup lang="ts">
// Bad
const props = defineProps(['status'])

// Good
const props = defineProps<{
    status: 'active' | 'inactive'
}>()
</script>

<!-- Always use :key with v-for -->
<template>
    <!-- Bad -->
    <li v-for="todo in todos">{{ todo.text }}</li>

    <!-- Good -->
    <li v-for="todo in todos" :key="todo.id">{{ todo.text }}</li>
</template>
```

### Priority B: Strongly Recommended

```vue
<script setup lang="ts">
// Component file names should be PascalCase
// ✓ MyComponent.vue
// ✗ myComponent.vue, my-component.vue

// Props should be camelCase in JavaScript
defineProps<{
    greetingText: string  // ✓
}>()

// Events should be camelCase
const emit = defineEmits<{
    updateValue: [value: string]  // ✓
}>()
</script>

<template>
    <!-- Props should be kebab-case in templates -->
    <MyComponent :greeting-text="text" />

    <!-- Events should be kebab-case in templates -->
    <MyComponent @update-value="handler" />

    <!-- Self-closing components when no content -->
    <MyComponent />
</template>
```

### Component Structure Order

```vue
<script setup lang="ts">
// 1. Imports
import { ref, computed, onMounted } from 'vue'
import type { User } from '@/types'
import UserAvatar from '@/Components/UserAvatar.vue'

// 2. Props
const props = defineProps<{
    user: User
    editable?: boolean
}>()

// 3. Emits
const emit = defineEmits<{
    save: [user: User]
    cancel: []
}>()

// 4. Composables
const { formatDate } = useDateFormatter()

// 5. Reactive state
const isEditing = ref(false)
const formData = ref({ ...props.user })

// 6. Computed
const fullName = computed(() => `${props.user.firstName} ${props.user.lastName}`)

// 7. Methods
function save() {
    emit('save', formData.value)
    isEditing.value = false
}

// 8. Lifecycle hooks
onMounted(() => {
    console.log('Component mounted')
})
</script>

<template>
    <div class="user-profile">
        <!-- Template content -->
    </div>
</template>

<style scoped>
/* Component styles */
</style>
```

## Pre-Commit Hook

Using lint-staged for efficient pre-commit checks:

```bash
npm install -D husky lint-staged
npx husky init
```

#### .husky/pre-commit
```bash
npx lint-staged
```

#### lint-staged.config.js
```javascript
export default {
    '*.{js,ts,vue}': ['eslint --fix', 'prettier --write'],
    '*.{css,scss}': ['stylelint --fix', 'prettier --write'],
    '*.{json,md}': ['prettier --write'],
}
```

## Tailwind CSS Conventions

### Class Order (via prettier-plugin-tailwindcss)
The plugin automatically sorts classes in this order:
1. Layout (display, position)
2. Flexbox/Grid
3. Spacing (margin, padding)
4. Sizing (width, height)
5. Typography
6. Backgrounds
7. Borders
8. Effects
9. Transitions
10. Responsive variants
11. State variants (hover, focus)

### Best Practices
```vue
<template>
    <!-- Use consistent class ordering -->
    <div class="flex items-center justify-between p-4 bg-white rounded-lg shadow-md hover:shadow-lg">

    <!-- Extract repeated patterns to components, not @apply -->
    <!-- Prefer components over @apply for reusability -->
    <Button variant="primary">Save</Button>
</template>
```
