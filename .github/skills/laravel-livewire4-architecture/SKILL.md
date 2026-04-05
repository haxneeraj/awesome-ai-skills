# Laravel Enterprise Architecture Skill

Review Laravel applications and enforce the project architecture.

This system follows a layered enterprise architecture designed for scalable applications.

Supported stack:

Laravel
Livewire 4
Alpine.js
TailwindCSS

---

# Architecture Overview

The application must follow this layered architecture:

```text
Entry Points
↓
Validation
↓
DTO
↓
Actions (Use Cases)
↓
Services (Domain Logic)
↓
Driver Manager
↓
Drivers
↓
Driver Interface
↓
Models / External APIs
```

---

# Entry Points

Entry points include:

* Livewire Components
* API Controllers
* Jobs
* Console Commands

Entry points must NOT contain:

* domain logic
* business rules
* database queries
* external integrations

Entry points may only:

* validate input
* dispatch actions
* manage UI state

Example:

```php id="l1f4b2"
app(CreateOrderAction::class)->execute($data);
```

---

# Livewire 4 Component Rules

Livewire components are considered ENTRY POINTS.

They must follow the same restrictions as controllers.

Allowed:

* UI state
* validation
* event dispatching
* calling actions

Forbidden:

* business logic
* database queries
* API calls
* domain calculations

Correct example:

```php id="w9t2q8"
public function createOrder()
{
    app(CreateOrderAction::class)->execute($this->form);
}
```

Incorrect example:

```php id="u6n8r1"
Order::create($data);
```

Database operations must exist in Services.

---

# Livewire Component Types

The system must detect Livewire components in these formats.

## Multi File Component (MFC)

Structure:

```text
resources/views/**/component-name/
    component-name.php
    component-name.blade.php
```

Example:

```text
resources/views/admin/course/category/list/
    list.php
    list.blade.php
```

---

## Single File Component (SFC)

Structure:

```text
resources/views/components/**/component-name.blade.php
```

Contains PHP class and Blade template in the same file.

---

## Page Components

Page components are rendered via routing.

Example:

```php id="m4x1p9"
Route::livewire('/dashboard', 'admin::dashboard');
```

---

# Livewire Component Location Rules

Component locations must NOT be hardcoded.

They must be resolved using:

```php id="q2k7d5"
config/livewire.php
```

Important configuration keys:

* component_locations
* component_namespaces
* class_namespace
* class_path
* view_path

Example:

```php id="c8v6n3"
'component_locations' => [
    resource_path('views/components'),
    resource_path('views/livewire'),
]
```

The skill must scan these directories when detecting components.

---

# Livewire Namespace Components

Livewire components may use namespaces defined in:

```php id="y3r9m1"
config('livewire.component_namespaces')
```

Example namespaces:

* layouts::
* site::
* admin::
* auth::

Example usage:

```blade id="n7j5k4"
<livewire:admin::course.category.list />
```

---

# Livewire Make Command Rules

Component generation must respect configuration:

```php id="p5z2h7"
config('livewire.make_command')
```

Example:

```php id="s1c9v4"
'type' => 'mfc'
'emoji' => false
```

Rules:

Emoji prefix must always be false.

Forbidden:

```text
⚡create-order
```

Allowed:

```text
create-order
```

---

# Livewire Routes

Livewire page routes must use:

```php id="d6w8x3"
Route::livewire()
```

Example:

```php id="b9f1e6"
Route::livewire('/orders/create', 'admin::orders.create');
```

Controllers must not render Livewire page components directly.

---

# Validation Layer

Applications must use reusable validation classes.

Location:

```text
app/Validation
```

Example structure:

```text
app
 ├ Validation
 │   └ Product
 │       ├ CreateProductValidation.php
 │       └ UpdateProductValidation.php
```

Example:

```php id="g4n2m8"
class CreateProductValidation
{
    public static function rules()
    {
        return [
            'name' => ['required','string','max:255'],
            'price' => ['required','numeric'],
            'media_id' => ['nullable','exists:media,id']
        ];
    }
}
```

