#develop/tree  

Public-facing urls must use kebab-case.

https://spatie.be/open-source
https://spatie.be/jobs/front-end-developer
Register you routes with tuple notation #

```php
// good
use App\Http\Controllers\MyController;

Route::get('my-route', [MyController::class, 'index']);

//bad
Route::get('my-route', 'MyController@index');

```

### Keep your endpoints RESTful #
Always try and keep your endpoints RESTful wherever possible, using the seven operations as described in the RESTful controllers documentation.

```php
// http://example.com/articles
Route::get('articles', [ArticlesController::class, 'index'])->name('articles.index');
<a href="{{ route('articles.index') }}">
    Articles
</a>

```

### Route names must use camelCase. #

```php
Route::get('open-source', [OpenSourceController::class, 'index'])->name('openSource.index');
<a href="{{ route('openSource.index') }}">
    Open Source
</a>

```

### Route parameters #
When specifying parameters, use the singular form of the resource name.

```php
// Good
Route::get('articles/{article}', [ArticlesController::class, 'show'])->name('articles.show');

// Bad
Route::get('articles/{articles}', [ArticlesController::class, 'show'])->name('articles.show');
```

When declaring nested route names, use the singular form of the parent resource and plural for the child.

```php
// Good
Route::post('articles/{article}/comments', [ArticleCommentsController::class, 'store'])
     ->name('article.comments.store');

// Bad
Route::post('articles/{article}/comments', [ArticleCommentsController::class, 'store'])
     ->name('articles.comments.store');
```

Route parameters should use camelCase.

```php
Route::get('news/{newsItem}', [NewsItemsController::class, 'index']);
```

### Route declaration #

When declaring routes in your application, be sure to always define the HTTP verb first. This makes it much easier to visually scan the routes file.

```php
// Good
Route::get('articles', [ArticlesController::class, 'index'])->name('articles.index');
Route::post('articles/{article}/comments', [ArticleCommentsController::class, 'store'])
     ->name('article.comments.store');

// Bad
Route::name('articles.index')->get('articles', [ArticlesController::class, 'index']);
Route::name('article.comments.store')
     ->post('articles/{article}/comments', [ArticleCommentsController::class, 'store']);
```
