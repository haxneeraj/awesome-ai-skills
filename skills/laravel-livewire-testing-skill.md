# Laravel Testing Skill

Review Laravel applications and enforce comprehensive automated testing.

Focus areas:

Unit testing
Feature testing
Livewire component testing
Action testing
Service testing

Supported stack:

Laravel
PHPUnit
Livewire 4

---

# Testing Philosophy

Every feature must be testable.

Tests must cover:

Business logic
Application flows
Livewire components
API endpoints

Tests must not depend on UI rendering.

---

# Test Directory Structure

Recommended structure:

```
tests
 ├ Feature
 ├ Unit
 └ Livewire
```

Example:

```
tests
 ├ Feature
 │   └ Order
 │       CreateOrderTest.php
 │
 ├ Unit
 │   └ Services
 │       PaymentServiceTest.php
 │
 └ Livewire
     └ Admin
         OrderTableTest.php
```

---

# Unit Tests

Unit tests must focus on:

Services
Helpers
Drivers
DTO logic

Example location:

```
tests/Unit
```

Example:

```
PaymentServiceTest
CommissionCalculatorTest
StripeDriverTest
```

Example test:

```php
public function it_calculates_commission()
{
    $result = calculate_commission(1000, 10);

    $this->assertEquals(100, $result);
}
```

---

# Action Tests

Actions must be tested independently.

Location:

```
tests/Feature/Actions
```

Example:

```
CreateOrderActionTest
TransferFundsActionTest
```

Example:

```php
public function it_creates_order()
{
    $dto = new CreateOrderDTO(...);

    $order = app(CreateOrderAction::class)->execute($dto);

    $this->assertDatabaseHas('orders', [
        'id' => $order->id
    ]);
}
```

---

# Service Tests

Services must test business logic.

Location:

```
tests/Unit/Services
```

Example:

```
WalletServiceTest
PaymentServiceTest
```

Example:

```php
public function it_blocks_transfer_when_balance_insufficient()
{
    $this->expectException(Exception::class);

    $service = app(WalletService::class);

    $service->transfer($from,$to,1000);
}
```

---

# Feature Tests

Feature tests must test application flows.

Location:

```
tests/Feature
```

Example:

```
UserRegistrationTest
CreateOrderTest
```

Example:

```php
public function user_can_register()
{
    $response = $this->post('/register',[
        'name'=>'Test',
        'email'=>'test@test.com',
        'password'=>'password'
    ]);

    $response->assertStatus(302);

    $this->assertDatabaseHas('users',[
        'email'=>'test@test.com'
    ]);
}
```

---

# API Tests

API endpoints must have tests.

Example:

```php
public function api_returns_users()
{
    $response = $this->getJson('/api/users');

    $response->assertStatus(200);
}
```

---

# Livewire Component Tests

Livewire components must use Livewire testing utilities.

Example location:

```
tests/Livewire
```

Example:

```
UserTableTest
CreateOrderFormTest
```

Example test:

```php
Livewire::test(UserTable::class)
    ->set('search','john')
    ->call('search')
    ->assertSee('John Doe');
```

---

# Livewire Form Tests

Example:

```php
Livewire::test(CreateOrder::class)
    ->set('amount',100)
    ->call('submit')
    ->assertHasNoErrors();
```

---

# Database Testing

Tests must use database refresh.

Example:

```php
use RefreshDatabase;
```

Example:

```php
class CreateOrderTest extends TestCase
{
    use RefreshDatabase;
}
```

This ensures clean database state.

---

# Test Data

Use factories for generating test data.

Example:

```php
User::factory()->create();
```

Avoid hardcoded database inserts.

---

# Mocking External Services

External integrations must be mocked.

Example:

Payment drivers
SMS drivers
Email providers

Example:

```php
$this->mock(PaymentDriverInterface::class);
```

Tests must not call real APIs.

---

# Driver Testing

Drivers must have unit tests.

Example:

```
tests/Unit/Drivers
```

Example:

```
StripeDriverTest
TwilioDriverTest
```

---

# Validation Tests

Validation classes must be tested.

Example:

```php
Validator::make([], CreateProductValidation::rules())
    ->fails();
```

---

# DTO Testing

DTOs must validate type safety.

Example:

```php
$dto = new CreateProductDTO('Phone',100,null);

$this->assertEquals('Phone',$dto->name);
```

---

# Test Coverage

Important layers must have tests:

Actions
Services
Drivers
Livewire components

Avoid untested business logic.

---

# Test Naming Conventions

Tests must use descriptive names.

Examples:

```
it_creates_order
it_prevents_invalid_payment
it_calculates_commission
```

Avoid vague names.

---

# Test Isolation

Tests must not depend on other tests.

Each test must create its own data.

---

# Output Format

Return testing issues using:

```
file:line rule message
```

Example:

```
app/Services/WalletService.php:20 testing Missing unit test for service

app/Actions/CreateOrderAction.php:12 testing Action has no feature test

app/Livewire/Admin/UserTable.php:18 testing Livewire component missing test
```
