Conditionals are a common part of any application, but there are a few simple conventions you can follow which makes them easier to parse when scanning through a file.

## Avoid `if`/`elseif`/`else` chains [#](https://lsg.spdload.dev/conditionals/#avoid-if-elseif-else-chains)

It is often easier to reason about the execution path of conditional behaviour using [Guard Clauses](https://refactoring.com/catalog/replaceNestedConditionalWithGuardClauses.html), rather than chaining one or more `elseif` or `else` statements.

In general, handling error conditions early in your conditional block works best; it allows you to see upfront any conditions that would cause an error.

```php
// Good
public function update($service)
{
    if (Gate::denies('update-service', $service)) {
        abort(403);
    }

    // Handle success condition...
}

// Bad
public function update($service)
{
    if (Gate::allows('update-service', $service)) {
        // Handle success condition...
    } else {
        abort(403);
    }
}
```

## Avoid nested conditionals [#](https://lsg.spdload.dev/conditionals/#avoid-nested-conditionals)

Whenever possible, avoid deep-nesting and look for opportunities to either extract complex conditional logic to methods or return early.

## Always use curly brackets [#](https://lsg.spdload.dev/conditionals/#avoid-curly-brackets)

```php
// Good
if ($condition) {
   ...
}

// Bad
if ($condition) ...
```

## Ternary operations [#](https://lsg.spdload.dev/conditionals/#ternary-operations)

Unless you are dealing with very short ternary conditions, each expression should be on its own line.

```php
// Good
$result = $object instanceof Model
    ? $object->name
    : 'A default value';

$name = $isFoo ? 'foo' : 'bar';

// Bad
$result = $object instanceof Model ?
    $object->name :
   'A default value';

$result = $object instanceof Model ? $object->name : 'A default value';
```

Avoid nesting ternary operations. They are trickier to reason about and don't necessarily execute in the order you expect.
