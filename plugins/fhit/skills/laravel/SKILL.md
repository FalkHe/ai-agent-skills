---
name: laravel
description: Laravel conventions and commands for implementing and testing work items — structure, Eloquent, validation, jobs, Pest/PHPUnit, Pint. Preloaded into backend-php; not invoked by users.
user-invocable: false
---

# Laravel

Check version + installed first-party packages first: `composer show laravel/* --direct`. Use what is installed (Breeze/Fortify/Sanctum/Horizon) before writing your own. APIs change between majors — look up anything this repo does not already use (`docs-lookup`).

## Where things go
Form Requests for validation, Policies for authorization, Actions/Services for logic longer than a few lines, Resources for API output, Jobs for anything slow. Thin controllers. Enums for fixed sets.

## Data
Migration per change, never edit a shipped migration. Factory for every model. `$fillable` or `$guarded`, never neither. Eager-load in queries that render lists (`with()`); N+1 is a bug.

## Tests
Pest if `tests/Pest.php` exists, else PHPUnit. `RefreshDatabase`. Feature tests hit HTTP (`$this->postJson()`); unit tests only for pure logic. Name: `it('AC1: rejects wrong password')`.
Run: `php artisan test --compact` (single file: `php artisan test tests/Feature/X.php`).

## Lint / format
`vendor/bin/pint --dirty`; static analysis if configured: `vendor/bin/phpstan analyse`.

## Never
Raw SQL where Eloquent/Query Builder works. `env()` outside config files. Logic in Blade. `dd()`/`dump()` committed.
