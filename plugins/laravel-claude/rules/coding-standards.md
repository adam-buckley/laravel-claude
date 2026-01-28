# Laravel Coding Standards

## PHP Standards

Follow PSR-12 coding style with Laravel conventions.

### Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Classes | PascalCase | `UserController`, `OrderService` |
| Methods | camelCase | `getUserById`, `processOrder` |
| Properties | camelCase | `$userName`, `$isActive` |
| Constants | UPPER_SNAKE | `MAX_ATTEMPTS`, `DEFAULT_ROLE` |
| Config keys | snake_case | `config('app.timezone')` |
| Database tables | snake_case, plural | `users`, `order_items` |
| Database columns | snake_case | `created_at`, `user_id` |
| Routes | kebab-case | `/user-profile`, `/order-items` |

### Type Declarations

Always use type declarations:

```php
// Method signatures
public function findById(int $id): ?User
{
    return User::find($id);
}

// Property types
private readonly UserRepository $userRepository;

// Return types for collections
public function getActiveUsers(): Collection
{
    return User::where('active', true)->get();
}
```

### Class Structure Order

1. Constants
2. Properties (public, protected, private)
3. Constructor
4. Public methods
5. Protected methods
6. Private methods

## Laravel Specific

### Controllers
- Keep controllers thin - delegate to Actions/Services
- Use Form Requests for validation
- One controller per resource (CRUD)
- Use resource controllers when appropriate

### Models
- Define `$fillable` explicitly (prefer over `$guarded`)
- Use `$casts` for type casting
- Define relationships with return types
- Keep business logic out of models

### Migrations
- Descriptive names: `create_users_table`, `add_email_to_users_table`
- Always define foreign key constraints
- Include rollback logic in `down()` method

### Routes
- Group related routes
- Use route model binding
- Name all routes
- Apply middleware at group level

```php
Route::middleware(['auth'])->group(function () {
    Route::resource('posts', PostController::class);
});
```

## Code Quality Rules

1. **No magic numbers** - Use constants or config
2. **No hardcoded strings** - Use translations or constants
3. **No env() in code** - Use config() instead
4. **No fat controllers** - Extract to Actions/Services
5. **No N+1 queries** - Use eager loading
6. **No raw queries without bindings** - Prevents SQL injection
7. **No mass assignment without protection** - Define $fillable
