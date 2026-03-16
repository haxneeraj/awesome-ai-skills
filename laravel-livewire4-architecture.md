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

---

# Entry Points

Entry points include:

Livewire Components
API Controllers
Jobs
Console Commands

Entry points must NOT contain:

• domain logic
• business rules
• database queries
• external integrations

Entry points may only:

• validate input
• dispatch actions
• manage UI state

Example:

```
app(CreateOrderAction::class)->execute($data);
```

---

# Livewire 4 Component Rules

Livewire components are considered ENTRY POINTS.

They must follow the same restrictions as controllers.

Allowed:

• UI state
• validation
• event dispatching
• calling actions

Forbidden:

• business logic
• database queries
• API calls
• domain calculations

Correct example:

```
public function createOrder()
{
    app(CreateOrderAction::class)->execute($this->form);
}
```

Incorrect example:

```
Order::create($data);
```

Database operations must exist in Services.

---

# Livewire Component Types

The system must detect Livewire components in these formats.

## Multi File Component (MFC)

Structure:

```
resources/views/**/component-name/
    component-name.php
    component-name.blade.php
```

Example:

```
resources/views/admin/course/category/list/
    list.php
    list.blade.php
```

---

## Single File Component (SFC)

Structure:

```
resources/views/components/**/component-name.blade.php
```

Contains PHP class and Blade template in the same file.

---

## Page Components

Page components are rendered via routing.

Example:

```
Route::livewire('/dashboard', 'admin::dashboard');
```

---

# Livewire Component Location Rules

Component locations must NOT be hardcoded.

They must be resolved using:

```
config/livewire.php
```

Important configuration keys:

• component_locations
• component_namespaces
• class_namespace
• class_path
• view_path

Example:

```
'component_locations' => [
    resource_path('views/components'),
    resource_path('views/livewire'),
]
```

The skill must scan these directories when detecting components.

---

# Livewire Namespace Components

Livewire components may use namespaces defined in:

```
config('livewire.component_namespaces')
```

Example namespaces:

layouts::
site::
admin::
auth::

Example usage:

```
<livewire:admin::course.category.list />
```

---

# Livewire Make Command Rules

Component generation must respect configuration:

```
config('livewire.make_command')
```

Example:

```
'type' => 'mfc'
'emoji' => false
```

Rules:

Emoji prefix must always be false.

Forbidden:

```
⚡create-order
```

Allowed:

```
create-order
```

---

# Livewire Routes

Livewire page routes must use:

```
Route::livewire()
```

Example:

```
Route::livewire('/orders/create', 'admin::orders.create');
```

Controllers must not render Livewire page components directly.

---

# Validation Layer

Applications must use reusable validation classes.

Location:

```
app/Validation
```

Example structure:

```
app
 ├ Validation
 │   └ Product
 │       ├ CreateProductValidation.php
 │       └ UpdateProductValidation.php
```

Example:

```
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

• contain validation rules only
• be reusable across Livewire, API, Jobs, Console

Validation classes must NOT contain:

• business logic
• database access

---

# Validation Usage

## API Controller

```
Validator::make(
    $request->all(),
    CreateProductValidation::rules()
)->validate();

app(CreateProductAction::class)->execute($request->all());
```

---

## Livewire

```
$this->validate(CreateProductValidation::rules());

app(CreateProductAction::class)->execute($this->all());
```

---

# DTO Layer

Large applications should use Data Transfer Objects.

Location:

```
app/DTO
```

Example:

```
app
 ├ DTO
 │   └ Product
 │       └ CreateProductDTO.php
```

Example DTO:

```
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

• contain typed properties
• represent validated data

DTOs must NOT:

• contain business logic
• access database

---

# Using DTO in Actions

Example:

```
public function execute(CreateProductDTO $dto)
{
    return Product::create([
        'name'=>$dto->name,
        'price'=>$dto->price,
        'media_id'=>$dto->media_id
    ]);
}
```

---

# Actions (Use Cases)

Actions represent application use cases.

Examples:

CreateOrderAction
RegisterUserAction
TransferFundsAction

Responsibilities:

• orchestrate services
• coordinate workflows

Forbidden:

• domain logic
• external API calls

Correct flow:

Entry → Action → Service

---

# Domain Services

Services contain business logic.

Examples:

PaymentService
OrderService
WalletService

Responsibilities:

• domain rules
• business calculations
• database operations
• interacting with drivers

---

# Driver Architecture

External integrations must use driver pattern.

Examples:

Payment gateways
SMS providers
Email providers

Architecture:

Service → DriverManager → Driver → Interface

---

# Driver Manager

Managers resolve drivers dynamically.

Example:

PaymentManager

Responsibilities:

• resolve configured driver
• manage multiple providers

Example drivers:

Stripe
Razorpay
PayPal

---

# Drivers

Drivers implement external integrations.

Examples:

StripeDriver
RazorpayDriver
TwilioDriver

Drivers must implement interface.

Drivers must NOT contain domain rules.

---

# Driver Interfaces

Interfaces define contract for drivers.

Example:

```
interface PaymentDriverInterface
{
    public function charge(array $data);
}
```

---

# Helpers

Location:

```
app/Helpers/helpers.php
```

Helpers must contain pure functions.

Allowed:

• calculations
• formatting
• utility functions

Example:

```
calculate_commission($amount, $percent);
```

Forbidden:

• database queries
• API calls

---

# Traits

Traits contain reusable behaviour.

Examples:

HasTransactions
LogsActivity

Traits must NOT contain domain logic.

---

# Models

Models represent database entities.

Examples:

User
Order
Transaction

Allowed:

• relationships
• casts
• accessors

Forbidden:

• heavy business logic
• API integrations

---

# Final Architecture Pattern

```
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

```
file:line rule message
```

Example:

```
app/Livewire/Admin/Orders/CreateOrder.php:25 architecture Business logic detected in Livewire component

resources/views/admin/course/category/list/list.php:18 architecture Database query inside Livewire component

app/Actions/CreateOrderAction.php:18 architecture Domain rule should exist in Service

app/Services/PaymentService.php:40 architecture External API must be called through Driver

app/Drivers/Payment/StripeDriver.php:10 architecture Driver must implement interface
```
