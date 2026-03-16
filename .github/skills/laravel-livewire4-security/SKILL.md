# Laravel Security Skill

Review Laravel applications and detect security vulnerabilities.

Focus areas:

Input validation
SQL injection
XSS prevention
Authentication security
File upload safety
API security

Supported stack:

Laravel
Livewire 4
Alpine.js
TailwindCSS

---

# Input Validation Rules

All user input must be validated.

Entry points requiring validation:

Livewire Components
API Controllers
Controllers
Jobs
Console Commands

Validation must use:

Validation Classes

Example location:

```
app/Validation
```

Example:

```
CreateUserValidation
UpdateProductValidation
UploadMediaValidation
```

Forbidden:

Missing validation before processing user input.

Example violation:

```
public function store(Request $request)
{
    User::create($request->all());
}
```

---

# Mass Assignment Protection

Models must define fillable or guarded properties.

Required:

```
protected $fillable = [
    'name',
    'email'
];
```

Forbidden:

```
protected $guarded = [];
```

or

```
User::create($request->all());
```

without fillable protection.

---

# SQL Injection Prevention

Never use raw queries with user input.

Forbidden:

```
DB::select("SELECT * FROM users WHERE email = '$email'");
```

Correct:

```
User::where('email', $email)->first();
```

or parameter binding:

```
DB::select("SELECT * FROM users WHERE email = ?", [$email]);
```

---

# XSS Protection

Blade templates must use escaped output.

Correct:

```
{{ $user->name }}
```

Forbidden:

```
{!! $user->name !!}
```

unless content is sanitized.

User-generated HTML must be sanitized before rendering.

---

# File Upload Security

File uploads must validate:

type
size
mime

Example:

```
'image' => ['required','image','max:2048']
```

Forbidden:

```
$request->file('file')->store();
```

without validation.

Uploaded files must NOT be stored with original user filename.

Use:

```
Storage::putFile()
```

or

```
$file->store()
```

---

# Authentication Rules

Sensitive routes must require authentication.

Use middleware:

```
auth
```

Example:

```
Route::middleware('auth')->group(function () {

});
```

Forbidden:

Sensitive endpoints accessible without authentication.

---

# Authorization Rules

Access to resources must use authorization checks.

Use:

Policies
Gates

Example:

```
$this->authorize('update', $post);
```

Forbidden:

Direct access to resources without permission checks.

---

# CSRF Protection

Forms must include CSRF tokens.

Example:

```
@csrf
```

Livewire automatically handles CSRF but must not disable middleware.

Forbidden:

Disabling CSRF protection.

---

# API Authentication

APIs must use secure authentication.

Recommended:

Laravel Sanctum
Laravel Passport

Tokens must not be exposed in frontend code.

Forbidden:

Hardcoding tokens.

---

# Rate Limiting

Sensitive endpoints must use throttling.

Example:

```
throttle:60,1
```

Example usage:

```
Route::middleware('throttle:60,1');
```

Protects against brute force attacks.

---

# Password Security

Passwords must always be hashed.

Correct:

```
Hash::make($password);
```

Forbidden:

```
User::create([
'password' => $password
]);
```

Passwords must never be stored in plain text.

---

# Environment Security

Sensitive configuration must use environment variables.

Correct:

```
env('STRIPE_SECRET')
```

Forbidden:

```
$secret = "sk_test_123";
```

Secrets must not be committed to repository.

---

# Livewire Security Rules

Livewire components must treat all public properties as user input.

Example:

```
public $amount;
```

Must validate before use.

Forbidden:

Using unvalidated public properties in database queries.

Example violation:

```
Order::where('amount', $this->amount)->get();
```

---

# Sensitive Data Exposure

Applications must not expose:

passwords
tokens
private keys

in:

API responses
logs
Blade views

---

# Logging Security

Logs must not store sensitive data.

Forbidden:

```
Log::info($request->all());
```

Recommended:

Log specific fields only.

---

# Session Security

Session cookies must use:

secure
http_only

Configured in:

```
config/session.php
```

---

# Output Format

Return issues using:

```
file:line rule message
```

Example:

```
app/Http/Controllers/UserController.php:24 security Missing validation before processing request

resources/views/users/profile.blade.php:12 security Unescaped output detected

app/Models/User.php:10 security Model missing fillable protection

app/Livewire/Admin/TransferFunds.php:30 security Unvalidated public property used in query
```
