#develop/tree 

Когда мы начинаем разговор про базовые принципы ООП, то первым же делом всегда всплывает аббревиатура SOLID. По сути, SOLID и есть та самая «база», без которой нельзя двигаться дальше. Также имейте в виду, что в 9/10 собеседований на позиции от мидла до сеньора, вас обязательно спросят про SOLID. Всё это очень весомые доводы, которые никак нельзя проигнорировать, а значит давайте узнаем что скрывается за этими пятью буквами.

# Зачем нужен SOLID
Пять принципов SOLID — это как семь нот в музыке: так или иначе, архитектура вашего проекта будет строиться на их основе. Каждый буква отвечает за один принцип. Каждый принцип — это требование, которому должен соответствовать качественный ООП код.

## Все пять принципов SOLID

1. **Single Responsibility** — принцип единственной ответственности Этот принцип самый простой и он вам уже должен быть знаком. Один класс отвечает за один функционал. Проектируя класс, организуйте его методы по тем сущностям и процессам, с которыми они работают. Если легко можно выделить несколько групп, то стоит подумать о выделении отдельных сущностей и переносе этих методов в них.

2. **Open/Closed**— принцип открытости/закрытости Означает, что классы можно расширять, но нельзя изменять. Класс с самого начала нужно проектировать так, чтобы любое дальнейшее развитие функционала не требовало изменения кода этого класса, но позволяло расширять функционал с помощью классов наследников.

3. **Liskov Substitution** — принцип подстановки Барбары Лисков Возможно, это самый непонятный для новичка принцип, но на самом деле, здесь скрываются уже знакомые вам термины: полиморфизм и использование интерфейсов вместо реализации. Говоря простым языком, это принцип требует, чтобы все дочерние классы от базового класса соблюдали такой контракт, при котором клиентский код может использовать любой экземпляр такого класса-наследника без нарушения корректности работы программы. Т.е. дочерние классы должны быть заменяемыми для родительского класса. То есть методы и функции, которые работают с суперклассом, должны иметь возможность без проблем работать и с его подклассами.

4. **Interface Segregation** — принцип разделения интерфейса. Это очень простой для понимания принцип: множество интерфейсов с ограниченным наборов методов лучше, чем один интерфейс с большим количеством методов.

5. **Dependency Inversion** — принцип инверсии зависимостей Сразу стоит сказать, что этому принципу посвящена целая следующая глава. Если кратко, то этот принцип требует, чтобы модули высшего порядка не зависели от модулей низшего порядка. Т.е. класс не должен иметь жестко прописанных в нем зависимостей от объектов других классов, например, через оператор new. Вместо этого зависимости должны передаваться в класс явным образом, например через аргументы конструктора.

SOLID с примерами кода
Хорошо, мы разобрались с этими принципами «на словах», теперь давайте посмотрим как это реализуется в php-коде.

**Single Responsibility**
Предположим, у нас есть класс для пользователя:

```php
<?php

class User {
    private $email;
    
    public function store() {
        // Сохраняем данные пользователя в БД
    }
}
```
В данном примере метод store не должен входить в зону ответственности класса User, а должен скорее находиться в классе, который работает с базой данных. Так что решением здесь будет создать еще один класс и разделить между ними ответственность.
Убираем из класса User работу с БД:

```php
<?php

class User {
  
    private $email;
}
```
Добавляем класс для работы с БД:

```php
<?php

class UserDB {
  
    public function store(User $user) {
        // Сохраняем данные пользователя в БД
    }
}
```

**Open/Closed**
Предположим, мы хотим посчитать общую площадь каких-нибудь объектов и чтобы сделать это мы создаем класс AreaCalculator, который должен уметь читать площадь любой фигуры. Проблема здесь в том, что у каждой фигуры может свой способ подсчета площади: для квадрата он один, а для круга — другой.

