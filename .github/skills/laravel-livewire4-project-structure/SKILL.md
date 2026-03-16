# Laravel Project Structure Skill

Review Laravel applications and enforce a scalable project structure.

Focus areas:

Module architecture
Folder organization
Naming conventions
Domain boundaries
Layer separation

Supported stack:

Laravel
Livewire 4
Alpine.js
TailwindCSS

---

# Architecture Philosophy

The application must follow modular architecture.

Each domain must be organized independently.

Example domains:

User
Order
Payment
Wallet
Report

Each domain contains its own:

Actions
Services
Validation
DTO
Tests

---

# Domain Folder Structure

Recommended structure:

```id="d1ukc8"
app
 ├ Actions
 ├ Services
 ├ DTO
 ├ Validation
 ├ Drivers
 ├ Contracts
 ├ Helpers
 ├ Traits
 ├ Models
```

Domains should be organized inside these layers.

Example:

```id="ufsoyw"
app
 ├ Actions
 │   └ Order
 │       CreateOrderAction.php
 │
 ├ Services
 │   └ Order
 │       OrderService.php
 │
 ├ DTO
 │   └ Order
 │       CreateOrderDTO.php
 │
 ├ Validation
 │   └ Order
 │       CreateOrderValidation.php
```

---

# Livewire UI Structure

Livewire UI should mirror domain structure.

Example:

```id="eqou37"
resources/views
 ├ admin
 │   └ orders
 │       └ create
 │           create.php
 │           create.blade.php
```

UI structure should follow business domains.

---

# Module Boundaries

Modules must not access each other's internal logic directly.

Incorrect:

OrderService directly accessing PaymentService internal methods.

Correct:

Use Action or Service interfaces.

Example flow:

```id="c0q95q"
Order Module
   ↓
PaymentService
   ↓
Payment Driver
```

---

# Layer Separation

Every feature must respect architecture layers.

Correct flow:

```id="r0m3z1"
Entry (Livewire / Controller)
      ↓
Validation
      ↓
DTO
      ↓
Action
      ↓
Service
      ↓
Driver
      ↓
Model
```

Forbidden:

Controller directly calling Model.

Example violation:

```id="2gvtg0"
Order::create($data);
```

---

# Folder Naming Rules

Folders must use **PascalCase or DomainName**.

Examples:

```id="6fdyc9"
Order
User
Payment
Wallet
Report
```

Avoid generic folder names.

Forbidden:

```id="9qte0k"
Utils
Misc
General
Stuff
```

---

# File Naming Conventions

Use consistent naming patterns.

Actions:

```id="j3t4co"
CreateOrderAction
UpdateOrderAction
CancelOrderAction
```

Services:

```id="ftweof"
OrderService
PaymentService
WalletService
```

DTO:

```id="h7ccbo"
CreateOrderDTO
UpdateUserDTO
```

Validation:

```id="3j1n06"
CreateOrderValidation
UpdateUserValidation
```

Drivers:

```id="9wzjmc"
StripeDriver
RazorpayDriver
TwilioDriver
```

Interfaces:

```id="y0e6w9"
PaymentDriverInterface
SmsDriverInterface
```

---

# Avoid Deep Nesting

Folders should not exceed 3–4 levels.

Incorrect:

```id="7ju73d"
app/Services/User/Management/Advanced/Operations
```

Correct:

```id="rptxjp"
app/Services/User
```

---

# Shared Utilities

Shared logic must be placed in dedicated locations.

Helpers:

```id="ktuyc0"
app/Helpers/helpers.php
```

Traits:

```id="jn6b2r"
app/Traits
```

Contracts:

```id="p3d64k"
app/Contracts
```

---

# Configuration Organization

Integration settings must live inside config files.

Example:

```id="pvgf9v"
config/payment.php
config/sms.php
config/services.php
```

Forbidden:

Hardcoding provider settings in services.

---

# Driver Directory

Driver based integrations must be structured clearly.

Example:

```id="d5pd3v"
app
 ├ Drivers
 │   └ Payment
 │       PaymentManager.php
 │       StripeDriver.php
 │       RazorpayDriver.php
```

Contracts:

```id="iy51jt"
app/Contracts/PaymentDriverInterface.php
```

---

# Test Structure

Tests must mirror application structure.

Example:

```id="w3cjsn"
tests
 ├ Feature
 │   └ Order
 │       CreateOrderTest.php
 │
 ├ Unit
 │   └ Services
 │       OrderServiceTest.php
 │
 └ Livewire
     └ Admin
         OrderTableTest.php
```

---

# Config Driven Architecture

Behavior must be configurable.

Example:

```id="36o3ra"
config('payment.driver')
```

Avoid hardcoded values.

---

# Migration Organization

Migration files must follow descriptive naming.

Example:

```id="i28f2c"
create_orders_table
create_wallet_transactions_table
```

Avoid generic migration names.

---

# Resource Organization

Views must follow domain hierarchy.

Example:

```id="1ej1q8"
resources/views/admin/orders
resources/views/admin/users
resources/views/site/dashboard
```

Avoid mixing unrelated UI.

---

# Route Organization

Routes must be grouped by domain.

Example:

```id="vfn4te"
routes
 ├ web.php
 ├ api.php
 └ admin.php
```

Example grouping:

```id="3c6h7g"
Route::prefix('orders')->group(function(){
});
```

---

# Output Format

Return structure violations using:

```id="jsd8ze"
file:line rule message
```

Example:

```id="3y7u4h"
app/Services/UserManager.php:10 structure Generic service naming detected

app/Http/Controllers/OrderController.php:25 structure Controller calling model directly

resources/views/dashboard.blade.php:12 structure UI not organized by domain
```

