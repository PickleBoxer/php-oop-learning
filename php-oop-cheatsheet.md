# 📋 PHP Advanced OOP Cheatsheet

## 📑 Table of Contents

- [📦 Namespaces](#namespaces)
- [🧩 Traits](#traits)
- [🎭 Anonymous Classes](#anonymous-classes)
- [🔗 Late Static Binding](#late-static-binding)
- [👯 Object Cloning and Comparison](#object-cloning-and-comparison)
- [🔍 Reflection](#reflection)
- [✨ Magic Methods](#magic-methods)
- [📝 Type Declarations](#type-declarations)
- [🔄 Iterators and Generators](#iterators-and-generators)
- [📲 Closure and Callable](#closure-and-callable)

---

## 📦 Namespaces

Namespaces organize code and prevent name collisions.

### 📝 Define a namespace
```php
<?php
namespace App\Services;

class UserService {
    // Implementation
}
```

### 📥 Import and use classes
```php
<?php
// Full qualified name
$service = new \App\Services\UserService();

// Import with use
use App\Services\UserService;
$service = new UserService();

// Import with alias
use App\Services\UserService as UserSrv;
$service = new UserSrv();
```

### 📦 Group use declarations (PHP 7.0+)
```php
<?php
use App\Services\{
    UserService,
    AuthService,
    PaymentService
};
```

---

## 🧩 Traits

Traits enable code reuse across multiple classes without inheritance.

### 📝 Basic trait usage
```php
<?php
trait Logger {
    public function log($message) {
        echo date('Y-m-d H:i:s') . ": $message\n";
    }
}

class UserService {
    use Logger;
    
    public function createUser($name) {
        $this->log("Creating user $name");
        // Implementation
    }
}
```

### 🔀 Resolving method conflicts
```php
<?php
trait FileLogger {
    public function log($message) {
        file_put_contents('app.log', $message . PHP_EOL, FILE_APPEND);
    }
}

trait ConsoleLogger {
    public function log($message) {
        echo $message . PHP_EOL;
    }
}

class Service {
    // Choose FileLogger's implementation
    use FileLogger, ConsoleLogger {
        FileLogger::log insteadof ConsoleLogger;
        ConsoleLogger::log as consoleLog;
    }
}
```

---

## 🎭 Anonymous Classes

Anonymous classes allow creating one-time use objects without defining a named class.

### 📝 Basic anonymous class
```php
<?php
$logger = new class {
    public function log($message) {
        echo date('Y-m-d H:i:s') . ": $message\n";
    }
};

$logger->log('Test message');
```

### 🧩 Implementing interfaces
```php
<?php
interface Logger {
    public function log($message);
}

$logger = new class implements Logger {
    public function log($message) {
        echo "LOG: $message\n";
    }
};
```

---

## 🔗 Late Static Binding

Late static binding allows referencing the called class in a static inheritance context.

### ❌ Problem with self::
```php
<?php
class ParentClass {
    public static function getClassName() {
        return self::class; // Always returns 'ParentClass'
    }
}

class ChildClass extends ParentClass {
}

echo ChildClass::getClassName(); // Outputs: ParentClass
```

### ✅ Solution with static::
```php
<?php
class ParentClass {
    public static function getClassName() {
        return static::class; // Returns the called class
    }
}

class ChildClass extends ParentClass {
}

echo ChildClass::getClassName(); // Outputs: ChildClass
```

---

## 👯 Object Cloning and Comparison

### 📝 Basic cloning
```php
<?php
class User {
    public $name;
    
    public function __construct($name) {
        $this->name = $name;
    }
}

$user1 = new User('John');
$user2 = clone $user1;
$user2->name = 'Jane';

echo $user1->name; // John
echo $user2->name; // Jane
```

### 🔍 Deep cloning with __clone()
```php
<?php
class Address {
    public $city;
    
    public function __construct($city) {
        $this->city = $city;
    }
}

class User {
    public $name;
    public $address;
    
    public function __construct($name, Address $address) {
        $this->name = $name;
        $this->address = $address;
    }
    
    public function __clone() {
        // Create a deep copy of the Address object
        $this->address = clone $this->address;
    }
}
```

### 🔍 Object comparison
```php
<?php
$obj1 = new stdClass();
$obj1->name = 'John';

$obj2 = new stdClass();
$obj2->name = 'John';

$obj3 = $obj1;

// Equality (==) checks if objects have the same properties and values
var_dump($obj1 == $obj2); // bool(true)

// Identity (===) checks if objects are the same instance
var_dump($obj1 === $obj2); // bool(false)
var_dump($obj1 === $obj3); // bool(true)
```

---

## 🔍 Reflection

Reflection allows you to inspect and manipulate classes, methods, properties, and more at runtime.

### 📝 Examining a class
```php
<?php
class User {
    private $id;
    public $name;
    
    public function __construct($id, $name) {
        $this->id = $id;
        $this->name = $name;
    }
}

$reflection = new ReflectionClass(User::class);

// Get class info
echo $reflection->getName(); // User

// Get methods
foreach ($reflection->getMethods() as $method) {
    echo $method->getName() . ' is ' . 
        ($method->isPublic() ? 'public' : 'private') . "\n";
}
```

### 🔓 Accessing private members
```php
<?php
$user = new User(1, 'John');

// Access private property
$idProperty = $reflection->getProperty('id');
$idProperty->setAccessible(true);
echo $idProperty->getValue($user); // 1
```

---

## ✨ Magic Methods

Magic methods allow you to hook into PHP's built-in behaviors.

### 🏗️ Constructor and destructor
```php
<?php
class Connection {
    public function __construct() {
        echo "Connection opened\n";
    }
    
    public function __destruct() {
        echo "Connection closed\n";
    }
}
```

### 🔍 Property overloading
```php
<?php
class User {
    private $data = [];
    
    public function __get($name) {
        return $this->data[$name] ?? null;
    }
    
    public function __set($name, $value) {
        $this->data[$name] = $value;
    }
}

$user = new User();
$user->name = 'John';    // Calls __set()
echo $user->name;        // Calls __get(), outputs: John
```

### 📞 Method overloading
```php
<?php
class Service {
    public function __call($name, $arguments) {
        echo "Called $name with " . count($arguments) . " arguments\n";
    }
    
    public static function __callStatic($name, $arguments) {
        echo "Called static method $name\n";
    }
}
```

---

## 📝 Type Declarations

Type declarations enforce data types for method parameters, return values, and properties.

### 📝 Basic type declarations
```php
<?php
function add(int $a, int $b): int {
    return $a + $b;
}
```

### 🤝 Union types (PHP 8.0+)
```php
<?php
function process(string|int|float $value): string|int {
    if (is_string($value)) {
        return "String: $value";
    } else {
        return intval($value * 2);
    }
}
```

### 🏗️ Property types (PHP 7.4+)
```php
<?php
class User {
    public string $name;
    public ?string $email = null;
    private int $age;
    
    public function __construct(string $name, ?string $email, int $age) {
        $this->name = $name;
        $this->email = $email;
        $this->age = $age;
    }
}
```

---

## 🔄 Iterators and Generators

Iterators allow you to define custom iteration for objects, while generators simplify creating iterators.

### 🔄 Iterator interface
```php
<?php
class NumbersIterator implements Iterator {
    private $numbers;
    private $position = 0;
    
    public function __construct(array $numbers) {
        $this->numbers = $numbers;
    }
    
    public function current() {
        return $this->numbers[$this->position];
    }
    
    public function key() {
        return $this->position;
    }
    
    public function next() {
        $this->position++;
    }
    
    public function rewind() {
        $this->position = 0;
    }
    
    public function valid() {
        return isset($this->numbers[$this->position]);
    }
}
```

### 🧩 Basic generator
```php
<?php
function countTo($n) {
    for ($i = 1; $i <= $n; $i++) {
        yield $i;
    }
}

foreach (countTo(5) as $number) {
    echo $number . ' ';
} // Outputs: 1 2 3 4 5
```

### 🌊 Memory efficiency example
```php
<?php
// ❌ Uses a lot of memory
function getNumbersArray($max) {
    $numbers = [];
    for ($i = 1; $i <= $max; $i++) {
        $numbers[] = $i;
    }
    return $numbers;
}

// ✅ Memory efficient
function getNumbersGenerator($max) {
    for ($i = 1; $i <= $max; $i++) {
        yield $i;
    }
}
```

---

## 📲 Closure and Callable

Closures are anonymous functions, and callables are anything that can be called like a function.

### 📝 Basic anonymous function
```php
<?php
$add = function($a, $b) {
    return $a + $b;
};

echo $add(5, 3); // 8
```

### 🔗 Capturing variables with 'use'
```php
<?php
$multiplier = 2;
$multiply = function($number) use ($multiplier) {
    return $number * $multiplier;
};

echo $multiply(5); // 10
```

### 🏹 Arrow functions (PHP 7.4+)
```php
<?php
// Regular closure
$double = function($x) {
    return $x * 2;
};

// Arrow function
$double = fn($x) => $x * 2;
```

### 🔄 Common array functions with callbacks
```php
<?php
$numbers = [1, 2, 3, 4, 5];

// 🗺️ map - transforms each element
$doubled = array_map(fn($n) => $n * 2, $numbers);
// [2, 4, 6, 8, 10]

// 🔍 filter - keeps elements that pass the test
$even = array_filter($numbers, fn($n) => $n % 2 === 0);
// [2, 4]

// 📊 reduce - accumulates values
$sum = array_reduce($numbers, fn($carry, $n) => $carry + $n, 0);
// 15
```

---

[⬅️ Back to Advanced OOP](./02-advanced-oop/README.md)