```php
<?php

class Rectangle {
    public $width;
    public $height;
    
    public function __construct($width, $height) {
        $this->width = $width;
        $this->height = $height;
    }
}

class Square {
    public $length;
    
    public function __construct($length) {
        $this->length = $length;
    }
}


class AreaCalculator {
  
    protected $shapes;
    
    public function __construct($shapes = array()) {
        $this->shapes = $shapes;
    }
    
    public function sum() {
        $area = [];
        
        foreach($this->shapes as $shape) {
            if($shape instanceof Square) {
                $area[] = pow($shape->length, 2);
            } else if($shape instanceof Rectangle) {
                $area[] = $shape->width * $shape->height;
            }
        }
        return array_sum($area);
    }
}
```

Если мы добавим еще одну фигуру типа Circle, то нам понадобится изменить AreaCalculator, чтобы добавить поддержку подсчета площади новой фигуры, что не является легким в поддержке подходом к проектированию классов. Решением здесь будет создать простой интерфейс типа Shape, который будет иметь метод area () и этот метод должен быть реализован каждым классом фигур. Следуя этому пути, мы будем использовать только один метод для подсчета площади и когда понадобится добавить класс новой формы, то этот класс будет обязан применить интерфейс Shape

```php
<?php

interface Shape {
    public function area();
}

class Rectangle implements Shape {
  
    private $width;
    private $height;
    
    public function __construct($width, $height) {
        $this->width = $width;
        $this->height = $height;
    }
    
    public function area() {
        return $this->width * $this->height;
    }
}

class Square implements Shape {
  
    private $length;
    
    public function __construct($length) {
        $this->length = $length;
    }
    
    public function area() {
        return pow($this->length, 2);
    }
}


class AreaCalculator {
  
    protected $shapes;
    
    public function __construct($shapes = array()) {
        $this->shapes = $shapes;
    }
    
    public function sum() {
        $area = [];
        
        foreach($this->shapes as $shape) {
            $area[] = $shape->area();
        }
        return array_sum($area);
    }
}

```

Interface Segregation
Этот принцип гласит, что класс не должен применять интерфейсы, которые он не собирается использовать. Решением здесь будет разработать специализированные интерфейсы вместо интерфейсов общего назначения.

Представим, что мы изобрели машину FutureCar, которая может как ездить, так и летать.

```php
<?php

interface VehicleInterface {
    public function drive();
    public function fly();
}

class FutureCar implements VehicleInterface {
    
    public function drive() {
        echo 'Едем на машине будущего!';
    }
  
    public function fly() {
        echo 'Летим на машине будущего!';
    }
}

class Car implements VehicleInterface {
    
    public function drive() {
        echo 'Едем на машине!';
    }
  
    public function fly() {
        throw new Exception('Not implemented method');
    }
}

class Airplane implements VehicleInterface {
  
    public function drive() {
        throw new Exception('Not implemented method');
    }
    
    public function fly() {
        echo 'Летим на самолете!';
    }
}
```

Главная проблема здесь, как вы видите, это то, что Car и Airplane содержат методы, которые не используются. Решением будет разбить VehicleInterface на два других, более специфичных интерфейса, которые будут использоваться только когда действительно необходимы.

```php
<?php

interface CarInterface {
    public function drive();
}

interface AirplaneInterface {
    public function fly();
}

class FutureCar implements CarInterface, AirplaneInterface {
    
    public function drive() {
        echo 'Едем на машине будущего!';
    }
  
    public function fly() {
        echo 'Летим на машине будущего!';
    }
}

class Car implements CarInterface {
    
    public function drive() {
        echo 'Едем на машине!';
    }
}

class Airplane implements AirplaneInterface {
    
    public function fly() {
        echo 'Летим на самолете!';
    }
}
```

**Dependency Inversion**
А этому принципу посвящена следующая глава, поэтому ищите подробный обзор там.

[Принципы SOLID в картинках](https://up.htmlacademy.ru/php-3/2/module/1/item/5)
[Принципы SOLID о которых должен знать каждый разработчик](https://up.htmlacademy.ru/php-3/2/module/1/item/6)

---
prev:[[Главные принципы ООП]] next:[[Dependency Injection (lesson)]]
