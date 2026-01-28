# Security Rules for Laravel Development

## Authentication & Authorization

### Always verify authorization
```php
// In controllers - use authorize()
public function update(Post $post)
{
    $this->authorize('update', $post);
    // ...
}

// Or via Form Request
public function authorize(): bool
{
    return $this->user()->can('update', $this->route('post'));
}

// In Blade templates
@can('update', $post)
    <button>Edit</button>
@endcan
```

### Use policies for model authorization
```php
// Create policies for all models with user access
php artisan make:policy PostPolicy --model=Post
```

## Input Validation

### Always validate input
```php
// Use Form Requests - never validate in controllers
class StorePostRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'title' => ['required', 'string', 'max:255'],
            'body' => ['required', 'string'],
            'email' => ['required', 'email:rfc,dns'],
        ];
    }
}
```

### Sanitize output
```php
// In Blade - use {{ }} for escaped output
{{ $user->name }}

// Only use {!! !!} for trusted HTML
{!! $trustedHtml !!}
```

## Database Security

### Never use raw queries with user input
```php
// BAD - SQL injection vulnerability
DB::select("SELECT * FROM users WHERE email = '$email'");

// GOOD - Use parameter binding
DB::select('SELECT * FROM users WHERE email = ?', [$email]);

// BEST - Use Eloquent
User::where('email', $email)->first();
```

### Use mass assignment protection
```php
// Always define $fillable
protected $fillable = ['name', 'email'];

// Never allow sensitive fields
// BAD: protected $fillable = ['name', 'email', 'is_admin'];
```

## File Uploads

### Validate file uploads strictly
```php
'avatar' => [
    'required',
    'image',
    'mimes:jpg,jpeg,png',
    'max:2048', // 2MB
    'dimensions:max_width=2000,max_height=2000',
],
```

### Store files outside public directory
```php
// Store in storage/app (not public)
$path = $request->file('document')->store('documents');

// Only use 'public' disk for intentionally public files
$path = $request->file('avatar')->store('avatars', 'public');
```

## Session & CSRF

### Always include CSRF token in forms
```blade
<form method="POST">
    @csrf
    <!-- form fields -->
</form>
```

### For AJAX requests
```javascript
// Include in headers
axios.defaults.headers.common['X-CSRF-TOKEN'] = document.querySelector('meta[name="csrf-token"]').content;
```

## Sensitive Data

### Never log sensitive data
```php
// BAD
Log::info('User login', ['password' => $password]);

// GOOD
Log::info('User login', ['user_id' => $user->id]);
```

### Hide sensitive fields from JSON
```php
protected $hidden = [
    'password',
    'remember_token',
    'api_key',
];
```

### Use encryption for sensitive data
```php
// Encrypt before storing
$user->ssn = Crypt::encryptString($ssn);

// Decrypt when reading
$ssn = Crypt::decryptString($user->ssn);
```

## Rate Limiting

### Apply rate limiting to sensitive routes
```php
// In RouteServiceProvider or route file
Route::middleware(['throttle:6,1'])->group(function () {
    Route::post('/login', [AuthController::class, 'login']);
    Route::post('/forgot-password', [PasswordController::class, 'forgot']);
});
```

## Environment Security

### Never commit .env files
### Never use env() directly in code - use config()
### Keep debug mode off in production
### Use HTTPS in production
