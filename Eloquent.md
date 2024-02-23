#develop/tree  

The base model has one purpose out of the box; it sets the $guarded property to an empty array. This means you can avoid those situations where you forgot to update the $fillable property and were hit with a `MassAssignmentException`.


You are of course free to modify this base model to encapsulate any funtionality you require in all of your application's models on a per-application basis.

It is important that you never pass request()->all() into any of your models methods that allow mass assignment, such as create or update. Always use request()->only() (or $request->validated() for form request) to ensure that only desired input is being passed into your models, irrespective of what was in the request.

Explicit safety, right in your controllers - rather than the implicit and somewhat hidden safety of the $fillable property.

### Entity Creation #

Always create a migration and factory with a model

```shell
php artisan make:model Foo -mf
```

### Formatting #

The PSR-2 styles that are used with Founder define the order various components should be defined within a class. In the specific case of Eloquent models, the following convention should be used for ordering.

- use statements (traits)
- Properties, with framework-defined properties taking precedence over application-defined ones
- Magic methods
- boot method
- Named (static) constructors
- Query scopes
- Mutators and accessors (grouping each mutator / accessor pair)
- Model observers SHOULD be defined in model boot() method.

```php
class User extends Model
{
    // some code here
    protected static function boot()
    {
        parent::boot();

        self::observe(UserObserver::class);
    }
   // some code here
}
```

### Single-use Traits #

For large models you SHOULD extract some logic to "Single use traits" with parts of the model

Structure example:

```
app/Models folder

User/UserAttributes.php
User/UserRelations.php
User/UserScopes.php
User.php

```

### Dummy Data #

To provide real conditions for you application you SHOULD seed your database with test data.

### All tables MUST be covered by Seeders

Seeders SHOULD be created using Factories whenever it's possible.
Abstracting relationships #
As outlined in the general docs, wherever possible encapsulate any relationship methods for create and update operations behind specific methods. Encapsulating this logic prevents the implementation details from leaking into your controllers.