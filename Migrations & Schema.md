#develop/tree 

### Table Namings #

Entity tables SHOULD be in plural form, e.g. "posts"
Default migration for pivot tables #
Use following code to create migrations with pivot tables:

```php
Schema::create('post_has_tag', function (Blueprint $table) {
    $table->foreignId('post_id')->constrained()->cascadeOnDelete()->cascadeOnUpdate();
    $table->foreignId('tag_id')->constrained()->cascadeOnDelete()->cascadeOnUpdate();

    $table->primary(['post_id', 'tag_id']);
});
```

### Modifying migrations #

Once a migration file has left your development environment and is in the master branch, it must never be modified as the risk that it has been cloned by another developer or deployed to production means changes could break other environments.

Until that time, feel free to modify the migration as much as required.

### Forward-rolling migrations #

When generating migration files, they will contain both an up method and a down method.

The down method must not be used and should be removed. Reversing migrations will lead to data loss and most times this is not desirable, particularly if you have already captured user input.

In the event that a migration must be modified or removed, create a new migration that performs the necessary changes, allowing you to continue rolling forward only.