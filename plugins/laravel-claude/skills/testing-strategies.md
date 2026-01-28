# Laravel Testing Strategies

## Test Organization

```
tests/
├── Feature/           # Integration tests (HTTP, database)
│   ├── Auth/
│   ├── Api/
│   └── Web/
├── Unit/              # Isolated unit tests
│   ├── Actions/
│   ├── Models/
│   └── Services/
└── TestCase.php       # Base test class
```

## Feature Tests (HTTP Tests)

```php
namespace Tests\Feature;

use App\Models\Post;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class PostControllerTest extends TestCase
{
    use RefreshDatabase;

    public function test_user_can_view_posts_index(): void
    {
        $posts = Post::factory()->count(3)->create();

        $response = $this->get('/posts');

        $response->assertOk();
        $response->assertViewIs('posts.index');
        $response->assertViewHas('posts');
    }

    public function test_authenticated_user_can_create_post(): void
    {
        $user = User::factory()->create();

        $response = $this->actingAs($user)
            ->post('/posts', [
                'title' => 'My Post',
                'body' => 'Post content here',
            ]);

        $response->assertRedirect('/posts');
        $this->assertDatabaseHas('posts', [
            'title' => 'My Post',
            'user_id' => $user->id,
        ]);
    }

    public function test_guest_cannot_create_post(): void
    {
        $response = $this->post('/posts', [
            'title' => 'My Post',
            'body' => 'Post content',
        ]);

        $response->assertRedirect('/login');
    }

    public function test_validation_errors_are_returned(): void
    {
        $user = User::factory()->create();

        $response = $this->actingAs($user)
            ->post('/posts', [
                'title' => '', // Required field
            ]);

        $response->assertSessionHasErrors(['title', 'body']);
    }
}
```

## API Tests

```php
namespace Tests\Feature\Api;

use App\Models\User;
use Laravel\Sanctum\Sanctum;
use Tests\TestCase;

class PostApiTest extends TestCase
{
    use RefreshDatabase;

    public function test_can_list_posts(): void
    {
        Post::factory()->count(5)->create();

        $response = $this->getJson('/api/posts');

        $response->assertOk()
            ->assertJsonCount(5, 'data')
            ->assertJsonStructure([
                'data' => [
                    '*' => ['id', 'title', 'body', 'created_at'],
                ],
            ]);
    }

    public function test_authenticated_user_can_create_post(): void
    {
        $user = User::factory()->create();
        Sanctum::actingAs($user);

        $response = $this->postJson('/api/posts', [
            'title' => 'API Post',
            'body' => 'Created via API',
        ]);

        $response->assertCreated()
            ->assertJson([
                'data' => [
                    'title' => 'API Post',
                ],
            ]);
    }

    public function test_unauthenticated_request_returns_401(): void
    {
        $response = $this->postJson('/api/posts', [
            'title' => 'Test',
        ]);

        $response->assertUnauthorized();
    }
}
```

## Unit Tests

```php
namespace Tests\Unit\Actions;

use App\Actions\CalculateOrderTotalAction;
use App\Models\Product;
use PHPUnit\Framework\TestCase;

class CalculateOrderTotalActionTest extends TestCase
{
    public function test_calculates_total_correctly(): void
    {
        $action = new CalculateOrderTotalAction();

        $items = [
            ['price' => 10.00, 'quantity' => 2],
            ['price' => 25.00, 'quantity' => 1],
        ];

        $total = $action->execute($items);

        $this->assertEquals(45.00, $total);
    }

    public function test_applies_discount(): void
    {
        $action = new CalculateOrderTotalAction();

        $items = [
            ['price' => 100.00, 'quantity' => 1],
        ];

        $total = $action->execute($items, discountPercent: 10);

        $this->assertEquals(90.00, $total);
    }
}
```

## Model Tests

