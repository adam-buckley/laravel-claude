---
name: review
description: "Review code for Laravel best practices, security issues, and performance problems."
---

# Code Review Command

You perform thorough code reviews focused on Laravel applications.

## Usage

- `/review` - Review recently changed files (git diff)
- `/review path/to/file.php` - Review a specific file
- `/review app/Models/` - Review all files in a directory

## Review Checklist

When reviewing code, check for:

### Security
- [ ] SQL injection vulnerabilities (raw queries without bindings)
- [ ] Mass assignment vulnerabilities (missing $fillable/$guarded)
- [ ] XSS vulnerabilities (unescaped output)
- [ ] CSRF protection on forms
- [ ] Authorization checks (policies, gates)
- [ ] Sensitive data exposure in logs or responses
- [ ] Proper input validation

### Performance
- [ ] N+1 query problems (missing eager loading)
- [ ] Unnecessary database queries in loops
- [ ] Missing database indexes for frequent queries
- [ ] Large datasets loaded into memory
- [ ] Missing pagination on large collections
- [ ] Heavy operations that should be queued

### Laravel Best Practices
- [ ] Fat controllers (business logic should be in Actions/Services)
- [ ] Validation in controllers (should use Form Requests)
- [ ] Hardcoded values (should use config)
- [ ] Direct env() calls (should use config())
- [ ] Missing return types on methods
- [ ] Improper use of facades vs dependency injection
- [ ] Missing database transactions for multi-step operations

### Code Quality
- [ ] Dead code or commented-out code
- [ ] Duplicated logic that could be extracted
- [ ] Overly complex methods (should be broken down)
- [ ] Missing or incorrect PHPDoc comments
- [ ] Inconsistent naming conventions
- [ ] Magic numbers without constants

## Output Format

Provide feedback in this format:

### Critical Issues
Issues that must be fixed (security, data integrity)

### Improvements
Recommended changes for better code quality

### Suggestions
Optional enhancements and best practices

For each issue, include:
1. File and line number
2. Description of the problem
3. Suggested fix with code example
