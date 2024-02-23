----
Whilst docblocks can be useful for introducing new developers in your codebase to your functions, it is much too easy for them to fall out of sync with your actual implementation if you're not careful. Avoid them wherever possible by writing expressive code.

**Never** leave commented out code in your application. Your version control system is responsible for handling code that was - or should be - deleted.

Comments that describe the purpose of some method are usually a good indicator that the method is not clearly named.

```php
// Good
public function recentServiceSignups()
{
    // ...
}

// Bad
// Get services that recently signed up
public function recentServices()
{
    // ...
}
```

---

Comments should be avoided as much as possible by writing expressive code. If you do need to use a comment format it like this:

```php
// There should be space before a single line comment.

/*
 * If you need to explain a lot you can use a comment block. Notice the
 * single * on the first line. Comment blocks don't need to be three
 * lines long or three characters shorter than the previous line.
 */
```

---

Likewise, comments that describe _what_ code is doing are often unnecessary.

```php
// array of services
$return = [];
```

Comments that explain _how_ code functions are often suitable for extraction to their own method.

```php
// Good
return $this->recentResidentialServiceSignups($services);

// Bad
// Loop over recent signups and return those of a specific type
foreach ($services as $service) {
    if ($service->type == 'Residential') {
        $return[] = $service;
    }
}

return $return;
```

---

If your method is typehinted, or its intent is clear, avoid needless docblocks.

```php
// Good
class Service
{
    public static function createFromDetails(array $details) : Service
    {
        // ...
    }
}

// Bad: The description doesn't add any extra context
class Service
{
    /**
     * Create a signup from the given details.
     *
     * @param  array  $details
     *
     * @return \App\Entities\Service
     */
    public static function createFromDetails(array $details) : Service
    {
        // ...
    }
}
```

---

When necessary, always use the fully-qualified class name in your docblocks.

```php
// Good

/**
 * @param  array  $details
 *
 * @return \App\Entities\Service
 */

// Bad

/**
 * @param  array  $details
 *
 * @return Service
 */
```

---

Docblocks for class variables are required, as there's currently no other way to to typehint these.

```php
// Good

class Foo
{
    /** @var \Spatie\Url\Url */
    protected $url;

    /** @var string */
    protected $name;
}

// Bad

class Foo
{
    protected $url;
    protected $name;
}
```

---

When possible, docblocks should be written on one line.

```php
// Good

/** @var string */
/** @test */

// Bad

/**
 * @test
 */
```

---

If a variable has multiple types, the most common occurring type should be first.

```php
// Good

/** @var \Spatie\Goo\Bar|null */

// Bad

/** @var null|\Spatie\Goo\Bar */
```