Validation classes must:

* contain validation rules only
* be reusable across Livewire, API, Jobs, Console

Validation classes must NOT contain:

* business logic
* database access

---

# Validation Usage

## API Controller

```php id="h2u9q5"
Validator::make(
    $request->all(),
    CreateProductValidation::rules()
)->validate();

app(CreateProductAction::class)->execute($request->all());
```

---

## Livewire

```php id="r8m4c1"
$this->validate(CreateProductValidation::rules());

app(CreateProductAction::class)->execute($this->all());
```

---

# DTO Layer

Large applications should use Data Transfer Objects.

Location:

```text
app/DTO
```

Example:

```text
app
 ├ DTO
 │   └ Product
 │       └ CreateProductDTO.php
```

Example DTO:

```php id="v3k7t9"
class CreateProductDTO
{
    public function __construct(
        public string $name,
        public float $price,
        public ?int $media_id
    ){}
}
```

DTO rules:

DTOs must:

* contain typed properties
* represent validated data

DTOs must NOT:

* contain business logic
* access database

---

# Response DTO Layer

Applications must use a standardized Response DTO for returning results from Services.

Location:

```text
app/DTO/ResponseDTO.php
```

Purpose:

All Services must return a consistent response object instead of returning raw arrays, models, booleans, or mixed values.

This ensures:

* predictable return types
* clean action handling
* standardized success/error responses
* better type safety

---

# Response DTO Structure

The Response DTO must contain:

* `status` → boolean (`true` or `false`)
* `message` → string
* `data` → nullable mixed value
* `statusCode` → integer (default: `200`)

Example:

```php id="k6x2m4"
class ResponseDTO
{
    public function __construct(
        public bool $status,
        public string $message,
        public mixed $data = null,
        public int $statusCode = 200,
    ) {}
}
```

---

# Response DTO Rules

ResponseDTO must:

* be used as the standard return type from Services
* be returned for both success and failure cases
* contain meaningful `message` values
* use `data` for payload when required
* use `statusCode` for response intent

ResponseDTO must NOT:

* contain business logic
* perform transformations
* contain database access

---

# Using DTO in Actions

Example:

```php id="q9d4s7"
public function execute(CreateProductDTO $dto)
{
    return Product::create([
        'name' => $dto->name,
        'price' => $dto->price,
        'media_id' => $dto->media_id
    ]);
}
```

---

# Service Return Rules

All Service methods must return `ResponseDTO`.

Forbidden:

```php id="a5n8v1"
return true;
return false;
return [];
return $user;
```

Allowed:

```php id="j3m7q2"
return new ResponseDTO(
    status: true,
    message: 'User created successfully.',
    data: $user
);
```

Failure example:

```php id="u2x6k9"
return new ResponseDTO(
    status: false,
    message: 'Insufficient wallet balance.',
    data: null,
    statusCode: 422
);
```

---

# Action Return Rules

Actions must return `ResponseDTO`.

Actions must ensure the Service returns `ResponseDTO` using strict type hinting.

Example:

```php id="t8p4r6"
class CreateOrderAction
{
    public function __construct(
        protected OrderService $orderService
    ) {}

    public function execute(CreateOrderDTO $dto): ResponseDTO
    {
        return $this->orderService->create($dto);
    }
}
```

Rule:

Actions must NOT return raw arrays, booleans, or models.

Actions must act as a strict contract layer between Entry Points and Services.

---

# Service Type Hinting Rules

Service methods must explicitly return `ResponseDTO`.

Example:

```php id="w5c1n8"
class OrderService
{
    public function create(CreateOrderDTO $dto): ResponseDTO
    {
        $order = Order::create([
            'user_id' => $dto->user_id,
            'amount' => $dto->amount,
        ]);

        return new ResponseDTO(
            status: true,
            message: 'Order created successfully.',
            data: $order
        );
    }
}
```

