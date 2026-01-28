# Commit Often Rule

## Philosophy

Small, frequent commits create a better development workflow:
- **Easier code review** - Reviewers can understand changes incrementally
- **Safer refactoring** - Easy to revert if something breaks
- **Better history** - Git log tells the story of how code evolved
- **Reduced merge conflicts** - Smaller changes = fewer conflicts
- **Continuous progress** - Each commit is a checkpoint

## When to Commit

Commit after completing any of these:

### Logical Units of Work
- A single feature or function
- A bug fix
- A refactoring step
- Adding or updating tests
- Configuration changes
- Documentation updates

### Natural Checkpoints
- Before starting a risky change
- After getting tests to pass
- After completing a TODO item
- Before switching to a different task
- Before taking a break

## Commit Message Guidelines

### Format
```
<type>: <short description>

[optional body with more detail]

[optional footer with references]
```

### Types
- `feat:` - New feature
- `fix:` - Bug fix
- `refactor:` - Code restructuring without behavior change
- `test:` - Adding or updating tests
- `docs:` - Documentation changes
- `style:` - Formatting, whitespace (no code change)
- `chore:` - Build, config, dependencies

### Examples
```
feat: add user registration endpoint

fix: prevent duplicate order submissions

refactor: extract shipping calculation to service class

test: add coverage for order cancellation flow

docs: update API authentication section
```

### Good Commit Messages
- Use imperative mood ("add feature" not "added feature")
- Keep first line under 72 characters
- Explain WHY, not just WHAT (the diff shows what)
- Reference issue numbers when applicable

## Anti-Patterns to Avoid

### Don't Do This
- `git commit -m "WIP"`
- `git commit -m "fixes"`
- `git commit -m "asdfasdf"`
- Committing multiple unrelated changes together
- Waiting until end of day to commit everything
- Giant commits with 50+ changed files

### Instead
- Commit each logical change separately
- Use `git add -p` to stage specific hunks
- Write meaningful messages even for small changes

## Workflow Integration

### Before Starting Work
```bash
git pull                    # Get latest changes
git checkout -b feature/x   # Create feature branch
```

### During Development
```bash
# After completing a unit of work
git add app/Services/OrderService.php
git commit -m "feat: add order total calculation"

# After fixing a bug found during development
git add app/Models/Order.php
git commit -m "fix: handle null shipping address"

# After adding tests
git add tests/Feature/OrderTest.php
git commit -m "test: add order creation tests"
```

### End of Session
```bash
git status                  # Check for uncommitted work
git push                    # Push to remote
```

## Recommended Commit Frequency

| Task Duration | Suggested Commits |
|--------------|-------------------|
| 30 min fix | 1-2 commits |
| 2-hour feature | 3-5 commits |
| Full day feature | 5-10 commits |
| Multi-day epic | 10+ commits |

## Tools to Help

### Interactive Staging
```bash
git add -p                  # Stage specific changes
git add -i                  # Interactive mode
```

### Amending (Use Carefully)
```bash
git commit --amend          # Fix last commit (before push only!)
```

### Reviewing Before Commit
```bash
git diff --staged           # See what you're about to commit
```

Remember: It's better to have too many small commits than too few large ones. You can always squash commits later, but you can't easily split them.