```php
namespace Tests\Unit\Models;

use App\Models\Post;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class PostTest extends TestCase
{
    use RefreshDatabase;

    public function test_post_belongs_to_user(): void
    {
        $post = Post::factory()->create();

        $this->assertInstanceOf(User::class, $post->user);
    }

    public function test_published_scope_filters_correctly(): void
    {
        Post::factory()->create(['published_at' => now()->subDay()]);
        Post::factory()->create(['published_at' => null]);
        Post::factory()->create(['published_at' => now()->addDay()]);

        $published = Post::published()->get();

        $this->assertCount(1, $published);
    }

    public function test_slug_is_generated_on_create(): void
    {
        $post = Post::factory()->create(['title' => 'My Great Post']);

        $this->assertEquals('my-great-post', $post->slug);
    }
}
```

## Testing with Factories

```php
// database/factories/PostFactory.php
namespace Database\Factories;

use App\Models\Post;
use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;

class PostFactory extends Factory
{
    protected $model = Post::class;

    public function definition(): array
    {
        return [
            'user_id' => User::factory(),
            'title' => fake()->sentence(),
            'body' => fake()->paragraphs(3, true),
            'published_at' => fake()->optional()->dateTimeBetween('-1 month', '+1 month'),
        ];
    }

    public function published(): static
    {
        return $this->state(fn (array $attributes) => [
            'published_at' => now()->subDays(rand(1, 30)),
        ]);
    }

    public function draft(): static
    {
        return $this->state(fn (array $attributes) => [
            'published_at' => null,
        ]);
    }
}

// Usage in tests
Post::factory()->published()->create();
Post::factory()->draft()->count(3)->create();
Post::factory()->for($user)->create();
```

## Mocking

```php
use App\Services\PaymentGateway;
use Mockery\MockInterface;

public function test_payment_is_processed(): void
{
    $this->mock(PaymentGateway::class, function (MockInterface $mock) {
        $mock->shouldReceive('charge')
            ->once()
            ->with(1000, 'tok_visa')
            ->andReturn(true);
    });

    // Test code that uses PaymentGateway
}

// Partial mock
$this->partialMock(Service::class, function (MockInterface $mock) {
    $mock->shouldReceive('specificMethod')->andReturn('mocked');
});

// Spy
$spy = $this->spy(PaymentGateway::class);
// ... run code ...
$spy->shouldHaveReceived('charge')->once();
```

## Testing Events, Jobs, and Notifications

```php
use Illuminate\Support\Facades\Event;
use Illuminate\Support\Facades\Queue;
use Illuminate\Support\Facades\Notification;

public function test_event_is_dispatched(): void
{
    Event::fake();

    // Action that dispatches event
    $order = Order::factory()->create();
    $order->ship();

    Event::assertDispatched(OrderShipped::class, function ($event) use ($order) {
        return $event->order->id === $order->id;
    });
}

public function test_job_is_queued(): void
{
    Queue::fake();

    // Action that queues job

    Queue::assertPushed(ProcessOrder::class);
    Queue::assertPushedOn('orders', ProcessOrder::class);
}

public function test_notification_is_sent(): void
{
    Notification::fake();

    $user = User::factory()->create();
    $user->notify(new WelcomeNotification());

    Notification::assertSentTo($user, WelcomeNotification::class);
}
```

## Database Assertions

```php
// Assert record exists
$this->assertDatabaseHas('posts', [
    'title' => 'My Post',
    'user_id' => $user->id,
]);

// Assert record doesn't exist
$this->assertDatabaseMissing('posts', [
    'title' => 'Deleted Post',
]);

// Assert record count
$this->assertDatabaseCount('posts', 5);

// Assert soft deleted
$this->assertSoftDeleted('posts', [
    'id' => $post->id,
]);
```

## Running Tests

```bash
# Run all tests
php artisan test

# Run with coverage
php artisan test --coverage

# Run specific test file
php artisan test tests/Feature/PostControllerTest.php

# Run specific test method
php artisan test --filter test_user_can_create_post

# Run in parallel
php artisan test --parallel

# Stop on first failure
php artisan test --stop-on-failure
```
