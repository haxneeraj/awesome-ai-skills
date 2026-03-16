# Laravel Livewire UI Design Skill

Review frontend code and enforce modern UI design standards.

Supported stack:

Laravel
Livewire 4
Alpine.js
TailwindCSS

This skill enforces:

Reusable components
Clean UI architecture
Frontend performance optimization
Consistent design system

---

# Design Principles

The UI must follow these principles:

Consistency
Reusability
Minimal DOM complexity
Performance optimization
Accessibility

Avoid ad-hoc UI implementations.

---

# Component Based UI

UI elements must always be implemented as reusable components.

Reusable component location:

```
resources/views/components
```

Examples:

```
button
card
modal
badge
table
dropdown
input
form-group
```

Usage example:

```blade
<x-button>
Save
</x-button>
```

Forbidden:

Repeated HTML across multiple views.

---

# Section Components

Large UI sections must also be reusable.

Examples:

Dashboard widgets
Tables
Filters
Pagination sections

Example structure:

```
components
 ├ ui
 │   ├ table.blade.php
 │   ├ modal.blade.php
 │   └ dropdown.blade.php
```

---

# SVG Icon System

Icons must use SVG only.

Forbidden:

```
<i class="fa fa-user"></i>
```

Correct:

SVG icon components.

Example location:

```
resources/views/components/icons
```

Example:

```
<x-icons.user />
<x-icons.edit />
<x-icons.delete />
```

Example SVG component:

```blade
<svg
    xmlns="http://www.w3.org/2000/svg"
    class="w-5 h-5"
    fill="none"
    stroke="currentColor"
>
</svg>
```

Benefits:

smaller bundle
no external icon libraries
full styling control

---

# Tailwind Design Rules

Tailwind utilities must be used.

Avoid custom CSS whenever Tailwind utilities exist.

Correct:

```
class="flex items-center gap-3 text-sm font-medium"
```

Forbidden:

```
.custom-card { padding:10px }
```

Prefer:

```
class="p-3"
```

---

# Design System Consistency

Spacing must follow a consistent scale.

Examples:

```
p-2
p-4
p-6
p-8
```

Avoid random values.

Example violation:

```
style="padding:13px"
```

---

# Typography Rules

Typography must use consistent classes.

Example:

```
text-sm
text-base
text-lg
text-xl
```

Avoid inline styling.

---

# Layout System

Layout must use Tailwind grid or flex utilities.

Example:

```
grid grid-cols-12 gap-4
```

or

```
flex items-center justify-between
```

Avoid deeply nested layouts.

---

# Livewire Data Components

Whenever UI requires backend data interaction, use Livewire.

Example cases:

Tables
Forms
Filters
Pagination
Dynamic lists

Preferred component type:

Livewire Single File Component.

Example:

```
resources/views/components/users/table.blade.php
```

---

# Livewire Architecture Enforcement

Livewire components must follow architecture:

```
Livewire Component
      ↓
Action Class
      ↓
Service Class
      ↓
Model
```

Example:

```
UserTable component
     ↓
FetchUsersAction
     ↓
UserService
     ↓
User model
```

Livewire components must not contain:

business logic
database queries

---

# Livewire + Alpine Interaction

Alpine.js should be used for UI behavior only.

Allowed:

dropdowns
modals
tabs
toggles

Example:

```
x-data="{ open:false }"
x-show="open"
```

Forbidden:

business logic in Alpine.

---

# DOM Optimization

Avoid unnecessary DOM nesting.

Incorrect:

```
<div>
 <div>
  <div>
   <div>
```

Prefer flat DOM structures.

---

# Blade Rendering Optimization

Avoid heavy logic in Blade.

Incorrect:

```
@foreach(User::all() as $user)
```

Correct:

Data passed from Livewire or controller.

---

# Table UI Best Practice

Large tables must use:

pagination
search
lazy loading

Example:

```
wire:model.debounce.500ms="search"
```

---

# Livewire Request Optimization

Avoid excessive network requests.

Incorrect:

```
wire:model="search"
```

Correct:

```
wire:model.debounce.500ms="search"
```

---

# Lazy Components

Heavy components must use lazy loading.

Example:

```
<livewire:reports.chart lazy />
```

---

# Accessibility Rules

Interactive elements must use correct HTML tags.

Correct:

```
<button>
```

Forbidden:

```
<div onclick="">
```

---

# Loading States

Livewire components must show loading states.

Example:

```
wire:loading
```

Example UI:

```
Loading...
```

---

# Responsive Design

UI must be responsive.

Use Tailwind breakpoints:

```
sm:
md:
lg:
xl:
```

Example:

```
grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4
```

---

# Frontend Performance

Avoid large JS bundles.

Prefer:

Alpine.js instead of large frameworks.

Avoid unnecessary dependencies.

---

# Asset Optimization

Assets must use build tools.

Recommended:

Laravel Vite.

Example:

```
@vite(['resources/css/app.css','resources/js/app.js'])
```

---

# CSS Optimization

Avoid unused CSS.

Use Tailwind purge configuration.

---

# Component Reusability Rule

If UI code repeats more than twice, it must be extracted into a component.

Example:

```
x-button
x-card
x-table
```

---

# Folder Structure Recommendation

```
resources/views
 ├ components
 │   ├ ui
 │   ├ icons
 │   ├ forms
 │   └ tables
 │
 ├ admin
 ├ site
 └ layouts
```

---

# Output Format

Return violations using:

```
file:line rule message
```

Example:

```
resources/views/users/index.blade.php:22 design Repeated UI should be component

resources/views/admin/dashboard.blade.php:10 design FontAwesome icons detected use SVG components

resources/views/components/users/table.blade.php:34 design Database query inside Blade
```
