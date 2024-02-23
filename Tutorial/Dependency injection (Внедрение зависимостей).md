#develop/tree  

source: https://www.kobzarev.com/programming/di/

# Проблема

```php
<?php
 
namespace PluginName;
 
class Order {
  public $id;
}
 
class OrderProcessing {
  public function create_new_order() {
    /* Логика выполнения заказа */
    $this->log( 'Order created!' );
  }
  private function log( string $message ) {
    echo "Save log with message: {$message}" . PHP_EOL;
  }
}
 
$order_processing = new OrderProcessing();
$order_processing->create_new_order();
```

Первая проблема, которую вы можете увидеть, — это принцип единой ответственности. В `OrderProcessing` мы используем логику класса `Logger`. Перенесем эту логику в отдельный класс.

```php
<?php
 
namespace PluginName;
 
class Logger {
  public function log( string $message ) {
    echo "Save log with message: {$message}" . PHP_EOL;
  }
}
 
class OrderProcessing {
  public function create_new_order() {
    /* Логика выполнения заказа */
    $logger = new Logger();
    $logger->log('Order created!');
  }
}
 
$order_processing = new OrderProcessing();
$order_processing->create_new_order();
```

Небольшой рефакторинг, и мы можем повторно использовать класс Logger в разных частях нашего приложения. Но мы не можем так легко изменить хранилище Logger.

Для понимания зависимостей класса OrderProcessing необходимо прочитать весь класс. Каждая внешняя функция или класс, который используется внутри нашего класса, называется зависимостями.

Некоторые зависимости хороши, например, некоторая функция PHP, которая очищает какое-то свойство. Но в нашем примере у нас есть плохая зависимость new Logger, которая называется hard dependency (жесткая зависимость).

Почему жесткая зависимость — это плохо? Потому что мы не можем изменить логику без изменения этих классов. Если я хочу изменить хранилище записей журнала, мне нужно изменить оба класса.

Как сделать наш пример более гибким? Конечно, используйте шаблон проектирования внедрения зависимостей.

# Встречайте Dependency Injection

```php
<?php
 
namespace PluginName;
 
class OrderProcessing {
  private $logger;
  public function __construct( Logger $loger ) {
    $this->logger = $logger;
  }
  public function create_new_order() {
    /* Some kind of logic for create order */
    $this->logger->log('Order created!');
  }
}
 
$logger           = new Logger();
$order_processing = new OrderProcessing( $logger );
$order_processing->create_new_order();
```

Лучше всего использовать интерфейс в конструкторе вместо Logger, но об этом позже.

# Больше и больше зависимостей

```php
<?php
 
namespace PluginName;
 
class OrderProcessing {
  private $logger;
  public function __construct( Logger $loger, OrderRepository $repository, SMSNotifier $sms_notifier ) {
    $this->logger       = $logger;
    $this->repository   = $repository;
     $this->sms_notifier = $sms_notifier;
  }
  public function create_new_order() {
           /* Some kind of logic for create order */
    $this->logger->log('Order created!');
  }
}
 
$repository       = new OrderRepository();
$sms_notifier     = new SMSNotifier();
$logger           = new Logger();
$order_processing = new OrderProcessing( $logger, $repository, $sms_notifier );
$order_processing->create_new_order();
```

Со временем бизнес-логика нашего приложения вырастает, и сложность нашего конструктора тоже становится выше. Для добавления каждой новой функции необходимо проделать много работы.

Чтобы управлять всеми зависимостями, нам нужно создать объект god или переместить эту логику в другую часть. Лучше всего использовать контейнер внедрения зависимостей. Давай попробуем.

# Dependency Injection Container

Надеюсь, вы уже знаете об автозагрузке ваших классов и файлов, потому что это первое, что нужно сделать, чтобы начать использовать Dependency Injection Container (контейнер внедрения зависимостей, DIC).

Для контейнера внедрения зависимостей существует множество различных пакетов. Я предпочитаю пакет rdlowrey/auryn. Установим его:

```bash
composer require rdlowrey/auryn
```
Мы описали, что класс OrderProcessing имеет в конструкторе аргументы Logger, OrderRepository и SMSNotifier.

А как загрузить DIC?
```php
<?php
 
use PluginName\OrderProcessing;
use Auryn\Injector;
 
require_once 'vendor/autoload.php';
 
$injector = new Injector();
$injector->make( OrderProcessing::class );
Вместо создания нового объекта через new OrderProcessing просто используйте $injector->make( OrderProcessing :: class )
```

# Inversion Of Control

Инверсия управления (Inversion of Control, IoC) — важный принцип объектно-ориентированного программирования, используемый для уменьшения связанности в компьютерных программах. Также архитектурное решение интеграции, упрощающее расширение возможностей системы, при котором поток управления программы контролируется фреймворком.

Wikipedia
Эта черная магия помогает вообще забыть о new instances в работе. Просто создайте новый файл и опишите конструктор с подсказкой типа. Контейнер внедрения зависимостей разрешает все зависимости, а не когда вы пытаетесь получить объект из контейнера.

Добавив этот замечательный инструмент, вы упростите взаимодействие между элементами, заменив его подсказкой типа.

Зависимости и интерфейсы
Как управлять зависимостями, если вы используете интерфейс? Создадим интерфейс ILogger:

```php
<?php
 
namespace PluginName;
 
interface ILogger {
 
  public function message();
 
}
```

И переименуем Logger в DBLogger:

```php
<?php
 
namespace PluginName;
 
class DBLogger implements ILogger {
 
  public function message() {
    // TODO: Implement message() method.
  }
 
}
```

И изменим конструктор класса OrderProcessing:

```php
<?php
 
namespace PluginName;
 
class OrderProcessing {
  // ...
  public function __construct( ILogger $logger ) {
    // ...
  }
  // ...
}
```

А что происходит? DIC не может решить ваши проблемы. Почему? Потому что вам нужно точно описать класс, который вы хотите использовать в этом классе. Итак, вам нужно решить эту проблему вручную:

```php
<?php
// ...
$injector->make(
  OrderProcessing::class,
  [
    $injector->make( DBLogger::class ),
  ]
);
```

Это означает, что мы можем автоматически загружать все наши зависимости, но в случаях с абстракциями разрешать их придётся вручную.

# Заключение

Если вы избегаете жестких зависимостей, это поможет вам создавать более качественные и масштабируемые решения. Чтобы упростить повседневную разработку, используйте простой и полезный инструмент, называемый контейнером внедрения зависимостей.
