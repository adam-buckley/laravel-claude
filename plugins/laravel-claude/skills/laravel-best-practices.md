# Laravel Best Practices

## Directory Structure

Laravel 11+ uses a streamlined directory structure. Key conventions:

```
app/
├── Actions/          # Single-purpose action classes
├── Console/          # Artisan commands
├── Enums/            # PHP 8.1+ enums
├── Events/           # Event classes
├── Exceptions/       # Custom exceptions
├── Http/
│   ├── Controllers/  # Keep thin, delegate to actions/services
│   ├── Middleware/
│   └── Requests/     # Form request validation
├── Jobs/             # Queue jobs
├── Listeners/        # Event listeners
├── Mail/             # Mailable classes
├── Models/           # Eloquent models
├── Notifications/
├── Policies/         # Authorization policies
├── Providers/        # Service providers
├── Rules/            # Custom validation rules
└── Services/         # Business logic services
```

## Controller Best Practices

### Keep Controllers Thin
Controllers should only:
1. Validate input (via Form Requests)
2. Call actions/services
3. Return responses

```php
// Good - thin controller
class OrderController extends Controller
{
    public function store(StoreOrderRequest $request, CreateOrderAction $action)
    {
        $order = $action->execute($request->validated());

        return redirect()->route('orders.show', $order);
    }
}

// Bad - fat controller with business logic
class OrderController extends Controller
{
    public function store(Request $request)
    {
        $validated = $request->validate([...]); // Use Form Requests

        // Business logic should be in Actions/Services
        $order = new Order($validated);
        $order->calculateTax();
        $order->applyDiscount();
        $order->save();

        // Notification logic should be in listeners
        Mail::to($user)->send(new OrderConfirmation($order));

        return redirect()->route('orders.show', $order);
    }
}
```

### Use Resource Controllers
```php
Route::resource('posts', PostController::class);
Route::apiResource('api/posts', Api\PostController::class);
```

## Action Classes

Single-purpose classes that encapsulate business logic:

```php
namespace App\Actions;

class CreateOrderAction
{
    public function __construct(
        private readonly InventoryService $inventory,
        private readonly PricingService $pricing,
    ) {}

    public function execute(array $data): Order
    {
        return DB::transaction(function () use ($data) {
            $order = Order::create([
                'user_id' => $data['user_id'],
                'total' => $this->pricing->calculate($data['items']),
            ]);

            foreach ($data['items'] as $item) {
                $order->items()->create($item);
                $this->inventory->reserve($item['product_id'], $item['quantity']);
            }

            event(new OrderCreated($order));

            return $order;
        });
    }
}
```

## Form Requests

Always use Form Requests for validation:

```php
namespace App\Http\Requests;

class StorePostRequest extends FormRequest
{
    public function authorize(): bool
    {
        return $this->user()->can('create', Post::class);
    }

    public function rules(): array
    {
        return [
            'title' => ['required', 'string', 'max:255'],
            'body' => ['required', 'string'],
            'published_at' => ['nullable', 'date', 'after:now'],
            'tags' => ['array'],
            'tags.*' => ['exists:tags,id'],
        ];
    }

    public function messages(): array
    {
        return [
            'title.required' => 'Please provide a title for your post.',
        ];
    }
}
```

## Service Container & Dependency Injection

```php
// Bind interface to implementation in AppServiceProvider
$this->app->bind(PaymentGateway::class, StripePaymentGateway::class);

// Or use contextual binding
$this->app->when(PhotoController::class)
    ->needs(Filesystem::class)
    ->give(function () {
        return Storage::disk('photos');
    });
```

## Events & Listeners

Decouple side effects from main business logic:

```php
// Event
class OrderShipped
{
    public function __construct(public readonly Order $order) {}
}

// Listener
class SendShipmentNotification
{
    public function handle(OrderShipped $event): void
    {
        $event->order->user->notify(new OrderShippedNotification($event->order));
    }
}

// Dispatch
event(new OrderShipped($order));
// Or
OrderShipped::dispatch($order);
```

## Configuration

- Use config files, not hardcoded values
- Environment-specific values go in `.env`
- Access via `config('app.name')`, not `env('APP_NAME')`
- Cache config in production: `php artisan config:cache`

## Security Best Practices

1. **Always validate input** - Use Form Requests
2. **Use Eloquent or Query Builder** - Prevents SQL injection
3. **CSRF protection** - Automatic with `@csrf` in forms
4. **Mass assignment protection** - Use `$fillable` or `$guarded`
5. **Authentication** - Use Laravel's built-in auth or Sanctum/Passport
6. **Authorization** - Use Policies and Gates
7. **Rate limiting** - Apply to sensitive routes

```php
// Rate limiting in routes
Route::middleware(['throttle:api'])->group(function () {
    Route::post('/login', [AuthController::class, 'login']);
});
```