This ensures Actions can safely depend on a consistent return type.

---

# Entry Point Handling Rules

Entry points such as Livewire components, API Controllers, Jobs, and Console Commands must consume `ResponseDTO`.

Example in Livewire:

```php id="m7k3q9"
$response = app(CreateOrderAction::class)->execute($dto);

if (! $response->status) {
    $this->dispatch('notify', $response->message);
    return;
}
```

Example in API Controller:

```php id="p4r8v2"
$response = app(CreateOrderAction::class)->execute($dto);

return response()->json([
    'status' => $response->status,
    'message' => $response->message,
    'data' => $response->data,
], $response->statusCode);
```

---

# Recommended Response DTO Location

Recommended structure:

```text
app
 ├ DTO
 │   ├ Product
 │   │   └ CreateProductDTO.php
 │   └ ResponseDTO.php
```

---

# Actions (Use Cases)

Actions represent application use cases.

Examples:

* CreateOrderAction
* RegisterUserAction
* TransferFundsAction

Responsibilities:

* orchestrate services
* coordinate workflows

Forbidden:

* domain logic
* external API calls

Correct flow:

```text
Entry → Action → Service
```

---

# Domain Services

Services contain business logic.

Examples:

* PaymentService
* OrderService
* WalletService

Responsibilities:

* domain rules
* business calculations
* database operations
* interacting with drivers

Services must return `ResponseDTO`.

---

# Driver Architecture

External integrations must use driver pattern.

Examples:

* Payment gateways
* SMS providers
* Email providers

Architecture:

```text
Service → DriverManager → Driver → Interface
```

---

# Driver Manager

Managers resolve drivers dynamically.

Example:

* PaymentManager

Responsibilities:

* resolve configured driver
* manage multiple providers

Example drivers:

* Stripe
* Razorpay
* PayPal

---

# Drivers

Drivers implement external integrations.

Examples:

* StripeDriver
* RazorpayDriver
* TwilioDriver

Drivers must implement interface.

Drivers must NOT contain domain rules.

---

# Driver Interfaces

Interfaces define contract for drivers.

Example:

```php id="x1n7m5"
interface PaymentDriverInterface
{
    public function charge(array $data);
}
```

---

# Helpers

Location:

```text
app/Helpers/helpers.php
```

Helpers must contain pure functions.

Allowed:

* calculations
* formatting
* utility functions

Example:

```php id="d3q8k1"
calculate_commission($amount, $percent);
```

Forbidden:

* database queries
* API calls

---

# Traits

Traits contain reusable behaviour.

Examples:

* HasTransactions
* LogsActivity

Traits must NOT contain domain logic.

---

# Models

Models represent database entities.

Examples:

* User
* Order
* Transaction

Allowed:

* relationships
* casts
* accessors

Forbidden:

* heavy business logic
* API integrations

---

# Final Architecture Pattern

```text
Entry Points
↓
Validation
↓
DTO
↓
Actions
↓
Services
↓
Driver Managers
↓
Drivers
↓
Interfaces
↓
Models / External APIs
```

---

# Output Format

Return violations using:

```text
file:line rule message
```

Example:

```text
app/Livewire/Admin/Orders/CreateOrder.php:25 architecture Business logic detected in Livewire component

resources/views/admin/course/category/list/list.php:18 architecture Database query inside Livewire component

app/Actions/CreateOrderAction.php:18 architecture Domain rule should exist in Service

app/Services/PaymentService.php:40 architecture External API must be called through Driver

app/Drivers/Payment/StripeDriver.php:10 architecture Driver must implement interface

app/Services/OrderService.php:28 architecture Service must return ResponseDTO

app/Actions/CreateOrderAction.php:18 architecture Action must type-hint ResponseDTO return

app/Services/WalletService.php:42 architecture Raw boolean return detected use ResponseDTO

app/Http/Controllers/Api/OrderController.php:30 architecture ResponseDTO should be consumed instead of raw service return
```
