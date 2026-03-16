# Laravel Performance Optimization Skill

Review Laravel applications and detect performance issues.

This skill focuses on:

Database performance
Livewire rendering performance
Memory optimization
Large dataset handling

Supported stack:

Laravel
Livewire 4
Alpine.js
TailwindCSS

---

# Database Query Rules

Database queries must follow optimized patterns.

---

## N+1 Query Detection

Avoid queries inside loops.

Incorrect:

```
foreach ($users as $user) {
    echo $user->orders;
}
```

Correct:

```
User::with('orders')->get();
```

Rule:

Always eager load relationships.

---

## Missing Eager Loading

Incorrect:

```
$orders = Order::all();
foreach ($orders as $order) {
    echo $order->user->name;
}
```

Correct:

```
Order::with('user')->get();
```

---

# Pagination Rules

Large datasets must not use:

```
Model::all()
Model::get()
```

Instead use:

```
Model::paginate()
Model::cursorPaginate()
Model::chunk()
Model::lazy()
```

Example:

```
User::paginate(20);
```

---

# Chunk Processing

Large background jobs must use chunking.

Correct:

```
User::chunk(100, function ($users) {
    foreach ($users as $user) {
        // process
    }
});
```

Forbidden:

```
User::all()
```

in large processing loops.

---

# Query Select Optimization

Avoid selecting all columns.

Incorrect:

```
User::all();
```

Correct:

```
User::select('id','name')->get();
```

---

# Index Usage

Columns used in filters must have database indexes.

Example:

```
where('email')
where('user_id')
where('status')
```

These fields should be indexed.

---

# Livewire Performance Rules

Livewire components must avoid heavy operations inside render().

Forbidden:

```
public function render()
{
    return view('...', [
        'users' => User::all()
    ]);
}
```

Correct:

Load data in lifecycle methods.

```
public function mount()
{
    $this->users = User::paginate(20);
}
```

---

# Nested Component Depth

Avoid deeply nested Livewire components.

Maximum recommended depth:

3 levels

Example:

Page
→ Table Component
→ Row Component

Beyond this may degrade performance.

---

# wire:key Usage

Loops rendering components must use wire:key.

Incorrect:

```
@foreach($users as $user)
    <livewire:user-row :user="$user"/>
@endforeach
```

Correct:

```
@foreach($users as $user)
    <livewire:user-row :user="$user" :wire:key="$user->id"/>
@endforeach
```

---

# Debounce Inputs

Inputs must avoid excessive requests.

Incorrect:

```
wire:model="search"
```

Correct:

```
wire:model.debounce.500ms="search"
```

---

# Lazy Loading Components

Heavy components should use lazy loading.

Example:

```
<livewire:report-table lazy />
```

---

# Blade Performance

Avoid heavy logic in Blade.

Incorrect:

```
@foreach(User::all() as $user)
```

Correct:

Data must be passed from component or controller.

---

# Cache Rules

Frequently used queries should use caching.

Example:

```
Cache::remember('settings', 3600, function () {
    return Setting::all();
});
```

---

# API Performance

API endpoints must avoid returning large datasets.

Use:

```
pagination
filters
limit
```

Example:

```
User::paginate(20);
```

---

# Job Queue Rules

Heavy operations must run in background jobs.

Examples:

Email sending
Report generation
Bulk processing

---

# Memory Usage Rules

Avoid loading large collections into memory.

Incorrect:

```
User::all()
```

Correct:

```
User::cursor()
```

or

```
User::lazy()
```

---

# Output Format

Return performance issues using:

```
file:line rule message
```

Example:

```
app/Livewire/Admin/UserTable.php:32 performance N+1 query detected

resources/views/users/index.blade.php:14 performance Database query inside Blade

app/Jobs/ProcessUsers.php:18 performance Large dataset should use chunk()
```
