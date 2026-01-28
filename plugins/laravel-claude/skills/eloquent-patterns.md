# Eloquent ORM Patterns

## Model Conventions

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\HasMany;

class Post extends Model
{
    use HasFactory;

    protected $fillable = [
        'title',
        'slug',
        'body',
        'published_at',
        'user_id',
    ];

    protected $casts = [
        'published_at' => 'datetime',
        'metadata' => 'array',
        'is_featured' => 'boolean',
    ];

    // Relationships
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }

    public function comments(): HasMany
    {
        return $this->hasMany(Comment::class);
    }

    // Scopes
    public function scopePublished(Builder $query): Builder
    {
        return $query->whereNotNull('published_at')
            ->where('published_at', '<=', now());
    }

    public function scopeFeatured(Builder $query): Builder
    {
        return $query->where('is_featured', true);
    }

    // Accessors & Mutators (Laravel 9+ syntax)
    protected function title(): Attribute
    {
        return Attribute::make(
            get: fn (string $value) => ucfirst($value),
            set: fn (string $value) => strtolower($value),
        );
    }
}
```

## Eager Loading (N+1 Prevention)

```php
// Bad - N+1 problem
$posts = Post::all();
foreach ($posts as $post) {
    echo $post->user->name; // Query per iteration
}

// Good - Eager loading
$posts = Post::with('user')->get();

// Nested eager loading
$posts = Post::with(['user', 'comments.user'])->get();

// Constrained eager loading
$posts = Post::with(['comments' => function ($query) {
    $query->where('approved', true)->orderBy('created_at', 'desc');
}])->get();

// Lazy eager loading (when you already have models)
$posts->load('comments');
```

## Query Scopes

```php
// Local scope
public function scopeActive(Builder $query): Builder
{
    return $query->where('status', 'active');
}

// Usage
Post::active()->get();
Post::active()->published()->get();

// Dynamic scope
public function scopeOfType(Builder $query, string $type): Builder
{
    return $query->where('type', $type);
}

// Usage
Post::ofType('blog')->get();
```

## Query Optimization

```php
// Select only needed columns
User::select(['id', 'name', 'email'])->get();

// Use chunk for large datasets
User::chunk(200, function ($users) {
    foreach ($users as $user) {
        // Process user
    }
});

// Or lazy collections for memory efficiency
User::lazy()->each(function ($user) {
    // Process user
});

// Use cursor for memory-efficient iteration
foreach (User::cursor() as $user) {
    // Process user
}

// Efficient existence checks
if (Post::where('slug', $slug)->exists()) { }

// Count without loading models
$count = Post::where('status', 'published')->count();
```

## Relationships

```php
// One to One
public function profile(): HasOne
{
    return $this->hasOne(Profile::class);
}

// One to Many
public function posts(): HasMany
{
    return $this->hasMany(Post::class);
}

// Many to Many
public function roles(): BelongsToMany
{
    return $this->belongsToMany(Role::class)
        ->withPivot('assigned_at')
        ->withTimestamps();
}

// Has Many Through
public function countryPosts(): HasManyThrough
{
    return $this->hasManyThrough(Post::class, User::class);
}

// Polymorphic
public function commentable(): MorphTo
{
    return $this->morphTo();
}

public function comments(): MorphMany
{
    return $this->morphMany(Comment::class, 'commentable');
}
```

## Model Events & Observers

```php
// In the model (for simple cases)
protected static function booted(): void
{
    static::creating(function (Post $post) {
        $post->slug = Str::slug($post->title);
    });

    static::deleting(function (Post $post) {
        $post->comments()->delete();
    });
}

// Observer (for complex cases)
namespace App\Observers;

class PostObserver
{
    public function creating(Post $post): void
    {
        $post->slug = Str::slug($post->title);
    }

    public function deleted(Post $post): void
    {
        Storage::delete($post->featured_image);
    }
}

// Register in AppServiceProvider
Post::observe(PostObserver::class);
```

## Soft Deletes

```php
use Illuminate\Database\Eloquent\SoftDeletes;

class Post extends Model
{
    use SoftDeletes;
}

// Queries automatically exclude soft deleted
Post::all(); // Only non-deleted

// Include soft deleted
Post::withTrashed()->get();

// Only soft deleted
Post::onlyTrashed()->get();

// Restore
$post->restore();

// Permanent delete
$post->forceDelete();
```

## Mass Assignment Protection

```php
// Whitelist approach (recommended)
protected $fillable = ['title', 'body'];

// Blacklist approach
protected $guarded = ['id', 'created_at'];

// Allow all (dangerous - use carefully)
protected $guarded = [];
```

## Database Transactions

```php
use Illuminate\Support\Facades\DB;

// Automatic transaction
DB::transaction(function () {
    $user = User::create([...]);
    $user->profile()->create([...]);
});

// Manual transaction
DB::beginTransaction();
try {
    // Operations
    DB::commit();
} catch (\Exception $e) {
    DB::rollBack();
    throw $e;
}
```
