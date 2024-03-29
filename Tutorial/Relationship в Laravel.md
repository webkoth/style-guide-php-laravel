#develop/tree 

Eloquent может предложить больше возможностей и имеет несколько интересных подходах, о которых мы поговорим особо. Но пока давайте кратко разберём базовые функции любого ORM — организация связей.

## Виды связей

Вы знаете, что в реляционных БД есть следующие виды связей:

- один к одному
- один ко многим
- многие ко многим

Сразу рассмотрим следующие виды связей на примере:

- у сериала есть связь с жанром (один к одному)
- у сериала есть множество серий (один ко многим)
- у множества пользователей есть связь с множеством просмотренных серий (многие ко многим)
Вот как это будет реализовано в коде:

**связь сериала и жанра**

```php
// Shop.php
public function genre()
{
	return $this->hasOne(Genre::class);
}
//Genre.php

public function shows(): BelongsToMany
{
	return $this->belongsToMany(Show::class);
}

// связь сериала с сериями

//Show.php

public function episodes(): HasMany
{
    return $this->hasMany(Episode::class);
}
// Episode.php

public function show(): BelongsTo
{
	return $this->belongsTo(Show::class);
}
```


Связь пользователей с просмотренными сериалами

User.php

```php
public function episodes(): BelongsToMany
{
	return $this->belongsToMany(Episode::class);
}
```

Последний пример надо разобрать отдельно. Как вы знаете, для организации связи многие ко многим требуется промежуточная таблица. Здесь мы полагаем, что такая таблица уже существует и её имя будет строиться по именам моделей, входящих в связь. В данном случае таблица должна называться episode_user (соглашение об именовании требует использовать имена моделей в алфавитном порядке, в единственном числе), а вот её структура:

id	episode_id	user_id
Но вы можете переопределить название таблицы и полей, указав необходимые значения параметрами метода отношений.

Полиморфные связи
Поговорим об одной крайне интересной и полезной возможности Eloquent — полиморфных связях. Если кратко, то это способность добавить одной дочерней модели отношения с двумя и более моделями используя одну связь.
Хорошим примером здесь будет модель комментариев: в нашем приложении комментарий можно оставить как к эпизоду, так и к сериалу. Т.е. у нас будет только одна таблица comments, где каждый комментарий может быть назначен как сериалу, так и эпизоду.
Для начала посмотрим на структуру таблицы comments:

`id	text	user_id	commentable_id	commentable_type	created_at`

Теперь доработаем наши модели, чтобы добавить туда полиморфные связи:

```php
class Comment extends Model
{
    public function commentable()
    {
        return $this->morphTo();
    }
}
 
class Show extends Model
{
    public function comments()
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}
 
class Episode extends Model
{
    public function comments()
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}
```

Этого полностью достаточно. Сохранение и получение моделей будет работать точно также, как и для обычных связей.

Интересные возможности
Давайте познакомимся с парой полезных фишек Eloquent, которые нам точно пригодятся при разработке проекта, тем более что таких возможностей не было в фреймворке Yii 2.

Absence
Иногда требуется найти модели, у которых нет связанных данных.
Например, нам требуется показать все эпизоды, для которых не оставили ни одного комментария. Для таких целей есть специальный метод doesntHave, который позволит загрузить модели без связей:

$episodes = Episode::doesntHave('comments')->get();
Модели по умолчанию
Пользу этой возможности сложно переоценить. Дело в том, что одной из самых распространённых ошибок в веб-приложениях с ORM внутри — это обращение к несуществующей модели-связи.
Допустим, вы получили модель сериала и обратились к его жанру вот так:

$show = Show::find(1);
$show->genre->title;
Что произойдет, если у этого сериала не назначен жанр? Произойдет ошибка Attempt to read property "title" on null.
Чтобы не приходилось каждый раз проверять связанную модель на null, Laravel предлагает использовать встроенный подход, который реализует паттерн «null object». Суть этого паттерна в автоматической подстановке объекта вместо null по умолчанию. Чтобы добавить это поведение, необходимо немного уточнить связь:

```php
public function genre()
{
	return $this->hasOne(Genre::class)->withDefault();
}
```

Этот код вернёт пустую модель Genre, в случае если для сериала не будет найдено связанного жанра.

Нетерпеливая загрузка
Вам должна быть знакома эта концепция из курса по Yii 2.
По умолчанию, Laravel будет выполнять запрос на получение связанной записи только при обращении к полю-связи. Это называется ленивой загрузкой. Однако, в Eloquent есть возможность подключить нетерпеливую (или жадную) загрузку (eager loading). Тогда при получении родительской модели автоматически будут подгружаться её связи.

Также, как и в Yii, для добавления модели жадной загрузки необходимо дополнительно вызывать метод with, с указанием имени связи, которую нужно подгрузить.

```php
$shows = Show::with('genre')->get();

foreach ($shows as $show) {
    echo $show->genre->title;
}
```

Этот код выполнит только два SQL запроса. Один запрос, чтобы получить все сериалы и еще один для загрузки всех жанров для этих сериалов.

Возможно, вам захочется настроить модель на постоянное использование жадной загрузки по умолчанию. Такая опция также доступа. Чтобы этого достичь, потребуется определить поле with внутри модели:

```php
class Show extends Model
{
    protected $with = ['genres'];
}
```

Сохранение связанных моделей
Само собой, как и любая другая продвинутая реализация ORM, Eloquent предоставляет возможность для сохранения связанных моделей. Допустим, нам требуется добавить в сериал новую серию. Вместо того, чтобы вручную указывать в полях внешний и первичные ключи двух моделей, можно просто задействовать метод save:

```php
$episode = new Episode(['title' => 'The new begining']); 
$tvshow = Show::find(1);

$tvshow->episodes()->save($episode);
```

Этот код одновременно сохранит новый эпизод, а также создаст связь между этим эпизодом и сериалом.

prev:[[ORM в Laravel]] next:[[QueryBuilder]]

