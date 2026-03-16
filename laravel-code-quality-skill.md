# Laravel Code Quality Skill

Review Laravel applications and enforce high code quality standards.

Focus areas:

Naming conventions
Class size
Service design
Duplicate logic
Dependency Injection
Maintainability

Supported stack:

Laravel
Livewire 4
Alpine.js
TailwindCSS

---

# Naming Conventions

Classes, methods, and variables must use meaningful names.

Examples:

Good:

```
CreateOrderAction
ProcessPaymentService
SendWelcomeEmailJob
```

Bad:

```
DoStuff
Helper
Manager2
TestClass
```

Variables must describe their purpose.

Good:

```
$orderAmount
$userBalance
$paymentProvider
```

Bad:

```
$a
$data1
$temp
```

---

# Class Responsibility Rule

Each class must follow **Single Responsibility Principle (SRP)**.

A class should have one responsibility.

Example:

Correct:

```
CreateOrderAction
PaymentService
EmailNotificationService
```

Incorrect:

```
OrderManager
SystemHelper
ApplicationService
```

Classes must not combine multiple unrelated responsibilities.

---

# Large Class Detection

Classes must not exceed recommended size.

Recommended limits:

Maximum methods: 20
Maximum lines: 400

Large classes are difficult to maintain and test.

Violation example:

```
app/Services/OrderService.php:1200 lines
```

Suggested fix:

Split into smaller services.

---

# God Service Detection

Services must not handle multiple domains.

Example violation:

```
UserService
   createUser()
   processPayment()
   sendEmail()
   generateReport()
```

Correct separation:

```
UserService
PaymentService
EmailService
ReportService
```

Each service must focus on a single domain.

---

# Method Length Rules

Methods should remain small and readable.

Recommended:

Maximum 40 lines.

Violation example:

```
public function processOrder()
{
   // 150 lines of logic
}
```

Suggested fix:

Extract smaller methods.

Example:

```
validateOrder()
calculateTotals()
processPayment()
saveOrder()
```

---

# Duplicate Logic Detection

Repeated logic must be extracted into reusable functions.

Example violation:

```
$commission = ($amount * $percent) / 100;
```

Repeated in multiple places.

Correct solution:

Use helper:

```
calculate_commission($amount,$percent)
```

Location:

```
app/Helpers/helpers.php
```

---

# Dependency Injection Rules

Classes must use constructor injection.

Correct:

```
public function __construct(
    protected PaymentService $paymentService
) {}
```

Forbidden:

```
$service = new PaymentService();
```

Direct instantiation reduces testability.

---

# Service Container Usage

Use Laravel container when resolving classes.

Example:

```
app(CreateOrderAction::class)->execute($data);
```

Avoid manual object creation.

---

# Interface Usage

External integrations must use interfaces.

Correct:

```
PaymentDriverInterface
SmsDriverInterface
EmailDriverInterface
```

Drivers must implement interfaces.

Violation example:

Directly using provider SDK inside service.

---

# Trait Usage

Traits should only contain reusable behavior.

Examples:

```
HasTransactions
LogsActivity
HandlesUploads
```

Traits must not contain domain logic.

---

# Helper Usage

Common calculations must exist in helpers.

Location:

```
app/Helpers/helpers.php
```

Allowed:

calculations
formatting
utility functions

Forbidden:

database queries
API calls

---

# Method Naming

Methods must clearly describe their behavior.

Good:

```
createOrder()
processPayment()
calculateCommission()
```

Bad:

```
handle()
run()
executeProcess()
```

---

# Return Type Rules

Methods should use return types when possible.

Example:

```
public function calculateTotal(): float
```

Improves readability and static analysis.

---

# File Organization

Project structure must remain consistent.

Recommended:

```
app
 ├ Livewire
 ├ Actions
 ├ Services
 ├ Drivers
 ├ DTO
 ├ Validation
 ├ Helpers
 ├ Traits
 └ Models
```

Files must not be placed in incorrect directories.

---

# Cyclomatic Complexity

Methods with too many conditions reduce readability.

Violation example:

```
if (...) {
}
elseif (...) {
}
elseif (...) {
}
elseif (...) {
}
elseif (...) {
}
```

Suggested fix:

Extract smaller methods or use strategy pattern.

---

# Dead Code Detection

Remove unused:

methods
imports
variables

Example violation:

```
use App\Services\PaymentService;
```

but never used.

---

# Comments vs Clean Code

Avoid unnecessary comments explaining obvious code.

Bad:

```
 // add two numbers
 $total = $a + $b;
```

Good code should be self-explanatory.

---

# Testability Rules

Code must be testable.

Avoid static calls when possible.

Prefer dependency injection.

---

# Output Format

Return issues using:

```
file:line rule message
```

Example:

```
app/Services/UserService.php:120 quality Large class detected

app/Services/OrderService.php:55 quality God service detected

app/Actions/CreateOrderAction.php:18 quality Direct dependency instantiation detected

app/Livewire/Admin/Orders/ListOrders.php:45 quality Poor variable naming
```
