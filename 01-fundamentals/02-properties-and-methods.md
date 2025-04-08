# 📊 Properties and Methods in PHP

> **💡 Pro Tip:** "Properties define what an object knows, while methods define what an object can do."

## 📋 Table of Contents

| Emoji | Topic | Description |
|:---:|---|---|
| 💾 | [Properties Basics](#properties-basics) | Understanding class variables and data storage |
| 🔐 | [Property Visibility](#property-visibility) | Public, protected, and private access control |
| 📝 | [Typed Properties](#typed-properties) | Ensuring data integrity with type declarations |
| ⚙️ | [Methods Basics](#methods-basics) | Implementing object behaviors through functions |
| 👁️ | [Method Visibility](#method-visibility) | Controlling access to object behaviors |
| 🌐 | [Static Members](#static-members) | Class-level properties and methods |
| 🔍 | [Return Types](#return-types) | Specifying and enforcing method output types |
| 🔒 | [Class Constants](#class-constants) | Working with unchangeable class values |
| 📝 | [Method Parameters](#method-parameters) | Customizing method behavior with inputs |
| 🎯 | [Constructor Promotion](#constructor-promotion) | Simplifying property initialization (PHP 8+) |
| ✅ | [Best Practices](#best-practices) | Recommended coding standards |

<a id="properties-basics"></a>
## 💾 Properties Basics

Properties are variables that belong to a class. They represent the state or characteristics of objects created from that class.

> **🔍 Real-world analogy:** If an object is a car, properties are its characteristics like color, brand, and mileage.

```php
<?php
class Car {
    // Property declarations
    public $brand;          // Make of the car, e.g., "Toyota"
    public $model;          // Model name, e.g., "Corolla"
    public $year;           // Year of manufacture
    public $color = "white"; // Default color if not specified
    private $mileage = 0;   // Starting mileage for new cars
}

$myCar = new Car();
$myCar->brand = "Honda";
$myCar->model = "Civic";
$myCar->year = 2023;
// $myCar->color remains "white" unless changed
// $myCar->mileage is private and cannot be accessed directly
```

> **💡 Key Point:** Unlike local variables, properties maintain their values between method calls and throughout the object's lifetime.

<a id="property-visibility"></a>
## 🔐 Property Visibility

PHP supports three visibility levels for properties, which control how and where they can be accessed.

| Visibility | Symbol | Description | Access |
|------------|--------|-------------|--------|
| 🟢 Public | `public` | Accessible from anywhere | From inside and outside the class |
| 🟡 Protected | `protected` | Limited access | From within the class and child classes |
| 🔴 Private | `private` | Highly restricted | Only from within the defining class |

```php
<?php
class Product {
    public $name;       // 🟢 Accessible from anywhere
    protected $price;   // 🟡 Accessible within this class and child classes
    private $cost;      // 🔴 Accessible only within this class
    
    public function __construct($name, $price, $cost) {
        $this->name = $name;
        $this->price = $price;
        $this->cost = $cost;
    }
    
    public function getProfit() {
        // Can access private $cost here
        return $this->price - $this->cost;
    }
}
```

> **❓ Did you know?** Using appropriate visibility helps implement the principle of **encapsulation**, one of the four pillars of OOP, which protects your data and prevents unwanted modifications.

### Example: Access Restrictions in Practice

```php
<?php
$product = new Product("Laptop", 1000, 700);
echo $product->name;    // ✅ Works: "Laptop"
// echo $product->price;  // ❌ Error: Cannot access protected property
// echo $product->cost;   // ❌ Error: Cannot access private property
echo $product->getProfit(); // ✅ Works: 300 (uses the public method)
```

<a id="typed-properties"></a>
## 📝 Typed Properties (PHP 7.4+)

Type declarations for properties ensure that only values of the specified type can be assigned, enhancing code reliability.

```php
<?php
class User {
    public string $name;
    public int $age;
    public ?string $email; // Nullable string (can be string or null)
    public array $roles = []; // With default value
}

$user = new User();
$user->name = "John";    // ✅ OK
$user->age = 30;         // ✅ OK
$user->email = null;     // ✅ OK (nullable)
// $user->age = "thirty"; // ❌ Error: must be of type int
```

> **🔥 Pro Tip:** Using typed properties helps catch bugs early at runtime rather than discovering them through unexpected behavior.

<a id="methods-basics"></a>
## ⚙️ Methods Basics

Methods are functions defined within a class that determine what actions objects can perform.

> **🔍 Real-world analogy:** If properties are a car's characteristics, methods are the actions it can perform: start(), accelerate(), brake(), etc.

```php
<?php
class Calculator {
    // Method declarations
    public function add($a, $b) {
        return $a + $b;
    }
    
    public function subtract($a, $b) {
        return $a - $b;
    }
    
    public function multiply($a, $b) {
        return $a * $b;
    }
    
    public function divide($a, $b) {
        if ($b == 0) {
            return "Cannot divide by zero";
        }
        return $a / $b;
    }
}

$calc = new Calculator();
echo $calc->add(5, 3);      // Output: 8
```

<a id="method-visibility"></a>
## 👁️ Method Visibility

Like properties, methods can also have visibility modifiers to control access.

```php
<?php
class BankAccount {
    private $balance = 0;
    
    // 🟢 Public methods - part of the public interface (API)
    public function deposit($amount) {
        if ($this->validateAmount($amount)) {
            $this->balance += $amount;
            return "Deposited $amount. New balance: {$this->balance}";
        }
        return "Invalid amount.";
    }
    
    public function withdraw($amount) {
        if ($this->validateAmount($amount) && $this->hasSufficientFunds($amount)) {
            $this->balance -= $amount;
            return "Withdrew $amount. New balance: {$this->balance}";
        }
        return "Withdrawal failed.";
    }
    
    // 🟡 Protected method - available to child classes
    protected function hasSufficientFunds($amount) {
        return $this->balance >= $amount;
    }
    
    // 🔴 Private method - only accessible within this class
    private function validateAmount($amount) {
        return is_numeric($amount) && $amount > 0;
    }
}
```

> **💡 Key Point:** Use public methods to create a clean, stable API for your class. Hide implementation details using protected/private methods.

<a id="static-members"></a>
## 🌐 Static Members

Static properties and methods belong to the class itself, not to any specific object instance. They are shared among all instances of the class.

### Static Properties

```php
<?php
class Counter {
    public static $count = 0;
    
    public function __construct() {
        // Increments the shared counter
        self::$count++;
    }
}

$obj1 = new Counter();
echo Counter::$count;  // Output: 1

$obj2 = new Counter();
echo Counter::$count;  // Output: 2

$obj3 = new Counter();
echo Counter::$count;  // Output: 3
```

### Static Methods

```php
<?php
class StringHelper {
    public static function capitalize($string) {
        return ucfirst(strtolower($string));
    }
    
    public static function slugify($string) {
        $string = strtolower($string);
        $string = preg_replace('/[^a-z0-9]+/', '-', $string);
        return trim($string, '-');
    }
}

// Using static methods without creating an object
echo StringHelper::capitalize("hello world");  // Output: "Hello world"
echo StringHelper::slugify("Hello World!");    // Output: "hello-world"
```

> **⚠️ Warning:** Static members cannot access non-static members using `$this` because `$this` refers to an object instance, which static methods don't have.

<a id="return-types"></a>
## 🔍 Method Return Type Declarations

PHP 7.0+ allows you to specify the expected return type of a method:

```php
<?php
class MathOperations {
    // Basic return type
    public function add(float $a, float $b): float {
        return $a + $b;
    }
    
    // Class return type
    public function createPoint(float $x, float $y): Point {
        return new Point($x, $y);
    }
    
    // Nullable return type (PHP 7.1+)
    public function divide(float $a, float $b): ?float {
        if ($b === 0) {
            return null;  // Return null when division by zero
        }
        return $a / $b;
    }
    
    // Void return type (PHP 7.1+)
    public function logOperation(string $operation): void {
        // Log the operation
        // No return value needed
    }
}
```

> **🔥 Pro Tip:** Return types enhance code reliability and make your method signatures more self-documenting.

<a id="class-constants"></a>
## 🔒 Class Constants

Class constants are values that remain unchanged throughout the execution of a script. They are accessed using the scope resolution operator (::).

```php
<?php
class PaymentStatus {
    const PENDING = 'pending';
    const PAID = 'paid';
    const FAILED = 'failed';
    const REFUNDED = 'refunded';
}

class Payment {
    private $amount;
    private $status;
    
    public function __construct($amount) {
        $this->amount = $amount;
        $this->status = PaymentStatus::PENDING;
    }
    
    // Methods to process payment...
}

// Using the constants
echo PaymentStatus::PENDING;  // Output: "pending"

// Constants can be used for comparison
$payment = new Payment(100);
if ($payment->getStatus() === PaymentStatus::PAID) {
    echo "Payment completed!";
}
```

> **💡 Key Point:** Constants provide a way to define fixed values that won't change, making code more maintainable and less error-prone.

<a id="method-parameters"></a>
## 📝 Method Parameters

PHP offers several features for handling method parameters:

### Default Parameter Values

```php
<?php
class MessageFormatter {
    public function format($message, $prefix = "[INFO]", $suffix = "") {
        return "$prefix $message $suffix";
    }
}

$formatter = new MessageFormatter();
echo $formatter->format("Process completed");          // "[INFO] Process completed "
echo $formatter->format("Warning!", "[WARNING]");      // "[WARNING] Warning! "
echo $formatter->format("Error", "[ERROR]", "!!!");    // "[ERROR] Error !!!"
```

### Variable-length Parameter Lists

```php
<?php
class ArrayTools {
    public function joinValues(string $glue, ...$values): string {
        return implode($glue, $values);
    }
}

$tools = new ArrayTools();
echo $tools->joinValues(", ", "apple", "banana", "cherry");  // "apple, banana, cherry"
echo $tools->joinValues("-", "2023", "05", "15");            // "2023-05-15"
```

### Named Arguments (PHP 8.0+)

```php
<?php
class EmailMessage {
    public function send(
        string $to,
        string $subject,
        string $body,
        string $from = 'system@example.com',
        bool $html = false,
        int $priority = 3
    ) {
        // Sending logic here
        return "Email sent to $to with subject: $subject";
    }
}

$mailer = new EmailMessage();

// Without named arguments - must follow parameter order
$mailer->send(
    'john@example.com',
    'Hello',
    'This is a test message',
    'noreply@example.com',
    true,
    1
);

// With named arguments - order doesn't matter and can skip defaults
$mailer->send(
    to: 'john@example.com',
    subject: 'Hello',
    body: 'This is a test message',
    html: true,
    // Using defaults for from and priority
);
```

> **❓ Did you know?** Named arguments are particularly useful for functions with many optional parameters, making code more readable and maintainable.

<a id="constructor-promotion"></a>
## 🎯 Constructor Property Promotion (PHP 8.0+)

PHP 8.0 introduced constructor property promotion, a shorthand syntax that combines property declaration and constructor assignment:

### Before PHP 8.0
```php
<?php
class Product {
    private string $name;
    private float $price;
    private int $quantity;
    
    public function __construct(string $name, float $price, int $quantity) {
        $this->name = $name;
        $this->price = $price;
        $this->quantity = $quantity;
    }
    
    public function getTotalValue(): float {
        return $this->price * $this->quantity;
    }
}
```

### With PHP 8.0 Constructor Promotion
```php
<?php
class Product {
    public function __construct(
        private string $name,
        private float $price,
        private int $quantity
    ) {}
    
    public function getTotalValue(): float {
        return $this->price * $this->quantity;
    }
}
```

> **🔥 Pro Tip:** Constructor promotion reduces boilerplate code, making your classes more concise and maintainable.

<a id="best-practices"></a>
## ✅ Best Practices

1. 📌 **Follow the Single Responsibility Principle:**
   - Each method should do one thing and do it well
   - Break complex methods into smaller, focused ones

2. 🔒 **Use proper visibility:**
   - Make properties private or protected when possible
   - Provide public getter/setter methods for controlled access

3. 📏 **Keep methods small:**
   - Aim for methods that are less than 20 lines
   - Methods should fit on one screen without scrolling

4. 📝 **Use descriptive names:**
   - Methods should have verb-based names that describe their action
   - Property names should clearly describe the data they hold

5. 🔢 **Limit parameters:**
   - Methods with many parameters are hard to use
   - Consider using parameter objects or configuration arrays

6. 🧪 **Write testable code:**
   - Avoid methods that have side effects
   - Design methods to be easily unit-tested

7. 📚 **Document your code:**
   - Use PHPDoc comments to explain the purpose of properties and methods
   - Include type hints, parameter descriptions, and return values

> **Pro Tip:** Think of your classes as services with a clear, well-defined API. Public methods should form a coherent interface, while implementation details are hidden through encapsulation.

---

[Back to Fundamentals](./README.md) | [Previous: Classes and Objects](./01-classes-and-objects.md) | [Next: Encapsulation](./03-encapsulation.md)
