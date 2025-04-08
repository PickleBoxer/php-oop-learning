# 🧠 Abstraction in PHP

> **💡 Pro Tip:** "Abstraction helps you focus on what an object does rather than how it does it, simplifying complex systems by hiding unnecessary details."

## 📋 Table of Contents

| Emoji | Topic | Description |
|:---:|---|---|
| 📘 | [What Is Abstraction](#what-is-abstraction) | Understanding this core OOP concept |
| 🎯 | [Purpose & Benefits](#purpose--benefits) | Why abstraction matters in programming |
| 🔰 | [Abstract Classes](#abstract-classes) | Creating template classes in PHP |
| 🚀 | [Using Abstract Classes](#using-abstract-classes) | How to work with abstract classes |
| 📏 | [Rules for Abstract Classes](#rules-for-abstract-classes) | Guidelines you must follow |
| 🤔 | [When to Use Abstraction](#when-to-use-abstraction) | Scenarios where abstraction is valuable |
| 🌟 | [Real-world Examples](#real-world-examples) | Practical applications of abstraction |
| 📊 | [Abstraction vs. Interface](#abstraction-vs-interface) | Understanding the key differences |
| 📈 | [Abstract vs. Concrete Classes](#abstract-vs-concrete-classes) | Comparing class types |
| ✅ | [Best Practices](#best-practices) | Guidelines for effective abstraction |
| ⚠️ | [Common Pitfalls](#avoiding-common-pitfalls) | Mistakes to avoid with abstraction |
| 🏋️ | [Practice Exercise](#practice-exercise) | Apply abstraction concepts in code |

<a id="what-is-abstraction"></a>
## 📘 What Is Abstraction

Abstraction is one of the four fundamental pillars of Object-Oriented Programming (OOP), alongside encapsulation, inheritance, and polymorphism.

> **🔍 Definition:** Abstraction is the concept of hiding complex implementation details and showing only the necessary features of an object. It allows you to focus on what an object does rather than how it does it.

### Key Aspects of Abstraction

| Aspect | Description |
|--------|-------------|
| 🔍 Focus on Essentials | Present only what's relevant; hide the rest |
| 🧩 Complexity Management | Break down complex systems into simpler components |
| 🛡️ Implementation Hiding | Shield users from internal workings |
| 📏 Creating Models | Represent real-world entities by their essential characteristics |

> **🔍 Real-world analogy:** Think about driving a car. You interact with the steering wheel, pedals, and dashboard (the abstraction), but you don't need to understand how the engine, transmission, or electrical systems work internally (the implementation details). The car's interface abstracts away its complex mechanics.

<a id="purpose--benefits"></a>
## 🎯 Purpose & Benefits

Abstraction serves several important purposes in software development:

### 1. 📉 Simplifies Complexity

Abstraction allows developers to manage complex systems by breaking them into manageable pieces, focusing on one aspect at a time.

```php
// Without abstraction - dealing with all database connection details directly
$connection = mysqli_connect('localhost', 'username', 'password', 'database');
$result = mysqli_query($connection, "SELECT * FROM users");
$users = mysqli_fetch_all($result, MYSQLI_ASSOC);

// With abstraction - complex details hidden behind simple methods
$database = new Database();
$users = $database->getUsers();
```

### 2. 🔄 Enhances Maintainability

When implementation details are hidden, you can modify the internal workings without affecting code that uses the abstraction.

### 3. 🔐 Improves Security

By hiding sensitive implementation details, abstraction can prevent unauthorized access and misuse of critical systems.

### 4. 📦 Promotes Reusability

Well-designed abstractions can be reused across different parts of an application or even different projects.

### 5. 🧪 Facilitates Testing

With proper abstraction, components can be tested in isolation, making unit testing simpler and more effective.

> **❓ Did you know?** Abstraction is closely related to the concept of "information hiding," first introduced by computer scientist David Parnas in 1972. This principle suggests that modules should reveal as little as possible about their inner workings.

<a id="abstract-classes"></a>
## 🔰 Abstract Classes

In PHP, abstraction is primarily implemented through abstract classes and interfaces. An abstract class is a class that cannot be instantiated on its own and is designed to be extended by other classes.

```php
<?php
abstract class Shape {
    protected $color;
    
    public function __construct(string $color) {
        $this->color = $color;
    }
    
    public function getColor(): string {
        return $this->color;
    }
    
    // Abstract method - must be implemented by child classes
    abstract public function calculateArea(): float;
    
    // Regular method - can be inherited as is or overridden
    public function describe(): string {
        return "A {$this->color} shape with an area of {$this->calculateArea()} square units.";
    }
}

class Circle extends Shape {
    private $radius;
    
    public function __construct(string $color, float $radius) {
        parent::__construct($color);
        $this->radius = $radius;
    }
    
    // Implementation of the abstract method
    public function calculateArea(): float {
        return pi() * $this->radius * $this->radius;
    }
    
    // Additional method specific to Circle
    public function calculateCircumference(): float {
        return 2 * pi() * $this->radius;
    }
}

class Rectangle extends Shape {
    private $width;
    private $height;
    
    public function __construct(string $color, float $width, float $height) {
        parent::__construct($color);
        $this->width = $width;
        $this->height = $height;
    }
    
    // Implementation of the abstract method
    public function calculateArea(): float {
        return $this->width * $this->height;
    }
    
    // Additional method specific to Rectangle
    public function calculatePerimeter(): float {
        return 2 * ($this->width + $this->height);
    }
}
```

> **💡 Key Point:** Abstract classes provide a blueprint for other classes, enforcing a specific structure while allowing for specialized implementations in child classes.

<a id="using-abstract-classes"></a>
## 🚀 Using Abstract Classes

Since abstract classes cannot be instantiated directly, you work with them by creating and using concrete child classes:

```php
<?php
// $shape = new Shape("red"); // Error: Cannot instantiate abstract class

// Create concrete instances from classes that extend the abstract class
$circle = new Circle("blue", 5);
$rectangle = new Rectangle("green", 4, 6);

// Use methods from the abstract parent class
echo $circle->getColor();     // "blue"
echo $rectangle->getColor();  // "green"

// Use overridden/implemented abstract methods
echo $circle->calculateArea();     // 78.54 (π * 5²)
echo $rectangle->calculateArea();  // 24 (4 * 6)

// Use the regular (non-abstract) methods from the parent
echo $circle->describe();     // "A blue shape with an area of 78.54 square units."
echo $rectangle->describe();  // "A green shape with an area of 24 square units."

// Use methods specific to concrete classes
echo $circle->calculateCircumference();  // 31.42 (2π * 5)
echo $rectangle->calculatePerimeter();   // 20 (2 * (4 + 6))

// Polymorphism with abstract classes
function printShapeInfo(Shape $shape): void {
    echo "Color: " . $shape->getColor() . "\n";
    echo "Area: " . $shape->calculateArea() . "\n";
    echo "Description: " . $shape->describe() . "\n";
}

// Works with any Shape subclass
printShapeInfo($circle);
printShapeInfo($rectangle);
```

> **🔥 Pro Tip:** Abstract classes are excellent for creating a "template method pattern" where you define the skeleton of an algorithm in a method in the abstract class, deferring some steps to subclasses.

<a id="rules-for-abstract-classes"></a>
## 📏 Rules for Abstract Classes

When working with abstract classes in PHP, you must follow these rules:

| Rule | Description | Example |
|------|-------------|---------|
| ❌ **No Direct Instantiation** | You cannot create objects directly from an abstract class | `$shape = new Shape();  // Error` |
| ✅ **Can Contain Abstract Methods** | Methods declared as abstract must be implemented by child classes | `abstract public function draw();` |
| ✅ **Can Contain Concrete Methods** | Regular methods with implementations | `public function getColor() { return $this->color; }` |
| ✅ **Can Contain Properties** | Both static and instance properties | `protected $color;` |
| 🔄 **Child Classes Must Implement** | Non-abstract child classes must implement all abstract methods | `public function draw() { /* implementation */ }` |
| 🔀 **Abstract Child Classes** | Abstract child classes can defer implementation to their children | `abstract class Shape3D extends Shape { /* ... */ }` |

> **⚠️ Note:** An abstract method cannot have a body in the abstract class. The implementation is provided by the child classes.

<a id="when-to-use-abstraction"></a>
## 🤔 When to Use Abstraction

Use abstract classes in the following situations:

### 1. 📚 Common Functionality

When several related classes share common behavior:

```php
abstract class DatabaseConnection {
    protected $connection;
    protected $host;
    protected $username;
    protected $password;
    
    // Common connection logic
    public function connect() {
        // Base connection setup
    }
    
    // Force child classes to implement specific database operations
    abstract protected function executeQuery($query);
}
```

### 2. 🧩 Partial Implementation

When you want to provide some implemented methods and leave others to child classes:

```php
abstract class FormValidator {
    // Common validation logic
    public function validateRequired($value) {
        return !empty($value);
    }
    
    public function validateEmail($email) {
        return filter_var($email, FILTER_VALIDATE_EMAIL) !== false;
    }
    
    // Child classes must implement specific validation logic
    abstract public function validate($data);
}
```

### 3. 🎯 Enforced Structure

When you want to enforce a common structure across multiple classes:

```php
abstract class ApiController {
    // All API controllers must implement these methods
    abstract public function index();
    abstract public function store($data);
    abstract public function show($id);
    abstract public function update($id, $data);
    abstract public function destroy($id);
}
```

### 4. 🧬 Type Hierarchies

When you want to establish an "is-a" relationship and ensure type safety:

```php
abstract class Animal {
    abstract public function makeSound();
}

class Dog extends Animal {
    public function makeSound() {
        return "Woof!";
    }
}

// Type safety - function expects any Animal
function hearAnimal(Animal $animal) {
    echo $animal->makeSound();
}
```

> **❓ Did you know?** Abstract classes bridge the gap between interfaces (which are 100% abstract) and concrete classes (which are 0% abstract). They allow you to specify some behavior while leaving other parts to be defined by child classes.

<a id="real-world-examples"></a>
## 🌟 Real-world Examples

### Example: Payment Processing System

```php
<?php
abstract class PaymentProcessor {
    protected $amount;
    protected $currency;
    protected $transactionId;
    
    public function __construct(float $amount, string $currency) {
        $this->amount = $amount;
        $this->currency = $currency;
        $this->transactionId = $this->generateTransactionId();
    }
    
    // Common method for all payment processors
    protected function generateTransactionId(): string {
        return uniqid('TRANS_');
    }
    
    public function getTransactionId(): string {
        return $this->transactionId;
    }
    
    // Common validation logic
    protected function validateAmount(): bool {
        return $this->amount > 0;
    }
    
    // Methods that must be implemented by specific payment processors
    abstract public function processPayment(): bool;
    abstract public function refundPayment(): bool;
    
    // Template method pattern - defines the skeleton of the payment flow
    public function pay(): array {
        // Pre-processing
        if (!$this->validateAmount()) {
            return [
                'success' => false,
                'error' => 'Invalid amount'
            ];
        }
        
        // Process payment (implementation varies by processor)
        $result = $this->processPayment();
        
        // Post-processing
        return [
            'success' => $result,
            'transaction_id' => $this->transactionId,
            'amount' => $this->amount,
            'currency' => $this->currency,
            'timestamp' => date('Y-m-d H:i:s')
        ];
    }
}

class CreditCardProcessor extends PaymentProcessor {
    private $cardNumber;
    private $expiryDate;
    private $cvv;
    
    public function __construct(float $amount, string $currency, string $cardNumber, string $expiryDate, string $cvv) {
        parent::__construct($amount, $currency);
        $this->cardNumber = $cardNumber;
        $this->expiryDate = $expiryDate;
        $this->cvv = $cvv;
    }
    
    protected function validateCreditCard(): bool {
        // Basic validation for demonstration
        $isValidNumber = strlen(str_replace(' ', '', $this->cardNumber)) === 16;
        $isValidExpiry = preg_match('/^\d{2}\/\d{2}$/', $this->expiryDate);
        $isValidCvv = strlen($this->cvv) === 3;
        
        return $isValidNumber && $isValidExpiry && $isValidCvv;
    }
    
    public function processPayment(): bool {
        if (!$this->validateCreditCard()) {
            return false;
        }
        
        // Credit card payment gateway integration
        // This is simplified for demonstration
        echo "Processing credit card payment of {$this->amount} {$this->currency}\n";
        return true; // Simulate successful payment
    }
    
    public function refundPayment(): bool {
        // Credit card refund process
        echo "Refunding credit card payment {$this->transactionId}\n";
        return true; // Simulate successful refund
    }
}

class PayPalProcessor extends PaymentProcessor {
    private $email;
    private $password;
    
    public function __construct(float $amount, string $currency, string $email, string $password) {
        parent::__construct($amount, $currency);
        $this->email = $email;
        $this->password = $password;
    }
    
    protected function authenticate(): bool {
        // PayPal authentication logic
        return !empty($this->email) && !empty($this->password);
    }
    
    public function processPayment(): bool {
        if (!$this->authenticate()) {
            return false;
        }
        
        // PayPal API integration
        echo "Processing PayPal payment of {$this->amount} {$this->currency}\n";
        return true; // Simulate successful payment
    }
    
    public function refundPayment(): bool {
        if (!$this->authenticate()) {
            return false;
        }
        
        // PayPal refund process
        echo "Refunding PayPal payment {$this->transactionId}\n";
        return true; // Simulate successful refund
    }
}

// Usage
$creditCardPayment = new CreditCardProcessor(99.99, 'USD', '4111111111111111', '12/25', '123');
$result = $creditCardPayment->pay();
print_r($result);

$payPalPayment = new PayPalProcessor(49.99, 'EUR', 'customer@example.com', 'secure_password');
$result = $payPalPayment->pay();
print_r($result);
```

> **🔥 Pro Tip:** Notice how the `pay()` method in the abstract class defines the overall algorithm, but delegates specific steps to child classes. This is the Template Method pattern, one of the most powerful uses of abstraction.

<a id="abstraction-vs-interface"></a>
## 📊 Abstraction vs. Interface

While both abstract classes and interfaces enable abstraction, they serve different purposes and have key differences:

| Feature | Abstract Class | Interface |
|---------|---------------|-----------|
| **Implementation** | Can contain both implemented and abstract methods | Contains only method signatures (PHP 8.0+ allows default methods) |
| **Inheritance** | A class can extend only one abstract class | A class can implement multiple interfaces |
| **Constructor** | Can have a constructor | Cannot have a constructor |
| **Properties** | Can have properties with any visibility | Can only have constants (public by default) |
| **Method Visibility** | Can have public, protected, and private methods | All methods are public |
| **Use Case** | When you want to share code among closely related classes | When you want to define a contract for unrelated classes |

### When to Use Abstract Classes vs. Interfaces

```php
// Use abstract classes when:
abstract class Animal {
    // Shared implementation
    protected $age;
    
    public function setAge($age) {
        $this->age = $age;
    }
    
    public function getAge() {
        return $this->age;
    }
    
    // Abstract methods for customization
    abstract public function makeSound();
}

// Use interfaces when:
interface Loggable {
    public function log($message);
    public function getLogHistory();
}

// Different classes can implement the same interface
class User implements Loggable {
    // Implementation
}

class Order implements Loggable {
    // Implementation
}

class Product implements Loggable {
    // Implementation
}
```

> **💡 Key Insight:** Use abstract classes when you want to share code among related classes. Use interfaces when you want to define a contract that unrelated classes can fulfill.

<a id="abstract-vs-concrete-classes"></a>
## 📈 Abstract vs. Concrete Classes

Understanding the differences between abstract and concrete classes is crucial for effective OOP design:

| Feature | Abstract Class | Concrete Class |
|---------|---------------|---------------|
| **Instantiation** | Cannot be instantiated directly | Can be instantiated directly |
| **Methods** | Can contain both abstract and concrete methods | Contains only concrete methods |
| **Purpose** | Provides a template for other classes | Provides a complete implementation |
| **Focus** | Defines what subclasses should be like | Defines what objects will be like |
| **Completeness** | Usually incomplete; requires extension | Complete and ready to use |

```php
// Abstract class - cannot create objects directly
abstract class Vehicle {
    abstract public function start();
}

// Concrete class - can create objects
class Car extends Vehicle {
    public function start() {
        return "Car engine started";
    }
}

$myCar = new Car(); // Works
// $myVehicle = new Vehicle(); // Error: Cannot instantiate abstract class
```

> **❓ Did you know?** You can think of an abstract class as a "partially completed" class. It has some implemented parts, but other parts are deliberately left incomplete for child classes to fill in.

<a id="best-practices"></a>
## ✅ Best Practices

Follow these guidelines to use abstraction effectively in your PHP code:

### 1. 🎯 Keep It Focused

Make abstract classes focused on a specific concern or responsibility.

```php
// Good: Focused abstract class
abstract class Logger {
    // Methods related to logging only
}

// Not good: Too many responsibilities
abstract class FileHandler {
    // Methods for logging
    // Methods for file uploads
    // Methods for image processing
    // Methods for PDF generation
}
```

### 2. 📝 Use Meaningful Names

Choose names that clearly indicate the purpose or role of your abstract class.

```php
// Good: Clear name indicating purpose
abstract class HttpClient { }

// Not good: Vague name
abstract class Manager { }
```

### 3. 📚 Document Requirements

Clearly document what child classes need to implement.

```php
/**
 * Abstract repository for database operations.
 * 
 * Child classes must implement:
 * - getConnection(): Returns a database connection
 * - getTableName(): Returns the name of the table
 */
abstract class Repository {
    abstract protected function getConnection();
    abstract protected function getTableName();
}
```

### 4. 🎭 Don't Over-Abstract

Only make methods abstract if different implementations are truly needed.

```php
abstract class Vehicle {
    // Common to all vehicles, no need to be abstract
    public function registerVehicle($id) {
        // Standard registration process
    }
    
    // Need different implementations, should be abstract
    abstract public function calculateFuelEfficiency();
}
```

### 5. ⚖️ Balance Abstract and Concrete

Find a good balance between abstract methods (to be implemented by children) and concrete methods (shared functionality).

```php
abstract class FormValidator {
    // Shared concrete methods
    public function validateEmail($email) { /* ... */ }
    public function validatePhone($phone) { /* ... */ }
    
    // Abstract methods that need custom implementation
    abstract public function getValidationRules();
    abstract public function displayErrors();
}
```

### 6. 🔍 Provide Default Implementations Where Possible

Consider providing default implementations that can be overridden if needed.

```php
abstract class Controller {
    // Default implementation
    public function respondWithJson($data) {
        header('Content-Type: application/json');
        echo json_encode($data);
    }
    
    // Must be implemented
    abstract public function handle();
}
```

### 7. 🧬 Use Abstract Classes in a Hierarchy

Abstract classes work well as intermediate classes in inheritance hierarchies.

```php
abstract class Animal { /* ... */ }

abstract class Mammal extends Animal { /* ... */ }

class Dog extends Mammal { /* ... */ }
```

<a id="avoiding-common-pitfalls"></a>
## ⚠️ Avoiding Common Pitfalls

Watch out for these common mistakes when working with abstract classes:

### 1. 🔄 Excessive Abstraction

Don't create abstractions without a clear reason.

```php
// Unnecessary abstraction
abstract class User {
    public function getUsername() { /* ... */ }
    public function getEmail() { /* ... */ }
    // No abstract methods or specialized behavior
}

// Better as a concrete class unless there's a specific need for abstraction
```

### 2. 📊 Too Deep Hierarchies

Avoid creating deep inheritance chains with multiple levels of abstract classes.

```php
// Too deep and complex
abstract class Entity { /* ... */ }
abstract class Person extends Entity { /* ... */ }
abstract class Employee extends Person { /* ... */ }
abstract class Manager extends Employee { /* ... */ }
class DepartmentManager extends Manager { /* ... */ }

// Consider flattening the hierarchy or using composition
```

### 3. 🔀 Mixing Concerns

Don't put unrelated functionality in the same abstract class.

```php
// Mixing concerns
abstract class UserController {
    // User-related methods
    abstract public function getUser();
    
    // File-related methods
    abstract public function uploadFile();
    
    // Email-related methods
    abstract public function sendEmail();
}

// Better: Separate concerns
abstract class UserController { /* ... */ }
abstract class FileController { /* ... */ }
interface EmailSender { /* ... */ }
```

### 4. 📝 Missing Documentation

Always document what child classes need to implement.

```php
// Good
/**
 * @method string getTableName() Returns the database table name
 * @method array getFieldMappings() Returns field to column mappings
 */
abstract class Model { /* ... */ }
```

### 5. 🧩 Abstract vs. Interface Confusion

Use the right tool for the job:

```php
// If no shared implementation is needed, use an interface
interface Logger {
    public function log($message);
}

// If shared implementation exists, use an abstract class
abstract class DatabaseModel {
    // Shared implementation
    public function save() { /* ... */ }
    
    // Abstract methods
    abstract public function getTableName();
}
```

<a id="practice-exercise"></a>
## 🏋️ Practice Exercise

Create an abstraction-based content management system with:

1. An abstract `Content` class that:
   - Has properties for `title`, `author`, and `createdAt`
   - Has an abstract `render()` method
   - Has a concrete `getMetadata()` method
   
2. Create three concrete content types:
   - `Article` with a body text property
   - `Image` with image URL and alt text properties
   - `Video` with video URL and duration properties
   
3. Each should implement the `render()` method differently

**Sample Solution:**

```php
<?php
abstract class Content {
    protected $title;
    protected $author;
    protected $createdAt;
    
    public function __construct(string $title, string $author) {
        $this->title = $title;
        $this->author = $author;
        $this->createdAt = new DateTime();
    }
    
    // Concrete method - shared by all content types
    public function getMetadata(): array {
        return [
            'title' => $this->title,
            'author' => $this->author,
            'created' => $this->createdAt->format('Y-m-d H:i:s'),
            'type' => static::getType()
        ];
    }
    
    // Abstract methods - must be implemented by child classes
    abstract public function render(): string;
    abstract public static function getType(): string;
}

class Article extends Content {
    private $body;
    
    public function __construct(string $title, string $author, string $body) {
        parent::__construct($title, $author);
        $this->body = $body;
    }
    
    public function render(): string {
        return "<article>
                  <h1>{$this->title}</h1>
                  <p class='meta'>By {$this->author} on {$this->createdAt->format('F j, Y')}</p>
                  <div class='content'>{$this->body}</div>
                </article>";
    }
    
    public static function getType(): string {
        return 'article';
    }
}

class Image extends Content {
    private $imageUrl;
    private $altText;
    
    public function __construct(string $title, string $author, string $imageUrl, string $altText) {
        parent::__construct($title, $author);
        $this->imageUrl = $imageUrl;
        $this->altText = $altText;
    }
    
    public function render(): string {
        return "<figure>
                  <img src='{$this->imageUrl}' alt='{$this->altText}' />
                  <figcaption>{$this->title} by {$this->author}</figcaption>
                </figure>";
    }
    
    public static function getType(): string {
        return 'image';
    }
}

class Video extends Content {
    private $videoUrl;
    private $duration;
    
    public function __construct(string $title, string $author, string $videoUrl, int $duration) {
        parent::__construct($title, $author);
        $this->videoUrl = $videoUrl;
        $this->duration = $duration;
    }
    
    public function render(): string {
        $minutes = floor($this->duration / 60);
        $seconds = $this->duration % 60;
        
        return "<div class='video'>
                  <h2>{$this->title}</h2>
                  <p>By {$this->author} • {$minutes}:{$seconds}</p>
                  <video src='{$this->videoUrl}' controls></video>
                </div>";
    }
    
    public static function getType(): string {
        return 'video';
    }
}

// Usage
$article = new Article(
    "Understanding Abstraction in PHP",
    "Jane Developer",
    "<p>Abstraction is an important OOP concept that...</p>"
);

$image = new Image(
    "Sunset at the Beach",
    "John Photographer",
    "sunset.jpg",
    "A beautiful sunset over the ocean"
);

$video = new Video(
    "PHP Tutorial",
    "Code Teacher",
    "php-tutorial.mp4",
    360 // 6 minutes
);

// Content management system
function displayContent(Content $content) {
    $metadata = $content->getMetadata();
    echo "Rendering {$metadata['type']} content...\n";
    echo $content->render() . "\n\n";
}

displayContent($article);
displayContent($image);
displayContent($video);

// Creating a content feed
$contentFeed = [$article, $image, $video];

foreach ($contentFeed as $content) {
    displayContent($content);
}
```

> **🌟 Key Takeaway:** Abstraction through abstract classes helps you create flexible yet structured systems. It lets you enforce common behavior while allowing for specialized implementations.

---

[Back to Fundamentals](./README.md) | [Previous: Polymorphism](./05-polymorphism.md) | [Next: Interfaces](./07-interfaces.md)
