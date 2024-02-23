----
#develop/tree  

The names given to artisan commands should all be kebab-cased.

```bash
# Good
php artisan delete-old-records

# Bad
php artisan deleteOldRecords
```

---

A command should always give some feedback on what the result is. Minimally you should let the handle method spit out a comment at the end indicating that all went well.

```php
// in a Command
public function handle()
{
    // do some work

    $this->comment('All ok!');
}
```

If possible use a descriptive success message eg. Old records deleted.