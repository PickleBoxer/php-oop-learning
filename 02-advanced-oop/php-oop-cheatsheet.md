# 📘 PHP Advanced OOP Cheatsheet

> **💡 Pro Tip:** "Keep this cheatsheet handy as a quick reference while working with advanced PHP OOP concepts. Sometimes even experienced developers need to refresh their memory on syntax details!"

## 📋 Quick Navigation

| Emoji | Topic | Description |
|:---:|---|---|
| 📦 | [Namespaces](#namespaces) | Organization and name collision prevention |
| 🧩 | [Traits](#traits) | Code reuse mechanism |
| 🎭 | [Anonymous Classes](#anonymous-classes) | One-time use class instances |
| 🔗 | [Late Static Binding](#late-static-binding) | Dynamic static method inheritance |
| 👯 | [Object Cloning](#object-cloning) | Deep and shallow object copying |
| ⚖️ | [Object Comparison](#object-comparison) | Compare object equality and identity |
| 🔍 | [Reflection](#reflection) | Runtime code introspection |
| ✨ | [Magic Methods](#magic-methods) | Special PHP object methods |
| 📝 | [Type Declarations](#type-declarations) | Parameter and return type validation |
| 🔄 | [Iterators](#iterators) | Custom object traversal |
| 🌱 | [Generators](#generators) | Memory-efficient iterations |
| 🧩 | [Closures](#closures) | Anonymous functions with state |
| 📞 | [Callables](#callables) | Function references and callbacks |

## 📦 Namespaces

Namespaces organize code and prevent name collisions - essential for large applications.

### Define a namespace
```php
<?php
namespace App\Services;

class UserService {
    // Implementation
}
```

### Import and use classes
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

> **🔑 Key Concept:** Always use vendor-specific namespace prefixes in libraries to prevent collisions.

### Group use declarations (PHP 7.0+)
```php
<?php
use App\Services\{
    UserService,
    AuthService,
    PaymentService
};
```

### Import functions and constants
```php
<?php
use function App\Utilities\format_price;
use const App\Utilities\MAX_ITEMS;
```

## 🧩 Traits

Traits enable horizontal code reuse across multiple classes - "copy-paste" functionality but better.

### Basic trait usage
```php
<?php
trait Logger {
    public function log($message) {
        echo date('Y-m-d H:i:s') . ": $message\n";
    }
}

class UserService {
    use Logger; // Import trait functionality
    
    public function createUser($name) {
        $this->log("Creating user $name");
        // Implementation
    }
}
```

> **💡 Pro Tip:** Traits are great for functionality that doesn't fit neatly into your inheritance hierarchy.

### Multiple traits
```php
<?php
trait Logger {
    public function log($message) {
        echo "Log: $message\n";
    }
}

trait Timestampable {
    public function getTimestamp() {
        return date('Y-m-d H:i:s');
    }
}

class Post {
    use Logger, Timestampable; // Use multiple traits
    
    public function save() {
        $this->log("Saved at " . $this->getTimestamp());
    }
}
```

### Resolving method conflicts
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
    // Resolve conflict by choosing which implementation to use
    use FileLogger, ConsoleLogger {
        FileLogger::log insteadof ConsoleLogger; // Use FileLogger's implementation
        ConsoleLogger::log as consoleLog;        // Create alias for ConsoleLogger's method
    }
}
```

### Changing method visibility
```php
<?php
trait Helper {
    public function helperMethod() {
        // Implementation
    }
}

class Service {
    use Helper {
        helperMethod as protected; // Change method visibility
    }
}
```

## 🎭 Anonymous Classes

Anonymous classes allow creating one-time use objects without defining a named class.

### Basic anonymous class
```php
<?php
$logger = new class {
    public function log($message) {
        echo date('Y-m-d H:i:s') . ": $message\n";
    }
};

$logger->log('Test message');
```

> **🔑 Key Concept:** Anonymous classes are ideal for simple implementations or adapters needed in a single place.

### With constructor parameters
```php
<?php
$formatter = new class('Y-m-d') {
    private $format;
    
    public function __construct($format) {
        $this->format = $format;
    }
    
    public function format($date) {
        return date($this->format, strtotime($date));
    }
};

echo $formatter->format('2023-01-15'); // e.g., 2023-01-15
```

### Implementing interfaces
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

## 🔗 Late Static Binding

Late static binding solves the problem of static method inheritance by referring to the called class, not the defined class.

### Problem with self::
```php
<?php
class ParentClass {
    public static function getClassName() {
        return self::class; // Always returns 'ParentClass'
    }
}

class ChildClass extends ParentClass {
    // No override necessary - we're demonstrating the issue
}

echo ChildClass::getClassName(); // Outputs: ParentClass (not what we want!)
```

### Solution with static::
```php
<?php
class ParentClass {
    public static function getClassName() {
        return static::class; // Returns the called class
    }
}

class ChildClass extends ParentClass {
    // No override needed!
}

echo ChildClass::getClassName(); // Outputs: ChildClass (correct!)
```

> **💡 Pro Tip:** `static::` is especially useful for implementing factory methods in base classes.

### Factory method example
```php
<?php
abstract class Model {
    public static function create(array $attributes) {
        $model = new static(); // Creates instance of the called class
        // Set attributes
        return $model;
    }
}

class User extends Model {
    // Inherits the create() method
}

$user = User::create(['name' => 'John']); // Returns User instance
```

## 👯 Object Cloning

### Basic cloning
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

echo $user1->name; // John (unchanged)
echo $user2->name; // Jane
```

> **⚠️ Warning:** By default, PHP performs a shallow clone, which can cause issues with nested objects.

### Deep cloning with __clone()
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

$address = new Address('New York');
$user1 = new User('John', $address);
$user2 = clone $user1;

// Change the cloned user's address
$user2->address->city = 'Boston';

echo $user1->address->city; // New York (unchanged)
echo $user2->address->city; // Boston
```

## ⚖️ Object Comparison

### Equality vs. Identity
```php
<?php
$obj1 = new stdClass();
$obj1->name = 'John';

$obj2 = new stdClass();
$obj2->name = 'John';

$obj3 = $obj1; // Reference to the same object

// Equality (==) checks if objects have the same properties and values
var_dump($obj1 == $obj2);  // bool(true) - same content

// Identity (===) checks if objects are the same instance
var_dump($obj1 === $obj2); // bool(false) - different instances
var_dump($obj1 === $obj3); // bool(true) - same instance
```

> **🔑 Key Concept:** Use `==` to compare object content and `===` to check if two variables reference the same object.

### Custom comparison logic
```php
<?php
class User {
    public $id;
    public $name;
    
    public function __construct($id, $name) {
        $this->id = $id;
        $this->name = $name;
    }
    
    // Custom method for comparing users
    public function equals(User $other) {
        // Consider users equal if they have the same ID
        return $this->id === $other->id;
    }
}

$user1 = new User(1, 'John');
$user2 = new User(1, 'Johnny'); // Same ID, different name

var_dump($user1 == $user2);         // bool(false) - different properties
var_dump($user1->equals($user2));   // bool(true) - same ID per our logic
```

## 🔍 Reflection

Reflection allows you to inspect and manipulate classes, methods, properties at runtime.

### Examining a class
```php
<?php
class User {
    private $id;
    public $name;
    
    public function __construct($id, $name) {
        $this->id = $id;
        $this->name = $name;
    }
    
    private function getId() {
        return $this->id;
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

// Get properties
foreach ($reflection->getProperties() as $property) {
    echo $property->getName() . ' is ' . 
        ($property->isPublic() ? 'public' : 'private') . "\n";
}
```

> **💡 Pro Tip:** Reflection is powerful for building frameworks but can impact performance, so use it primarily during initialization.

### Accessing private members
```php
<?php
$user = new User(1, 'John');

// Access private property
$idProperty = $reflection->getProperty('id');
$idProperty->setAccessible(true); // Allow access to private property
echo $idProperty->getValue($user); // 1

// Call private method
$idMethod = $reflection->getMethod('getId');
$idMethod->setAccessible(true); // Allow access to private method
echo $idMethod->invoke($user); // 1
```

### Creating objects dynamically
```php
<?php
$userReflection = new ReflectionClass(User::class);
$user = $userReflection->newInstance(1, 'John');
```

## ✨ Magic Methods

Magic methods are special methods that start with `__` and hook into PHP's object behaviors.

### Constructor and destructor
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

$conn = new Connection(); // Outputs: Connection opened
// When $conn goes out of scope: Outputs: Connection closed
```

### Property overloading
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
    
    public function __isset($name) {
        return isset($this->data[$name]);
    }
    
    public function __unset($name) {
        unset($this->data[$name]);
    }
}

$user = new User();
$user->name = 'John';    // Calls __set()
echo $user->name;        // Calls __get(), outputs: John
echo isset($user->name); // Calls __isset(), outputs: true
unset($user->name);      // Calls __unset()
```

> **🔑 Key Concept:** Magic methods let you handle undefined properties and methods, customize serialization, and more.

### Method overloading
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

$service = new Service();
$service->process('data');       // Called process with 1 arguments
Service::staticProcess();        // Called static method staticProcess
```

### String conversion and object invocation
```php
<?php
class Point {
    private $x, $y;
    
    public function __construct($x, $y) {
        $this->x = $x;
        $this->y = $y;
    }
    
    public function __toString() {
        return "($this->x, $this->y)";
    }
    
    public function __invoke($factor) {
        return new Point($this->x * $factor, $this->y * $factor);
    }
}

$point = new Point(1, 2);
echo $point;           // Outputs: (1, 2)
$scaled = $point(2);   // Uses __invoke to create a new scaled point
echo $scaled;          // Outputs: (2, 4)
```

## 📝 Type Declarations

Type declarations enforce data types for parameters, return values, and properties.

### Basic type declarations
```php
<?php
function add(int $a, int $b): int {
    return $a + $b;
}

add(1, 2);    // Works
// add('1', 2);  // TypeError with strict_types=1
```

> **💡 Pro Tip:** Always use `declare(strict_types=1);` at the start of your files for stronger type checking.

### Nullable types (PHP 7.1+)
```php
<?php
function process(?string $value): ?string {
    if ($value === null) {
        return null;
    }
    return strtoupper($value);
}

echo process('hello'); // HELLO
echo process(null);    // null
```

### Union types (PHP 8.0+)
```php
<?php
function format(string|int $value): string {
    if (is_int($value)) {
        return "Number: $value";
    }
    return "Text: $value";
}

echo format('hello'); // Text: hello
echo format(42);      // Number: 42
```

### Property types (PHP 7.4+)
```php
<?php
class User {
    public string $name;
    public ?string $email = null;
    private int $age;
    
    public function __construct(string $name, int $age) {
        $this->name = $name;
        $this->age = $age;
    }
}
```

> **⚠️ Warning:** Property types are checked on every property access and assignment, not just at initialization.

### Constructor property promotion (PHP 8.0+)
```php
<?php
class Product {
    public function __construct(
        private string $name,
        private float $price,
        private ?string $description = null
    ) {}
    
    public function getName(): string {
        return $this->name;
    }
}
```

## 🔄 Iterators

Iterators allow you to define how your objects should be traversed in a `foreach` loop.

### Iterator interface
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

$iterator = new NumbersIterator([1, 2, 3, 4, 5]);
foreach ($iterator as $number) {
    echo $number . ' ';
} // Outputs: 1 2 3 4 5
```

> **🔑 Key Concept:** Implementing the `Iterator` interface gives you full control over how your object is traversed.

### IteratorAggregate interface
```php
<?php
class Collection implements IteratorAggregate {
    private $items = [];
    
    public function add($item) {
        $this->items[] = $item;
    }
    
    public function getIterator(): Traversable {
        return new ArrayIterator($this->items);
    }
}

$collection = new Collection();
$collection->add('one');
$collection->add('two');

foreach ($collection as $item) {
    echo $item . ' ';
} // Outputs: one two
```

> **💡 Pro Tip:** Use `IteratorAggregate` when you want to make a class iterable with minimal code.

## 🌱 Generators

Generators simplify creating iterators with minimal memory usage.

### Basic generator
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

### Generator with keys
```php
<?php
function getUsers() {
    yield 'admin' => 'John';
    yield 'editor' => 'Jane';
    yield 'user' => 'Bob';
}

foreach (getUsers() as $role => $name) {
    echo "$role: $name\n";
}
```

> **🔥 Advantage:** Generators process one item at a time, allowing you to work with large datasets using minimal memory.

### Memory efficiency example
```php
<?php
// Uses a lot of memory
function getNumbersArray($max) {
    $numbers = [];
    for ($i = 1; $i <= $max; $i++) {
        $numbers[] = $i;
    }
    return $numbers;
}

// Memory efficient
function getNumbersGenerator($max) {
    for ($i = 1; $i <= $max; $i++) {
        yield $i;
    }
}

// $numbers = getNumbersArray(1000000); // ~80MB
$numbers = getNumbersGenerator(1000000); // ~1KB

foreach ($numbers as $number) {
    if ($number > 10) break;
    echo $number . ' ';
}
```

### Using yield from
```php
<?php
function getEven($max) {
    for ($i = 2; $i <= $max; $i += 2) {
        yield $i;
    }
}

function getOdd($max) {
    for ($i = 1; $i <= $max; $i += 2) {
        yield $i;
    }
}

function getAll($max) {
    yield from getEven($max);
    yield from getOdd($max);
}

foreach (getAll(10) as $number) {
    echo $number . ' ';
} // Outputs: 2 4 6 8 10 1 3 5 7 9
```

## 🧩 Closures

Closures are anonymous functions that can capture variables from their surrounding scope.

### Basic anonymous function
```php
<?php
$add = function($a, $b) {
    return $a + $b;
};

echo $add(5, 3); // 8
```

> **🔑 Key Concept:** Closures can be stored in variables, passed as arguments, or returned from functions.

### Capturing variables with 'use'
```php
<?php
$multiplier = 2;
$multiply = function($number) use ($multiplier) {
    return $number * $multiplier;
};

echo $multiply(5); // 10
```

### Arrow functions (PHP 7.4+)
```php
<?php
// Regular closure
$double = function($x) {
    return $x * 2;
};

// Arrow function - more concise
$double = fn($x) => $x * 2;

// Arrow functions automatically capture variables
$factor = 3;
$triple = fn($x) => $x * $factor;

echo $triple(5); // 15
```

> **💡 Pro Tip:** Arrow functions are perfect for short callbacks in array functions!

### Using closures with objects
```php
<?php
class Person {
    private $name;
    
    public function __construct($name) {
        $this->name = $name;
    }
}

$john = new Person('John');

$getName = function() {
    return $this->name;
};

$boundFunction = Closure::bind($getName, $john, Person::class);
echo $boundFunction(); // John
```

### Higher-order functions
```php
<?php
// Function that returns a function
function createMultiplier($factor) {
    return function($number) use ($factor) {
        return $number * $factor;
    };
}

$double = createMultiplier(2);
$triple = createMultiplier(3);

echo $double(5); // 10
echo $triple(5); // 15
```

## 📞 Callables

Callables are anything that can be called as a function.

### Different callable types
```php
<?php
// 1. Regular function name as string
function add($a, $b) { return $a + $b; }
$callable1 = 'add';

// 2. Method as array [object, method]
class Calculator {
    public function multiply($a, $b) {
        return $a * $b;
    }
}
$calc = new Calculator();
$callable2 = [$calc, 'multiply'];

// 3. Static method as array [class, method]
class Math {
    public static function subtract($a, $b) {
        return $a - $b;
    }
}
$callable3 = ['Math', 'subtract'];

// 4. Object with __invoke method
class Divider {
    public function __invoke($a, $b) {
        return $a / $b;
    }
}
$callable4 = new Divider();

// 5. Anonymous function/closure
$callable5 = function($a, $b) { return $a + $b; };

// Using callables
echo call_user_func($callable1, 5, 3); // 8
echo call_user_func($callable2, 5, 3); // 15
echo call_user_func($callable3, 5, 3); // 2
echo call_user_func($callable4, 6, 3); // 2
echo call_user_func($callable5, 4, 5); // 9
```

> **💡 Pro Tip:** You can check if something is callable with `is_callable($var)` before trying to invoke it.

### Common array functions with callbacks
```php
<?php
$numbers = [1, 2, 3, 4, 5];

// map - transforms each element
$doubled = array_map(fn($n) => $n * 2, $numbers);
// [2, 4, 6, 8, 10]

// filter - keeps elements that pass the test
$even = array_filter($numbers, fn($n) => $n % 2 === 0);
// [2, 4]

// reduce - accumulates values
$sum = array_reduce($numbers, fn($carry, $n) => $carry + $n, 0);
// 15

// sort with custom comparison function
$users = [
    ['name' => 'John', 'age' => 30],
    ['name' => 'Alice', 'age' => 25]
];

usort($users, fn($a, $b) => $a['age'] <=> $b['age']);
// Users sorted by age ascending
```

---

[Back to Advanced OOP](./README.md) | [Next: SOLID Principles](./03-solid-principles/README.md)

> **🔗 Related Resources:**
> * [PHP Manual - OOP](https://www.php.net/manual/en/language.oop5.php)
> * [PHP The Right Way](https://phptherightway.com/)
