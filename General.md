___
#develop/tree  

## Write a Clean Code [#](https://lsg.spdload.dev/general/#write-a-clean-code)

First and more important section! We are all fans of clean an maintainable code. For developing in the best quality we are following next practices:

⚠️ Take attention to these approaches it's really important and required to work in our team.

### Clean Business Logic

At first you should familiarize with - [Methods Are Affordances Not Abilities](https://adamwathan.me/2017/01/24/methods-are-affordances-not-abilities/) conception.

And more practical examples in talk:

⭐️ 🎥 **[Resisting Complexity](https://www.youtube.com/watch?v=dfgtKb-VpRk)**

Additional recommended practices:

-   [Polish Your Code](https://marcelpociot.de/blog/polish-your-code)
-   [Clean Code PHP](https://github.com/jupeter/clean-code-php)

### Clean routes and controllers

We are organize our routes in resources conception, check out excellent talk about this conception:

⭐️ 🎥 **[Cruddy Design](https://youtu.be/MF0jFKvS4SI)**

## Whitespace [#](https://lsg.spdload.dev/general/#whitespace)

PHP will be interpreted whether you write it with no whitespace or if you write it with double spacing. For readability, it is better to cater to your fellow developers rather than for the PHP interpreter. Don't be afraid to use whitespace.

```php
// Good
public function normalisePhoneNumber($input)
{
    $input = preg_replace('/[^0-9]+/', '', $input);

    if (trim($input) === '') {
        return '';
    }

    return $input;
}

// Bad: everything is cramped together making it hard to read
public function normalisePhoneNumber($input)
{
    $input = preg_replace('/[^0-9]+/', '', $input);
    if (trim($input) === '') {
        return '';
    }
    return $input;
}
```

---

Statements should have to breathe. In general always add blank lines between statements, unless they're a sequence of single-line equivalent operations. This isn't something enforceable, it's a matter of what looks best in its context.

```php
// Good
public function getPage($url)
{
    $page = $this->pages()->where('slug', $url)->first();

    if (! $page) {
        return null;
    }

    if ($page['private'] && ! Auth::check()) {
        return null;
    }

    return $page;
}

// Bad: Everything's cramped together.
public function getPage($url)
{
    $page = $this->pages()->where('slug', $url)->first();
    if (! $page) {
        return null;
    }
    if ($page['private'] && ! Auth::check()) {
        return null;
    }
    return $page;
}
```

```php
// Good: A sequence of single-line equivalent operations.
public function up()
{
    Schema::create('users', function (Blueprint $table) {
        $table->increments('id');
        $table->string('name');
        $table->string('email')->unique();
        $table->string('password');
        $table->rememberToken();
        $table->timestamps();
    });
}
```

---

Avoid extra whitespace inside conditional braces (`{}`). We want the code to breath, but there's no need to be wasteful.

```php
// Good
if (trim($input) === '') {
    return '';
}

// Bad
if (trim($input) === '') {

    return '';

}
```

## Exceptions [#](https://lsg.spdload.dev/general/#exceptions)

Exceptions are a good way to control the flow of your code and provide detailed and descriptive error messages to developers.

When you inline exceptions in your code, it can quickly become difficult to understand all the possible error cases as they may be split across different files, which in turn makes it harder to see why you might be raising exceptions wholistically.

```php
public function index()
{
    if ($this->invalidStep(request('step'))) {
        throw new RuntimeException(
            vsprintf('The step [%s] is invalid. Valid steps are: %s', [
                request('step'),
                $this->validSteps()->implode(', '),
            ])
        );
    }

    // ...
}
```

---

Placing exception messages behind static methods on custom exception classes allows you to keep your code clear and concise, hiding the implementation detail i.e. the precise message and formatting in a class specifically responsible for that behaviour.

```php
// SignupException.php
class SignupException extends RuntimeException
{
    public static function invalidStep($step, $validSteps)
    {
        return new static(vsprintf('The step [%s] is invalid. Valid steps are: %s', [
            $step,
            $this->validSteps()->implode(', '),
        ]));
    }
}

// SignupsController.php
public function index()
{
    if ($this->invalidStep(request('step'))) {
        throw SignupException::invalidStep(request('step'), $this->validSteps());
    }
}
```

Co-locating messages inside your custom exception classes makes it not only easier to track each message and its formatting, but also to determine when an exceptions messages start to drift apart in focus, allowing you to split them up into new exception classes with a narrower focus and more concise API.

Learn more about [formatting exception messages](http://rosstuck.com/formatting-exception-messages) from [Ross Tuck](https://twitter.com/rosstuck).

## Don't talk to strangers [#](https://lsg.spdload.dev/general/#dont-talk-to-strangers)

The [Law of Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter) is a particularly useful design guideline that helps you to build more concise APIs. Consider the following Eloquent model.

```php
class Post extends Model
{
    public function comments()
    {
        return $this->hasMany(Comment::class);
    }
}
```

---

When a new `Comment` is added to a `Post` you might write something along the following lines in your controller.

```php
// PostCommentsController.php
public function store(Post $post)
{
    $post->comments()->create([
        'author_id' => auth()->id(),
        'comment' => request('comment'),
    ]);

    return redirect()->route('posts.show', $post);
}
```

The Law of Demeter helps to decouple your code by encouraging you to ensure that each unit only has limited knowledge of other units. That is to say that your controller should not need to know how specifically a `Comment` is added to a `Post`.

Instead, consider abstract this implementation detail behind a new method. In doing so, your controller needn't know how a `Post` and `Comment` would be related, and if the implementation changes, the call to do so doesn't. SOLID!

```php
// Post.php
public function addComment($author, $comment)
{
    $this->comments()->create([
        'author_id' => $author->id,
        'comment' => $comment,
    ]);
}

// PostCommentsController.php
public function store(Post $post)
{
    $post->addComment(auth()->user(), request('comment'));

    return redirect()->route('posts.show', $post);
}
```
