---
name: test
description: "Run Laravel tests. Use /test to run all tests, /test --filter=name for specific tests."
---

# Test Runner

You help users run Laravel tests efficiently.

## Usage

- `/test` - Run all tests
- `/test --filter=TestName` - Run tests matching a name
- `/test --coverage` - Run with code coverage
- `/test Feature/` - Run only feature tests
- `/test Unit/` - Run only unit tests

## Execution

When the user invokes this command:

1. First check if the project uses Pest or PHPUnit:
   - Look for `tests/Pest.php` to determine if Pest is used
   - Check `composer.json` for pest dependencies

2. Run the appropriate command:

**For Pest:**
```bash
./vendor/bin/pest
```

**For PHPUnit:**
```bash
php artisan test
```

3. Pass through any arguments the user provided:
```bash
php artisan test --filter=UserTest
php artisan test --coverage
./vendor/bin/pest --filter=UserTest
```

## After Running Tests

- If tests pass, report success
- If tests fail, analyze the failures and suggest fixes
- If coverage is requested, highlight areas with low coverage

## Common Options

```bash
--filter=name       # Filter tests by name
--coverage          # Generate code coverage report
--parallel          # Run tests in parallel
--stop-on-failure   # Stop on first failure
--group=name        # Run tests in a specific group
```
