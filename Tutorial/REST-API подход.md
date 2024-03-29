#develop/tree 

## Общая информация

Давайте кратко вспомним основные постулаты этого подхода, которые понадобятся нам для понимания всего происходящего далее. Также стоит особо отметить, что в прошлом курсе мы знакомились с REST-подходом со стороны клиента (потребителя), но сейчас мы будем рассматривать реализацию сервера (поставщика данных).

Начнём с того, что целью разработки вашего личного проекта является создание API-сервера. Иными словами, у вашего веб-приложения нет интерфейса в его привычном понимании. Вместо этого любые URL такого сайта должны возвращать не верстку для показа пользователю, а данные в формате JSON. Эти данные будут смотреть не люди, а другие программы-клиенты. Например, мобильное приложение, но в вашем случае таким клиентом является SPA «Что посмотреть».

REST-API — это формат, который описывает протокол взаимодействия клиента с сервером, но на более высоком уровне, чем это делает протокол HTTP. По сути, REST можно считать в некотором роде надстройкой на HTTP, потому что используются его возможности, такие как метод запроса или код ответа. К этому ещё добавляются соглашение по формату запроса-ответа и дополнительные семантические требования.

## Основы REST

### Ресурсы

Ресурс — это сущность, над которой производятся действия. REST API строится вокруг доступа к ресурсам и управления ими. Один ресурс отвечает за одну сущность. Такой сущностью может быть пользователь, эпизод сериала или категория. Чаще всего, в коде приложения каждой сущности также соответствует отдельная модель. Поэтому можно поставить знак равенства между сущностью в REST понимании и моделью в приложении.

### Методы

Метод — это тип операции, которая производится над ресурсом. Такими операциями могут быть создание ресурса, обновление, удаление, чтение. Под каждый такой глагол есть отдельный HTTP метод:

`GET` — для чтения ресурса;
`POST` — для создания ресурса;
`PUT` — для обновления ресурса;
`DELETE` — для удаления ресурса.
Т.к. любой HTTP запрос состоит из метода и URL, то получается, что сам выбранный метод уже передает на сервер информацию о сути производимой операции.

### Коды и формат ответа

В протоколе HTTP предусмотрено множество предопределенных числовых кодов, которые означают статус выполнения операции. Такие коды могут сигнализировать об ошибке на стороне сервера, либо, наоборот, сообщать, что все прошло хорошо.
REST-API также активно полагается на коды ответа, чтобы передавать с их помощью дополнительную информацию.
Например, если у пользователя нет доступа к запрошеному ресурсу, то API-сервер вернёт код 403.

Конечно, одних только кодов недостаточно для определения всего ответа. Помимо кода, протокол предусматривает и «тело ответа»: та самая полезная информация, которую клиент должен получить в ответ на свой запрос. Такой информацией, к примеру, может быть список эпизодов выбранного сериала.
REST-API никак не регламентирует требования к сериализации формата ответа, но, тем не менее, есть общие правила, которые обычно соблюдаются при разработке API:

- **соблюдение REST-семантики** т. е. правильное использование методов и кодов ответа. 
- **сериализация в формате JSON** данные, которые возвращает API, преобразуются в формат JSON
- **правильные заголовки ответа** сообщают клиенту дополнительную информацию. Например, в заголовках передаются тип формата ответа, что помогает клиенту правильно распарсить полученные значения

### Реализация REST подхода в Laravel

В следующих главах мы более подробно рассмотрим все возможности, которые предлагает фреймворк для реализации упомянутых выше требований к REST-серверу. Пока же сделаем краткий обзор функций Laravel, которые мы будем активно использовать в этом разделе.

## Роутинг и контроллеры

Начнем с реализации ресурсов.
В самом простом случае, один ресурс будет означать одну таблицу в БД. Модель для этой таблицы легко создать средствами Laravel не написав ни строчки кода: всё благодаря возможностями автогенерации artisan и ORM-слою Eloquent (пойдет речь в следующей главе).

В Laravel очень гибкая система роутинга. Благодаря этому, становится очень легко сформировать URL по всем правилам REST, чтобы в ответ на запрос вызывался соответствующий контроллер и его метод.
Например, легко описать конфигурацию для реализации кода, который будет вызываться по такому запросу:

```
PUT https://larashows/series/58/episode/10
```

Данный запрос, как можно легко догадаться из его семантики, позволяет обновить информацию по одному эпизоду указанного сериала. Средствами Laravel легко реализовать такой роут.

### Формат запроса/ответа

Как правило, запросы на добавление и обновление информации содержат тело запроса. В теле запроса клиент передаёт информацию, которую хочет сохранить на сервере. Такая информация, также как и ответ, кодируется в JSON формате. Laravel позволяет просто настроить автоматическое преобразование сериализованных в строку данных в привычные php-массивы. Есть только одно требование — клиент должен передавать верные заголовки запроса с указанием формата данных.

### Формат ответа

Как и с запросом, Laravel может также сильно упростить формирование ответа, как на уровне кода ответа, так и с помощью автоматической сериализации данных в формат JSON.

[rest простым языком](https://medium.com/@andr.ivas12/rest-%D0%BF%D1%80%D0%BE%D1%81%D1%82%D1%8B%D0%BC-%D1%8F%D0%B7%D1%8B%D0%BA%D0%BE%D0%BC-90a0bca0bc78)
[Введение в REST API — RESTful веб-сервисы](https://habr.com/ru/articles/483202/)

prev:[[Сервис-провайдеры]] next:[[2_resources/develop/laravel/tutorial/Controllers]]


