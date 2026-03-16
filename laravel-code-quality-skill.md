# Laravel Code Quality Skill

Review Laravel applications and enforce high code quality standards.

Focus areas:

Naming conventions
Class size limits
Service responsibilities
Code duplication
Dependency injection quality
Method complexity

Supported stack:

Laravel
Livewire 4
Alpine.js
TailwindCSS

---

# Naming Conventions

Classes must follow descriptive and consistent naming.

Examples:

Actions:

CreateOrderAction
RegisterUserAction

Services:

OrderService
PaymentService

Drivers:

StripeDriver
RazorpayDriver

Interfaces:

PaymentDriverInterface

DTOs:

CreateOrderDTO

Validation:

CreateOrderValidation

Forbidden naming:

DataAction
HelperService
ManagerService

Names must clearly describe the responsibility.

---

# Method Naming

Methods must describe behavior clearly.

Correct:

```php
createOrder()
calculateCommission()
sendPaymentRequest()
```

Forbidden:

```php
process()
handle()
run()
```

unless the method is inside a job or queue worker.

---

# Class Size Limits

Classes must remain small and focused.

Recommended limits:

Controllers / Livewire components:

≤ 200 lines

Actions:

≤ 150 lines

Services:

≤ 300 lines

Large classes indicate poor separation of concerns.

---

# God Service Detection

Services must not contain too many responsibilities.

Violation example:

```php
UserService
```

doing:

* authentication
* payments
* notifications
* reporting

Instead split into:

AuthService
PaymentService
NotificationService

---

# Method Length

Methods should remain short.

Recommended:

≤ 40 lines

Large methods indicate hidden responsibilities.

Split large methods into smaller methods.

---

# Duplicate Logic Detection

Repeated logic must be extracted.

Incorrect:

```php
$total = $amount * $percent / 100;
```

repeated across multiple classes.

Correct:

Move to helper.

Example:

```php
calculate_commission($amount, $percent)
```

Location:

app/Helpers/helpers.php

---

# Reusable Logic Placement

Use correct location depending on logic type.

Pure calculations:

helpers.php

Shared behavior:

Traits

Domain logic:

Services

Application orchestration:

Actions

---

# Dependency Injection Rules

Dependencies must be injected via constructor.

Correct:

```php
public function __construct(
    protected PaymentService $paymentService
) {}
```

Forbidden:

```php
$service = new PaymentService();
```

or

```php
app(PaymentService::class)
```

inside services.

---

# Circular Dependency Prevention

Services must not depend on each other in circular patterns.

Incorrect:

OrderService → PaymentService
PaymentService → OrderService

Break cycles using actions or domain events.

---

# Trait Usage

Traits must contain reusable behavior.

Examples:

HasTransactions
LogsActivity

Traits must NOT contain:

* business rules
* database queries

Traits should be small and focused.

---

# Livewire Component Size

Livewire components must remain lightweight.

Responsibilities:

UI state
validation
calling actions

Forbidden:

* domain logic
* complex calculations

Example correct usage:

```php
public function save()
{
    app(CreateOrderAction::class)->execute($this->form);
}
```

---

# Blade Template Quality

Blade templates must remain simple.

Forbidden:

```php
@php
$total = $order->items->sum(...)
@endphp
```

Logic must be moved to:

View Models
Services
Livewire components.

---

# Long Parameter Lists

Methods must avoid too many parameters.

Violation:

```php
createOrder($user,$product,$price,$currency,$discount,$tax,$shipping)
```

Use DTO instead.

Correct:

```php
createOrder(CreateOrderDTO $dto)
```

---

# Large Switch Statements

Large switch blocks should be replaced with polymorphism.

Incorrect:

```php
switch($gateway) {
 case 'stripe':
 case 'razorpay':
 case 'paypal':
}
```

Correct:

Driver architecture.

Example:

PaymentManager → StripeDriver.

---

# Magic Numbers

Avoid hardcoded values.

Incorrect:

```php
if ($amount > 10000)
```

Correct:

```php
if ($amount > config('payment.limit'))
```

---

# Configuration Usage

Values should come from configuration files.

Correct:

```php
config('payment.driver')
```

Forbidden:

```php
$driver = 'stripe';
```

---

# Action Complexity

Actions must orchestrate services only.

Forbidden inside actions:

* heavy business logic
* database queries

Correct pattern:

Entry → Action → Service.

---

# Service Responsibilities

Services must contain domain logic only.

Services must not:

* render views
* handle HTTP requests
* manage UI state.

---

# Output Format

Return violations using:

```
file:line rule message
```

Example:

```
app/Services/UserService.php:120 quality God service detected

app/Livewire/Admin/Orders/CreateOrder.php:35 quality Business logic inside component

app/Services/PaymentService.php:42 quality Dependency instantiated manually

app/Actions/CreateOrderAction.php:30 quality Method too long
```
