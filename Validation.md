#develop/tree  

When referencing validation rules, you must always use the array syntax, rather than pipe-delimited strings.

This ensures consistent formatting whether you have one or many validation rules, if you use the Rule class, or if your rules are complex enough such that you need to create custom validation rules.

```php
// Good
request()->validate([
    'name' => ['required', 'max:32'],
    'email' => ['required', Rule::exists('users')],
]);

// Bad
request()->validate([
    'name' => 'required|max:32',
    'email' => ['required', Rule::exists('users')],
]);
```
