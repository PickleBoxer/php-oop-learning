# 🔄 Polymorphism in PHP

> **💡 Pro Tip:** "Polymorphism allows you to write flexible code that can work with objects of different classes without knowing their specific types."

## 📋 Table of Contents

| Emoji | Topic | Description |
|:---:|---|---|
| 📘 | [What Is Polymorphism](#what-is-polymorphism) | Understanding this fundamental OOP concept |
| 🧩 | [Types of Polymorphism](#types-of-polymorphism) | Different ways to implement polymorphism in PHP |
| 🔀 | [Method Overriding](#method-overriding) | Runtime polymorphism through inheritance |
| 🔌 | [Interface-based Polymorphism](#interface-based-polymorphism) | Achieving polymorphism through common interfaces |
| 🚀 | [Polymorphism in Action](#polymorphism-in-action) | Practical examples showing polymorphic behavior |
| 📊 | [Type Hinting](#type-hinting) | Using PHP type declarations with polymorphism |
| 🛠️ | [Implementing Polymorphism](#implementing-polymorphism) | Different techniques for polymorphic code |
| 🌟 | [Benefits of Polymorphism](#benefits-of-polymorphism) | Why polymorphism matters in OOP |
| ✅ | [Best Practices](#best-practices) | Guidelines for effective polymorphism |
| 🏋️ | [Practice Exercise](#practice-exercise) | Apply polymorphism concepts in a practical example |

<a id="what-is-polymorphism"></a>
## 📘 What Is Polymorphism

Polymorphism is one of the four fundamental pillars of Object-Oriented Programming (OOP). The word comes from Greek, meaning "many forms" (poly = many, morph = form).

> **🔍 Definition:** Polymorphism is the ability to treat objects of different classes that share a common interface in the same way. It allows methods to do different things based on the object they're acting upon.

### Core Concept

Polymorphism enables you to:
- Process objects differently depending on their data type or class
- Use a single interface to represent different underlying forms (classes)
- Call the same method on different objects and get different results

> **🔍 Real-world analogy:** Consider a universal remote control. It has a "power" button that works on TVs, DVD players, and stereos. The button is the same (common interface), but the actual effect depends on which device you're controlling (different implementations).

### Key Benefits of Polymorphism

| Benefit | Description |
|---------|-------------|
| 🔄 Flexibility | Write code that works with current and future classes |
| 📦 Reusability | Create methods that can handle different object types |
| 🧱 Modularity | Build interchangeable components with standard interfaces |
| 🔧 Maintainability | Modify implementations without changing the calling code |

<a id="types-of-polymorphism"></a>
## 🧩 Types of Polymorphism in PHP

PHP supports several types of polymorphism:

### 1. Method Overriding (Runtime Polymorphism)

This occurs when a child class provides a specific implementation for a method already defined in its parent class.

### 2. Interface-based Polymorphism

This happens when multiple classes implement the same interface, providing different implementations of the same methods.

### 3. Ad-hoc Polymorphism (Function/Operator Overloading)

PHP doesn't directly support function overloading, but similar behavior can be achieved using:
- Variable arguments with `func_get_args()`
- PHP 8's union types and named arguments

> **❓ Did you know?** While many programming languages support function overloading (same function name with different parameter types), PHP doesn't directly support this. PHP 8's union types and named parameters provide an alternative approach.

<a id="method-overriding"></a>
## 🔀 Method Overriding (Runtime Polymorphism)

Method overriding is the most common form of polymorphism in PHP. It occurs when a child class provides its own implementation of a method defined in a parent class.

```php
<?php
abstract class Shape {
    protected $name;
    
    public function __construct($name) {
        $this->name = $name;
    }
    
    // Method to be overridden by child classes
    abstract public function calculateArea();
    
    public function getName() {
        return $this->name;
    }
    
    public function getDescription() {
        return "This is a {$this->name} with area: {$this->calculateArea()}";
    }
}

class Circle extends Shape {
    private $radius;
    
    public function __construct($name, $radius) {
        parent::__construct($name);
        $this->radius = $radius;
    }
    
    // Implementation specific to Circle
    public function calculateArea() {
        return pi() * $this->radius * $this->radius;
    }
    
    // Additional Circle-specific method
    public function getDiameter() {
        return $this->radius * 2;
    }
}

class Rectangle extends Shape {
    private $width;
    private $height;
    
    public function __construct($name, $width, $height) {
        parent::__construct($name);
        $this->width = $width;
        $this->height = $height;
    }
    
    // Implementation specific to Rectangle
    public function calculateArea() {
        return $this->width * $this->height;
    }
    
    // Additional Rectangle-specific method
    public function getPerimeter() {
        return 2 * ($this->width + $this->height);
    }
}
```

> **🔥 Pro Tip:** Abstract methods in parent classes force child classes to implement their own versions, ensuring polymorphic behavior while maintaining a consistent interface.

<a id="interface-based-polymorphism"></a>
## 🔌 Interface-based Polymorphism

Interfaces provide a powerful way to achieve polymorphism in PHP, especially between classes that don't share an inheritance relationship.

```php
<?php
interface Drawable {
    public function draw();
    public function getPosition();
}

class Circle implements Drawable {
    private $radius;
    private $x;
    private $y;
    
    public function __construct($x, $y, $radius) {
        $this->x = $x;
        $this->y = $y;
        $this->radius = $radius;
    }
    
    public function draw() {
        return "Drawing a circle with radius {$this->radius} at ({$this->x}, {$this->y})";
    }
    
    public function getPosition() {
        return ["x" => $this->x, "y" => $this->y];
    }
    
    // Circle-specific method
    public function getRadius() {
        return $this->radius;
    }
}

class TextBox implements Drawable {
    private $text;
    private $x;
    private $y;
    
    public function __construct($x, $y, $text) {
        $this->x = $x;
        $this->y = $y;
        $this->text = $text;
    }
    
    public function draw() {
        return "Drawing text '{$this->text}' at ({$this->x}, {$this->y})";
    }
    
    public function getPosition() {
        return ["x" => $this->x, "y" => $this->y];
    }
    
    // TextBox-specific method
    public function getText() {
        return $this->text;
    }
}

// Polymorphic function that works with any Drawable object
function renderShape(Drawable $shape) {
    echo $shape->draw() . "\n";
    $position = $shape->getPosition();
    echo "Position: ({$position['x']}, {$position['y']})\n";
}

// Usage
$circle = new Circle(10, 20, 5);
$textBox = new TextBox(30, 40, "Hello World");

renderShape($circle);   // Works with Circle
renderShape($textBox);  // Works with TextBox
```

> **💡 Key Point:** With interface-based polymorphism, even completely different classes can be used interchangeably as long as they implement the required interface methods.

<a id="polymorphism-in-action"></a>
## 🚀 Polymorphism in Action

Let's see a practical example of polymorphism with a simple payment processing system:

```php
<?php
interface PaymentMethod {
    public function processPayment($amount);
    public function getPaymentDetails();
}

class CreditCardPayment implements PaymentMethod {
    private $cardNumber;
    private $expiryDate;
    
    public function __construct($cardNumber, $expiryDate) {
        $this->cardNumber = $cardNumber;
        $this->expiryDate = $expiryDate;
    }
    
    public function processPayment($amount) {
        // Credit card-specific processing logic
        $last4 = substr($this->cardNumber, -4);
        return "Processed \${$amount} payment via Credit Card ending in {$last4}";
    }
    
    public function getPaymentDetails() {
        return "Credit Card ending in " . substr($this->cardNumber, -4);
    }
}

class PayPalPayment implements PaymentMethod {
    private $email;
    
    public function __construct($email) {
        $this->email = $email;
    }
    
    public function processPayment($amount) {
        // PayPal-specific processing logic
        return "Processed \${$amount} payment via PayPal account: {$this->email}";
    }
    
    public function getPaymentDetails() {
        return "PayPal account: {$this->email}";
    }
}

class BankTransferPayment implements PaymentMethod {
    private $accountNumber;
    private $bankCode;
    
    public function __construct($accountNumber, $bankCode) {
        $this->accountNumber = $accountNumber;
        $this->bankCode = $bankCode;
    }
    
    public function processPayment($amount) {
        // Bank transfer-specific processing logic
        return "Processed \${$amount} payment via Bank Transfer to account: {$this->accountNumber}";
    }
    
    public function getPaymentDetails() {
        return "Bank account: {$this->bankCode}-{$this->accountNumber}";
    }
}

// Polymorphic checkout function that works with any payment method
function processCheckout(PaymentMethod $paymentMethod, $amount) {
    echo "Starting checkout process...\n";
    echo "Selected payment method: " . $paymentMethod->getPaymentDetails() . "\n";
    echo $paymentMethod->processPayment($amount) . "\n";
    echo "Checkout complete!\n";
}

// Usage with different payment methods
$creditCard = new CreditCardPayment("4111111111111111", "12/2025");
$payPal = new PayPalPayment("customer@example.com");
$bankTransfer = new BankTransferPayment("12345678", "BANKCODE123");

// Same function works with all payment methods
processCheckout($creditCard, 99.99);
processCheckout($payPal, 59.99);
processCheckout($bankTransfer, 149.99);
```

> **❓ Did you know?** Many frameworks and libraries rely heavily on polymorphism to provide plugin systems, where custom code can be integrated seamlessly into the main application flow.

<a id="type-hinting"></a>
## 📊 Type Hinting

PHP's type hinting system is crucial for implementing polymorphism effectively. It allows you to specify that a function parameter must be an instance of a specific class or interface.

```php
<?php
// Type hinting with a class
function processShape(Shape $shape) {
    echo $shape->getDescription();
}

// Type hinting with an interface
function renderElement(Drawable $element) {
    echo $element->draw();
}

// Type hinting with union types (PHP 8.0+)
function processItem(Shape|Drawable $item) {
    // Works with either Shape instances or Drawable instances
    echo "Processing item";
}
```

### Union Types for Enhanced Polymorphism (PHP 8.0+)

PHP 8.0 introduced union types, which allow a parameter or return type to accept values of multiple different types:

```php
<?php
class FileLogger {
    public function log(string $message): void {
        file_put_contents('app.log', $message . PHP_EOL, FILE_APPEND);
    }
}

class DatabaseLogger {
    public function log(string $message): void {
        // Store log in database
    }
}

// Function that accepts multiple logger types
function logMessage(FileLogger|DatabaseLogger $logger, string $message): void {
    $logger->log("[" . date('Y-m-d H:i:s') . "] $message");
}

// Even better with interfaces
interface Logger {
    public function log(string $message): void;
}

// Both logger classes would implement the Logger interface
// Then we could simply use:
function logMessage(Logger $logger, string $message): void {
    $logger->log("[" . date('Y-m-d H:i:s') . "] $message");
}
```

<a id="implementing-polymorphism"></a>
## 🛠️ Implementing Polymorphism

There are several ways to implement polymorphic behavior in PHP:

### 1. Using Abstract Classes

Abstract classes define a template with some implemented methods and some abstract methods that must be implemented by child classes.

```php
<?php
abstract class NotificationSender {
    protected $recipient;
    
    public function __construct($recipient) {
        $this->recipient = $recipient;
    }
    
    // Common method shared by all notification types
    public function validateRecipient() {
        // Common validation logic
        return !empty($this->recipient);
    }
    
    // Method that must be implemented by all child classes
    abstract public function send($message);
    
    // Template method pattern
    public function deliverNotification($message) {
        if ($this->validateRecipient()) {
            return $this->send($message);
        }
        return "Invalid recipient";
    }
}

class EmailNotification extends NotificationSender {
    public function send($message) {
        // Email-specific sending logic
        return "Email sent to {$this->recipient}: $message";
    }
}

class SMSNotification extends NotificationSender {
    public function send($message) {
        // SMS-specific sending logic
        return "SMS sent to {$this->recipient}: $message";
    }
}

class PushNotification extends NotificationSender {
    private $device;
    
    public function __construct($recipient, $device) {
        parent::__construct($recipient);
        $this->device = $device;
    }
    
    public function send($message) {
        // Push notification-specific logic
        return "Push notification sent to {$this->recipient}'s {$this->device}: $message";
    }
}
```

### 2. Using Interfaces

Interfaces define a contract that classes must fulfill, without specifying any implementation details.

```php
<?php
interface Exportable {
    public function export(): string;
    public function getFormat(): string;
}

class PDFExport implements Exportable {
    private $data;
    
    public function __construct($data) {
        $this->data = $data;
    }
    
    public function export(): string {
        // PDF export logic
        return "Exporting data to PDF format...";
    }
    
    public function getFormat(): string {
        return "PDF";
    }
}

class CSVExport implements Exportable {
    private $data;
    
    public function __construct($data) {
        $this->data = $data;
    }
    
    public function export(): string {
        // CSV export logic
        return "Exporting data to CSV format...";
    }
    
    public function getFormat(): string {
        return "CSV";
    }
}

class JSONExport implements Exportable {
    private $data;
    
    public function __construct($data) {
        $this->data = $data;
    }
    
    public function export(): string {
        // JSON export logic
        return "Exporting data to JSON format...";
    }
    
    public function getFormat(): string {
        return "JSON";
    }
}

// Polymorphic function
function exportData(Exportable $exporter) {
    echo "Starting export in " . $exporter->getFormat() . " format...\n";
    echo $exporter->export() . "\n";
    echo "Export complete!\n";
}

// Usage
$pdfExporter = new PDFExport(['data' => 'sample']);
$csvExporter = new CSVExport(['data' => 'sample']);
$jsonExporter = new JSONExport(['data' => 'sample']);

exportData($pdfExporter);
exportData($csvExporter);
exportData($jsonExporter);
```

### 3. Using Traits (Mixin-style Polymorphism)

Traits allow code reuse in multiple classes without traditional inheritance.

```php
<?php
trait Loggable {
    public function log($message) {
        echo "[LOG] " . get_class($this) . ": $message\n";
    }
}

trait Serializable {
    public function serialize() {
        return serialize(get_object_vars($this));
    }
    
    public function unserialize($data) {
        $vars = unserialize($data);
        foreach ($vars as $key => $value) {
            $this->$key = $value;
        }
    }
}

// Classes can use multiple traits
class User {
    use Loggable, Serializable;
    
    private $username;
    
    public function __construct($username) {
        $this->username = $username;
        $this->log("User created: $username");
    }
}

class Product {
    use Loggable, Serializable;
    
    private $name;
    private $price;
    
    public function __construct($name, $price) {
        $this->name = $name;
        $this->price = $price;
        $this->log("Product created: $name at \$$price");
    }
}

// Usage
$user = new User("john_doe");
$product = new Product("Laptop", 999.99);

// Both classes can use methods from the traits
$userData = $user->serialize();
$productData = $product->serialize();
```

<a id="benefits-of-polymorphism"></a>
## 🌟 Benefits of Polymorphism

Polymorphism provides several key benefits to your object-oriented code:

### 1. 🔌 Extensibility

New classes can be added to your system without changing existing code:

```php
// Existing code processes any Shape
function calculateTotalArea(array $shapes) {
    $total = 0;
    foreach ($shapes as $shape) {
        $total += $shape->calculateArea();
    }
    return $total;
}

// Later, you can add a new shape type without modifying the function
class Triangle extends Shape {
    private $base;
    private $height;
    
    public function __construct($name, $base, $height) {
        parent::__construct($name);
        $this->base = $base;
        $this->height = $height;
    }
    
    public function calculateArea() {
        return 0.5 * $this->base * $this->height;
    }
}
```

### 2. 🧩 Modularity

Polymorphism allows components to be more independent and interchangeable:

```php
interface DatabaseConnection {
    public function connect();
    public function query($sql);
    public function close();
}

// Multiple implementations can be swapped out as needed
class MySQLConnection implements DatabaseConnection { /* ... */ }
class PostgreSQLConnection implements DatabaseConnection { /* ... */ }
class SQLiteConnection implements DatabaseConnection { /* ... */ }

// Application code doesn't need to change when the database changes
function runReport(DatabaseConnection $db, $reportType) {
    $db->connect();
    // Process report using connection
    $db->close();
}
```

### 3. 📐 Cleaner Architecture

Polymorphism enables cleaner designs with reduced coupling:

```php
// Without polymorphism - tightly coupled with conditional logic
function processPayment($type, $amount, $data) {
    if ($type === 'credit_card') {
        // Credit card-specific code
    } elseif ($type === 'paypal') {
        // PayPal-specific code
    } elseif ($type === 'bank_transfer') {
        // Bank transfer-specific code
    } else {
        throw new Exception("Unknown payment type");
    }
}

// With polymorphism - loosely coupled, no conditional logic
function processPayment(PaymentMethod $method, $amount) {
    return $method->processPayment($amount);
}
```

### 4. 🧪 Testability

Polymorphic code is often easier to test because dependencies can be replaced with test doubles:

```php
interface EmailService {
    public function sendEmail($to, $subject, $body);
}

class OrderProcessor {
    private $emailService;
    
    public function __construct(EmailService $emailService) {
        $this->emailService = $emailService;
    }
    
    public function processOrder($order) {
        // Process order
        $this->emailService->sendEmail(
            $order->getCustomerEmail(),
            'Order Confirmation',
            'Your order #' . $order->getId() . ' has been processed.'
        );
    }
}

// In tests, you can inject a mock email service
class MockEmailService implements EmailService {
    public $sentEmails = [];
    
    public function sendEmail($to, $subject, $body) {
        $this->sentEmails[] = [
            'to' => $to,
            'subject' => $subject,
            'body' => $body
        ];
        return true;
    }
}

// Test code
$mockEmail = new MockEmailService();
$processor = new OrderProcessor($mockEmail);
$processor->processOrder($order);

// Assert email was "sent" with correct details
assert(count($mockEmail->sentEmails) === 1);
assert($mockEmail->sentEmails[0]['to'] === 'customer@example.com');
```

<a id="best-practices"></a>
## ✅ Best Practices

Follow these guidelines to use polymorphism effectively:

### 1. 🎯 Program to Interfaces, Not Implementations

```php
// Not ideal - tied to specific implementation
$logger = new FileLogger();

// Better - works with any Logger implementation
function setupLogger(): Logger {
    return new FileLogger();
}
```

### 2. 📏 Use the Liskov Substitution Principle

Child classes should be substitutable for their parent classes without causing issues:

```php
// Parent class
class Bird {
    public function fly() {
        return "Flying high";
    }
}

// This violates Liskov Substitution Principle
class Penguin extends Bird {
    public function fly() {
        throw new Exception("Penguins can't fly!"); // Problem!
    }
}

// Better design - adjust the class hierarchy
abstract class Bird {
    // Common bird behaviors
}

class FlyingBird extends Bird {
    public function fly() {
        return "Flying high";
    }
}

class Penguin extends Bird {
    public function swim() {
        return "Swimming fast";
    }
}
```

### 3. 📚 Keep Interfaces Focused

Follow the Interface Segregation Principle - smaller, specific interfaces are better than large, general-purpose ones:

```php
// Too broad - forces classes to implement methods they might not need
interface Worker {
    public function work();
    public function eat();
    public function sleep();
}

// Better - focused interfaces
interface Workable {
    public function work();
}

interface Eatable {
    public function eat();
}

interface Sleepable {
    public function sleep();
}

// Classes can implement just what they need
class Human implements Workable, Eatable, Sleepable {
    // Implementation...
}

class Robot implements Workable {
    // Only needs to implement work()
}
```

### 4. 🧪 Use Dependency Injection

Pass dependencies into objects rather than creating them internally:

```php
// Not ideal - tightly coupled to specific logger
class OrderProcessor {
    private $logger;
    
    public function __construct() {
        $this->logger = new FileLogger();
    }
}

// Better - accepts any compatible logger
class OrderProcessor {
    private $logger;
    
    public function __construct(LoggerInterface $logger) {
        $this->logger = $logger;
    }
}
```

### 5. 🔍 Favor Composition Over Inheritance

Use interfaces and composition when inheritance might create inflexible designs:

```php
// Instead of a deep inheritance hierarchy:
// Vehicle → Car → SportsCar → ElectricSportsCar

// Consider a composition approach:
interface Engine {
    public function start();
    public function stop();
}

class GasolineEngine implements Engine {
    // Implementation...
}

class ElectricEngine implements Engine {
    // Implementation...
}

class Car {
    private $engine;
    
    public function __construct(Engine $engine) {
        $this->engine = $engine;
    }
    
    public function start() {
        return $this->engine->start();
    }
}
```

<a id="practice-exercise"></a>
## 🏋️ Practice Exercise

Create a document rendering system with polymorphism:

1. Create an interface `DocumentRenderer` with methods:
   - `render()`: Returns the document content in the appropriate format
   - `getOutputFormat()`: Returns the format name (e.g., "HTML", "Plain Text")

2. Create several implementations:
   - `HTMLRenderer`: Renders with HTML tags
   - `PlainTextRenderer`: Renders as plain text
   - `MarkdownRenderer`: Renders with Markdown syntax

3. Create a `Document` class that uses the renderers

**Sample Solution:**

```php
<?php
interface DocumentRenderer {
    public function render(string $title, string $content): string;
    public function getOutputFormat(): string;
}

class HTMLRenderer implements DocumentRenderer {
    public function render(string $title, string $content): string {
        return "<!DOCTYPE html>
<html>
<head>
    <title>{$title}</title>
</head>
<body>
    <h1>{$title}</h1>
    <div>{$content}</div>
</body>
</html>";
    }
    
    public function getOutputFormat(): string {
        return "HTML";
    }
}

class PlainTextRenderer implements DocumentRenderer {
    public function render(string $title, string $content): string {
        $separator = str_repeat('=', strlen($title));
        return "{$title}\n{$separator}\n\n{$content}";
    }
    
    public function getOutputFormat(): string {
        return "Plain Text";
    }
}

class MarkdownRenderer implements DocumentRenderer {
    public function render(string $title, string $content): string {
        return "# {$title}\n\n{$content}";
    }
    
    public function getOutputFormat(): string {
        return "Markdown";
    }
}

class Document {
    private $title;
    private $content;
    
    public function __construct(string $title, string $content) {
        $this->title = $title;
        $this->content = $content;
    }
    
    public function renderWith(DocumentRenderer $renderer): string {
        echo "Rendering document in " . $renderer->getOutputFormat() . " format...\n";
        return $renderer->render($this->title, $this->content);
    }
}

// Usage
$document = new Document(
    "Polymorphism in PHP",
    "Polymorphism is a powerful OOP concept that allows objects of different classes to be treated through the same interface."
);

$htmlOutput = $document->renderWith(new HTMLRenderer());
$textOutput = $document->renderWith(new PlainTextRenderer());
$markdownOutput = $document->renderWith(new MarkdownRenderer());

echo "HTML Output:\n{$htmlOutput}\n\n";
echo "Plain Text Output:\n{$textOutput}\n\n";
echo "Markdown Output:\n{$markdownOutput}\n\n";
```

---

[Back to Fundamentals](./README.md) | [Previous: Inheritance](./04-inheritance.md) | [Next: Abstraction](./06-abstraction.md)
