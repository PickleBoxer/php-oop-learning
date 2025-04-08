# 🔄 Interfaces in PHP

> **💡 Pro Tip:** "Interfaces define what a class can do without dictating how it should do it, enabling different classes to work together through common contracts."

## 📋 Table of Contents

| Emoji | Topic | Description |
|:---:|---|---|
| 📘 | [What Are Interfaces](#what-are-interfaces) | Understanding the concept and purpose of interfaces |
| 🎯 | [Purpose & Benefits](#purpose--benefits) | Why interfaces matter in modern PHP applications |
| 🔰 | [Basic Implementation](#basic-implementation) | How to create and implement interfaces |
| 🛠️ | [Working with Interfaces](#working-with-interfaces) | Using interfaces in your code |
| 🧰 | [Interface Rules](#interface-rules) | Important rules and constraints for PHP interfaces |
| 🔢 | [Interface Constants](#interface-constants) | Working with constants in interfaces |
| 🔗 | [Multiple Interfaces](#multiple-interfaces) | Implementing more than one interface |
| 🌳 | [Interface Inheritance](#interface-inheritance) | Creating interface hierarchies |
| 🔍 | [Type Checking](#type-checking-with-interfaces) | Using interfaces for type verification |
| 📊 | [Interface vs. Abstract Class](#interface-vs-abstract-class) | Choosing between interfaces and abstract classes |
| 🌟 | [Real-world Example](#real-world-example-repository-pattern) | Applying interfaces in practical scenarios |
| 🚀 | [PHP 8 Features](#php-8-interface-improvements) | New interface capabilities in PHP 8 |
| 🧩 | [Design Patterns](#design-patterns-using-interfaces) | Common patterns that leverage interfaces |
| ✅ | [Best Practices](#best-practices) | Guidelines for effective interface usage |
| 🏋️ | [Practice Exercise](#practice-exercise) | Apply interface concepts in a hands-on exercise |

<a id="what-are-interfaces"></a>
## 📘 What Are Interfaces

An interface in PHP is a contract that specifies what methods a class must implement. Unlike abstract classes, interfaces are purely about method signatures with no implementation details.

> **🔍 Definition:** An interface defines a contract for what a class can do without specifying how it should do it, allowing different classes to be used interchangeably based on their capabilities rather than their type.

### Key Characteristics of Interfaces

| Aspect | Description |
|--------|-------------|
| 📝 Method Signatures | Define what methods implementing classes must have |
| ❌ No Implementation | Cannot contain method implementations (except in PHP 8.0+ with default methods) |
| 🔀 Multiple Implementation | Classes can implement multiple interfaces |
| 🧩 Type Declaration | Can be used as type hints to ensure objects have specific capabilities |

> **🔍 Real-world analogy:** Think of an interface like a power outlet standard. Any device with a compatible plug can connect to it, regardless of what the device is or how it works internally. The outlet (interface) only defines the connection point, not what the connected device does.

<a id="purpose--benefits"></a>
## 🎯 Purpose & Benefits

Interfaces serve several important purposes in object-oriented programming:

### 📋 Define Contracts

Interfaces establish clear agreements about what methods a class must implement, creating a predictable API.

```php
// The interface defines a clear contract for what any logger must do
interface Logger {
    public function log(string $message, string $level);
    public function getLogEntries(): array;
    public function clearLogs(): void;
}
```

### 🔀 Enable Polymorphism

Interfaces allow objects of different classes to be treated as instances of a common type.

```php
function processPayment(PaymentMethod $method, float $amount) {
    // Can work with any class that implements PaymentMethod
    return $method->processPayment($amount);
}

// Works with CreditCardPayment, PayPalPayment, etc.
```

### 🔌 Decouple Code

Interfaces reduce dependencies between classes by letting you program to an interface rather than a concrete implementation.

```php
// Before - tightly coupled
class OrderProcessor {
    private $mysqlDatabase;
    
    public function __construct() {
        $this->mysqlDatabase = new MySQLDatabase();
    }
}

// After - loosely coupled with interfaces
class OrderProcessor {
    private $database;
    
    public function __construct(Database $database) {
        $this->database = $database;
    }
}
```

### 🔄 Support Multiple Implementation

Unlike with inheritance, a class can implement multiple interfaces.

```php
class Product implements Serializable, JsonSerializable, ArrayAccess {
    // Can implement multiple different behaviors
}
```

### 🧪 Enhance Testability

Interfaces make it easier to create mock objects for testing.

```php
// Real implementation
class EmailService implements Notifier {
    public function send($to, $message) {
        // Actually send an email
    }
}

// Test mock implementation
class MockEmailService implements Notifier {
    public $sentMessages = [];
    
    public function send($to, $message) {
        // Just record the message for testing
        $this->sentMessages[] = ['to' => $to, 'message' => $message];
    }
}
```

> **❓ Did you know?** The concept of interfaces in programming draws inspiration from contract law, where parties agree to specific terms without dictating how those terms are fulfilled.

<a id="basic-implementation"></a>
## 🔰 Basic Implementation

Creating and implementing interfaces in PHP is straightforward:

```php
<?php
// Define an interface
interface Logger {
    // Method signatures only - no implementation
    public function log(string $message): void;
    public function info(string $message): void;
    public function warning(string $message): void;
    public function error(string $message): void;
}

// Class implementing the interface
class FileLogger implements Logger {
    private $logFile;
    
    public function __construct(string $logFile) {
        $this->logFile = $logFile;
    }
    
    // Must implement ALL methods from the interface
    public function log(string $message): void {
        $this->writeToLog("LOG", $message);
    }
    
    public function info(string $message): void {
        $this->writeToLog("INFO", $message);
    }
    
    public function warning(string $message): void {
        $this->writeToLog("WARNING", $message);
    }
    
    public function error(string $message): void {
        $this->writeToLog("ERROR", $message);
    }
    
    // Helper method - not part of the interface
    private function writeToLog(string $level, string $message): void {
        $timestamp = date('Y-m-d H:i:s');
        $logMessage = "[{$timestamp}] [{$level}] {$message}" . PHP_EOL;
        file_put_contents($this->logFile, $logMessage, FILE_APPEND);
    }
}

// Another class implementing the same interface
class DatabaseLogger implements Logger {
    private $db;
    
    public function __construct(PDO $db) {
        $this->db = $db;
    }
    
    public function log(string $message): void {
        $this->saveToDatabase("LOG", $message);
    }
    
    public function info(string $message): void {
        $this->saveToDatabase("INFO", $message);
    }
    
    public function warning(string $message): void {
        $this->saveToDatabase("WARNING", $message);
    }
    
    public function error(string $message): void {
        $this->saveToDatabase("ERROR", $message);
    }
    
    private function saveToDatabase(string $level, string $message): void {
        $stmt = $this->db->prepare("INSERT INTO logs (level, message, created_at) VALUES (?, ?, NOW())");
        $stmt->execute([$level, $message]);
    }
}
```

> **🔥 Pro Tip:** When designing interfaces, focus on the behavior (what things do) rather than what they are. For example, use `Renderable` rather than `UIComponent`.

<a id="working-with-interfaces"></a>
## 🛠️ Working with Interfaces

Once you've defined interfaces, you can use them to create flexible and interchangeable components:

```php
<?php
class Application {
    private $logger;
    
    // Type hint with interface instead of concrete class
    public function __construct(Logger $logger) {
        $this->logger = $logger;
    }
    
    public function run(): void {
        $this->logger->info("Application started");
        
        try {
            // Application logic
            $this->logger->log("Application running");
            $this->doSomethingRisky();
        } catch (Exception $e) {
            // Log the error with any logger implementation
            $this->logger->error("Error: " . $e->getMessage());
        }
        
        $this->logger->info("Application finished");
    }
    
    private function doSomethingRisky() {
        // Simulating potential error
        if (rand(0, 1) === 1) {
            throw new Exception("Something went wrong!");
        }
    }
}

// Using with FileLogger
$fileLogger = new FileLogger("app.log");
$app1 = new Application($fileLogger);
$app1->run();

// Using with DatabaseLogger - same code works with a different implementation!
$db = new PDO('mysql:host=localhost;dbname=testdb', 'username', 'password');
$dbLogger = new DatabaseLogger($db);
$app2 = new Application($dbLogger);
$app2->run();

// Creating a new logger implementation is easy
class ConsoleLogger implements Logger {
    public function log(string $message): void {
        echo date('H:i:s') . " LOG: $message\n";
    }
    
    public function info(string $message): void {
        echo date('H:i:s') . " \033[36mINFO: $message\033[0m\n";  // Cyan
    }
    
    public function warning(string $message): void {
        echo date('H:i:s') . " \033[33mWARNING: $message\033[0m\n";  // Yellow
    }
    
    public function error(string $message): void {
        echo date('H:i:s') . " \033[31mERROR: $message\033[0m\n";  // Red
    }
}

// Can use the new implementation without changing the Application class
$consoleLogger = new ConsoleLogger();
$app3 = new Application($consoleLogger);
$app3->run();
```

> **💡 Key Point:** The power of interfaces lies in flexibility. The `Application` class can work with any logger as long as it implements the `Logger` interface, making your code more adaptable to change.

<a id="interface-rules"></a>
## 🧰 Interface Rules

When working with PHP interfaces, you must follow these important rules:

| Rule | Description | Example |
|------|-------------|---------|
| 🔓 **Public Methods Only** | All methods in an interface must be public | `public function save();` |
| 📭 **No Implementations** | Methods can only be declared, not defined | `public function load($id);` (no method body) |
| 🚫 **No Properties** | Cannot contain regular properties, only constants | Cannot use `public $name;` |
| 🔄 **All Methods Required** | Implementing classes must define all interface methods | Must implement every method in the interface |
| 🧠 **Matching Signatures** | Method signatures must match exactly (names, parameters, return types) | Must have same parameters and return types |

### Method Signature Matching Example

```php
<?php
interface Searchable {
    public function search(string $keyword): array;
}

class Product implements Searchable {
    // Correct - signature matches exactly
    public function search(string $keyword): array {
        // Implementation
        return [];
    }
}

class BadImplementation implements Searchable {
    // Error - parameter type doesn't match
    public function search($keyword): array {
        return [];
    }
}

class AnotherBadImplementation implements Searchable {
    // Error - return type doesn't match
    public function search(string $keyword): iterable {
        return [];
    }
}
```

> **⚠️ Important:** Parameter types and return types must match exactly between interface and implementation. PHP 7.2+ enforces this strictly.

<a id="interface-constants"></a>
## 🔢 Interface Constants

Interfaces can define constants that become available to any implementing class:

```php
<?php
interface PaymentStatus {
    // Constants in interfaces are always public
    const PENDING = 'pending';
    const PAID = 'paid';
    const FAILED = 'failed';
    const REFUNDED = 'refunded';
}

class Payment implements PaymentStatus {
    private $status;
    
    public function __construct() {
        // Can access the constant directly
        $this->status = self::PENDING;
    }
    
    public function getStatus(): string {
        return $this->status;
    }
    
    public function markAsPaid(): void {
        $this->status = self::PAID;
    }
    
    public function markAsFailed(): void {
        $this->status = self::FAILED;
    }
    
    public function refund(): void {
        if ($this->status !== self::PAID) {
            throw new Exception("Cannot refund unpaid payment");
        }
        
        $this->status = self::REFUNDED;
    }
}

// Using the constants
$payment = new Payment();
echo $payment->getStatus(); // "pending"

$payment->markAsPaid();
echo $payment->getStatus(); // "paid"

// Can also access constants directly through the interface
if ($payment->getStatus() === PaymentStatus::PAID) {
    echo "Payment is completed";
}
```

> **❓ Did you know?** Interface constants are particularly useful for defining status values, error codes, or configuration options that should be consistent across different implementations.

<a id="multiple-interfaces"></a>
## 🔗 Multiple Interfaces

One of the strengths of interfaces is that a PHP class can implement multiple interfaces:

```php
<?php
interface Printable {
    public function getFormattedContent(): string;
}

interface Exportable {
    public function exportToFile(string $format, string $filename): bool;
}

interface Shareable {
    public function getShareableLink(): string;
    public function getEmbedCode(): string;
}

// Implementing multiple interfaces
class Document implements Printable, Exportable, Shareable {
    private $title;
    private $content;
    private $id;
    
    public function __construct(string $title, string $content) {
        $this->title = $title;
        $this->content = $content;
        $this->id = uniqid('doc_');
    }
    
    // Implementing Printable
    public function getFormattedContent(): string {
        return "<h1>{$this->title}</h1>\n<div>{$this->content}</div>";
    }
    
    // Implementing Exportable
    public function exportToFile(string $format, string $filename): bool {
        $data = '';
        
        switch ($format) {
            case 'txt':
                $data = "{$this->title}\n\n{$this->content}";
                break;
            case 'html':
                $data = $this->getFormattedContent();
                break;
            case 'json':
                $data = json_encode([
                    'id' => $this->id,
                    'title' => $this->title,
                    'content' => $this->content
                ]);
                break;
            default:
                throw new Exception("Unsupported format: $format");
        }
        
        return file_put_contents($filename, $data) !== false;
    }
    
    // Implementing Shareable
    public function getShareableLink(): string {
        return "https://example.com/documents/{$this->id}";
    }
    
    public function getEmbedCode(): string {
        return "<iframe src=\"https://example.com/embed/{$this->id}\" width=\"800\" height=\"600\"></iframe>";
    }
}

// Another class implementing a different combination of interfaces
class Image implements Printable, Shareable {
    // Implementation for Printable and Shareable methods
    // but not Exportable
    
    // ...Implementation details...
}
```

> **🔥 Pro Tip:** This is a major advantage over inheritance. A class can only extend one parent class, but it can implement as many interfaces as needed, giving you much more flexibility in your designs.

<a id="interface-inheritance"></a>
## 🌳 Interface Inheritance

Interfaces can inherit from other interfaces using the `extends` keyword:

```php
<?php
// Base interface for logging
interface Loggable {
    public function getLogData(): array;
}

// Base interface for entity identification
interface Identifiable {
    public function getId(): string;
}

// Interface inheriting from multiple interfaces
interface AuditableEntity extends Loggable, Identifiable {
    // Additional methods
    public function getCreatedAt(): DateTime;
    public function getUpdatedAt(): DateTime;
    public function getCreatedBy(): ?string;
}

// A class implementing the composite interface must implement ALL methods
// from AuditableEntity AND all its parent interfaces
class User implements AuditableEntity {
    private $id;
    private $username;
    private $email;
    private $createdAt;
    private $updatedAt;
    private $createdBy;
    
    // Constructor and other methods...
    
    // From Identifiable interface
    public function getId(): string {
        return $this->id;
    }
    
    // From Loggable interface
    public function getLogData(): array {
        return [
            'id' => $this->id,
            'username' => $this->username,
            'email' => $this->email,
            'created_at' => $this->createdAt->format('Y-m-d H:i:s'),
            'updated_at' => $this->updatedAt->format('Y-m-d H:i:s')
        ];
    }
    
    // From AuditableEntity interface
    public function getCreatedAt(): DateTime {
        return $this->createdAt;
    }
    
    public function getUpdatedAt(): DateTime {
        return $this->updatedAt;
    }
    
    public function getCreatedBy(): ?string {
        return $this->createdBy;
    }
}
```

> **📝 Note:** Unlike classes, interfaces can extend multiple parent interfaces. This allows you to create composite interfaces that combine capabilities from multiple sources.

<a id="type-checking-with-interfaces"></a>
## 🔍 Type Checking with Interfaces

You can use interfaces for type checking and declarations:

```php
<?php
// Check if an object implements an interface
$document = new Document("PHP Interfaces", "Content about interfaces...");

// Using instanceof operator
if ($document instanceof Printable) {
    echo "Document is printable\n";
    echo $document->getFormattedContent();
}

if ($document instanceof Exportable) {
    echo "Document is exportable\n";
    $document->exportToFile('txt', 'document.txt');
}

// Type hinting with interfaces in functions
function shareContent(Shareable $item): string {
    echo "Share this link: " . $item->getShareableLink();
    return $item->getEmbedCode();
}

function printContent(Printable $item): void {
    echo $item->getFormattedContent();
}

function exportContent(Exportable $item, string $format, string $filename): bool {
    return $item->exportToFile($format, $filename);
}

// These will work with any class that implements the required interface
$embedCode = shareContent($document);
printContent($document);
exportContent($document, 'html', 'document.html');

// Type declarations also work for return types
function getShareableItem(int $id): Shareable {
    // Could return any class that implements Shareable
    // based on the item type in the database
    if ($id < 100) {
        return new Document("Test", "Content");
    } else {
        return new Image("Test Image", "image.jpg");
    }
}
```

> **💡 Key Point:** Using interfaces for type hints makes your code more flexible. You're saying "I need something that can do X" rather than "I need specifically this class."

<a id="interface-vs-abstract-class"></a>
## 📊 Interface vs. Abstract Class

Both interfaces and abstract classes enable abstraction, but they have important differences:

| Feature | Interface | Abstract Class |
|---------|-----------|---------------|
| **Implementation** | No implementation* | Can include implemented methods |
| **Multiple Inheritance** | A class can implement multiple interfaces | A class can only extend one abstract class |
| **Properties** | Only constants | Can have regular properties |
| **Method Visibility** | All methods are public | Methods can have any visibility |
| **Constructor** | Cannot have a constructor | Can have a constructor |
| **Use Case** | Define a contract for unrelated classes | Provide a base for closely related classes |

*PHP 8.0+ allows default method implementations in interfaces

### When to Use Each

```php
// Use an interface when:
// 1. Multiple classes need to fulfill the same contract
// 2. Classes are not closely related
// 3. You're defining capabilities rather than structure
interface PaymentGateway {
    public function charge(float $amount): bool;
    public function refund(string $transactionId): bool;
}

// Use an abstract class when:
// 1. Classes share common implementation
// 2. Classes are closely related
// 3. You want to provide a common base structure
abstract class Animal {
    protected $name;
    protected $age;
    
    public function __construct(string $name, int $age) {
        $this->name = $name;
        $this->age = $age;
    }
    
    // Common method with implementation
    public function sleep(): string {
        return "{$this->name} is sleeping";
    }
    
    // Abstract method to be implemented
    abstract public function makeSound(): string;
}
```

> **🔍 Decision Guide:** Use interfaces to define what a class can do. Use abstract classes to define what a class is with some shared behavior.

<a id="real-world-example-repository-pattern"></a>
## 🌟 Real-world Example: Repository Pattern

Interfaces are commonly used in the Repository pattern to decouple data access from business logic:

```php
<?php
// Define the interface for user data access
interface UserRepositoryInterface {
    public function findById(int $id): ?User;
    public function findByEmail(string $email): ?User;
    public function findAll(int $limit = 0, int $offset = 0): array;
    public function save(User $user): void;
    public function delete(User $user): void;
}

// MySQL implementation
class MySqlUserRepository implements UserRepositoryInterface {
    private $db;
    
    public function __construct(PDO $db) {
        $this->db = $db;
    }
    
    public function findById(int $id): ?User {
        $stmt = $this->db->prepare("SELECT * FROM users WHERE id = :id");
        $stmt->execute(['id' => $id]);
        $userData = $stmt->fetch(PDO::FETCH_ASSOC);
        
        if (!$userData) {
            return null;
        }
        
        return $this->createUserFromData($userData);
    }
    
    public function findByEmail(string $email): ?User {
        $stmt = $this->db->prepare("SELECT * FROM users WHERE email = :email");
        $stmt->execute(['email' => $email]);
        $userData = $stmt->fetch(PDO::FETCH_ASSOC);
        
        if (!$userData) {
            return null;
        }
        
        return $this->createUserFromData($userData);
    }
    
    public function findAll(int $limit = 0, int $offset = 0): array {
        $sql = "SELECT * FROM users";
        
        if ($limit > 0) {
            $sql .= " LIMIT :limit OFFSET :offset";
            $stmt = $this->db->prepare($sql);
            $stmt->bindValue(':limit', $limit, PDO::PARAM_INT);
            $stmt->bindValue(':offset', $offset, PDO::PARAM_INT);
        } else {
            $stmt = $this->db->prepare($sql);
        }
        
        $stmt->execute();
        $usersData = $stmt->fetchAll(PDO::FETCH_ASSOC);
        
        $users = [];
        foreach ($usersData as $userData) {
            $users[] = $this->createUserFromData($userData);
        }
        
        return $users;
    }
    
    public function save(User $user): void {
        // Implementation for saving a user to MySQL
        if ($user->getId()) {
            // Update existing user
            // ...
        } else {
            // Insert new user
            // ...
        }
    }
    
    public function delete(User $user): void {
        // Implementation for deleting a user from MySQL
        $stmt = $this->db->prepare("DELETE FROM users WHERE id = :id");
        $stmt->execute(['id' => $user->getId()]);
    }
    
    private function createUserFromData(array $data): User {
        $user = new User(
            $data['id'],
            $data['username'],
            $data['email']
        );
        // Set other user properties...
        return $user;
    }
}

// In-memory implementation (for testing)
class InMemoryUserRepository implements UserRepositoryInterface {
    private $users = [];
    private $nextId = 1;
    
    public function findById(int $id): ?User {
        foreach ($this->users as $user) {
            if ($user->getId() === $id) {
                return $user;
            }
        }
        return null;
    }
    
    public function findByEmail(string $email): ?User {
        foreach ($this->users as $user) {
            if ($user->getEmail() === $email) {
                return $user;
            }
        }
        return null;
    }
    
    public function findAll(int $limit = 0, int $offset = 0): array {
        if ($limit === 0) {
            return array_values($this->users);
        }
        
        return array_slice(array_values($this->users), $offset, $limit);
    }
    
    public function save(User $user): void {
        if (!$user->getId()) {
            // New user, assign ID
            $user->setId($this->nextId++);
        }
        
        $this->users[$user->getId()] = $user;
    }
    
    public function delete(User $user): void {
        if (isset($this->users[$user->getId()])) {
            unset($this->users[$user->getId()]);
        }
    }
}

// User service that depends on the interface, not implementations
class UserService {
    private $repository;
    
    // Depends on interface, not concrete implementation
    public function __construct(UserRepositoryInterface $repository) {
        $this->repository = $repository;
    }
    
    public function getUserById(int $id): ?User {
        return $this->repository->findById($id);
    }
    
    public function getAllUsers(int $page = 1, int $perPage = 20): array {
        $offset = ($page - 1) * $perPage;
        return $this->repository->findAll($perPage, $offset);
    }
    
    public function registerUser(string $username, string $email, string $password): User {
        // Check if email is already used
        if ($this->repository->findByEmail($email)) {
            throw new \Exception("Email already in use");
        }
        
        // Create new user
        $user = new User(null, $username, $email);
        $user->setPasswordHash(password_hash($password, PASSWORD_DEFAULT));
        
        // Save user
        $this->repository->save($user);
        
        return $user;
    }
    
    public function deleteUser(int $id): bool {
        $user = $this->repository->findById($id);
        
        if (!$user) {
            return false;
        }
        
        $this->repository->delete($user);
        return true;
    }
}

// Example usage
// In production:
$productionDb = new PDO('mysql:host=localhost;dbname=app', 'user', 'password');
$productionRepo = new MySqlUserRepository($productionDb);
$userService = new UserService($productionRepo);

// In testing:
$testRepo = new InMemoryUserRepository();
$testUserService = new UserService($testRepo);
```

> **🔥 Pro Tip:** This pattern makes your code more testable and maintainable. You can easily swap out database implementations or create mock repositories for testing without changing your service logic.

<a id="php-8-interface-improvements"></a>
## 🚀 PHP 8 Interface Improvements

PHP 8 introduced several enhancements for interfaces:

### Mixed Type (PHP 8.0)

```php
<?php
interface CacheInterface {
    // Can use 'mixed' type for parameters or return types
    public function get(string $key, mixed $default = null): mixed;
    public function set(string $key, mixed $value, int $ttl = null): bool;
}
```

### Interface Constants with Type Declarations (PHP 8.1)

```php
<?php
interface PaymentStatusInterface {
    // Typed constants in PHP 8.1+
    public const string PENDING = 'pending';
    public const string PAID = 'paid';
    public const int STATUS_CODE_OK = 200;
    public const array VALID_STATUSES = ['pending', 'paid', 'failed', 'refunded'];
}
```

### Default Methods in Interfaces (PHP 8.0+)

```php
<?php
interface Collection {
    public function add($item): void;
    public function remove($item): void;
    public function contains($item): bool;
    
    // Default method implementation (PHP 8.0+)
    public function isEmpty(): bool {
        return $this->count() === 0;
    }
    
    public function count(): int;
}
```

> **❓ Did you know?** Default methods in interfaces were one of the most requested features for PHP interfaces before PHP 8. They help evolve interfaces without breaking existing implementations.

<a id="design-patterns-using-interfaces"></a>
## 🧩 Design Patterns Using Interfaces

Many design patterns rely heavily on interfaces:

### Strategy Pattern

The Strategy Pattern allows you to define a family of algorithms, encapsulate each one, and make them interchangeable:

```php
<?php
interface PaymentStrategy {
    public function pay(float $amount): bool;
    public function getName(): string;
}

class CreditCardPayment implements PaymentStrategy {
    private $cardNumber;
    private $cvv;
    private $expiry;
    
    public function __construct(string $cardNumber, string $cvv, string $expiry) {
        $this->cardNumber = $cardNumber;
        $this->cvv = $cvv;
        $this->expiry = $expiry;
    }
    
    public function pay(float $amount): bool {
        // Process credit card payment
        echo "Processing $amount payment using Credit Card ending with " . substr($this->cardNumber, -4) . "\n";
        return true;
    }
    
    public function getName(): string {
        return "Credit Card";
    }
}

class PayPalPayment implements PaymentStrategy {
    private $email;
    private $password;
    
    public function __construct(string $email, string $password) {
        $this->email = $email;
        $this->password = $password;
    }
    
    public function pay(float $amount): bool {
        // Process PayPal payment
        echo "Processing $amount payment using PayPal account: {$this->email}\n";
        return true;
    }
    
    public function getName(): string {
        return "PayPal";
    }
}

class Order {
    private $amount;
    private $paymentStrategy;
    
    public function __construct(float $amount) {
        $this->amount = $amount;
    }
    
    public function setPaymentMethod(PaymentStrategy $paymentStrategy): void {
        $this->paymentStrategy = $paymentStrategy;
    }
    
    public function checkout(): bool {
        if (!$this->paymentStrategy) {
            throw new Exception("Payment method not set");
        }
        
        echo "Checking out order for $" . number_format($this->amount, 2) . " using " . $this->paymentStrategy->getName() . "...\n";
        return $this->paymentStrategy->pay($this->amount);
    }
}

// Usage
$order = new Order(99.99);

// User selects payment method
$selectedMethod = "credit_card"; // This could come from user input

// Set the appropriate payment strategy
if ($selectedMethod === "credit_card") {
    $order->setPaymentMethod(new CreditCardPayment("4111111111111111", "123", "12/25"));
} elseif ($selectedMethod === "paypal") {
    $order->setPaymentMethod(new PayPalPayment("customer@example.com", "password"));
}

// Process the order with the selected payment method
$order->checkout();
```

### Observer Pattern

Interfaces are also commonly used in the Observer pattern:

```php
<?php
interface Subject {
    public function attach(Observer $observer): void;
    public function detach(Observer $observer): void;
    public function notify(): void;
}

interface Observer {
    public function update(Subject $subject): void;
}

class ConcreteSubject implements Subject {
    private $observers = [];
    private $state;
    
    public function attach(Observer $observer): void {
        $this->observers[] = $observer;
    }
    
    public function detach(Observer $observer): void {
        $key = array_search($observer, $this->observers, true);
        if ($key !== false) {
            unset($this->observers[$key]);
        }
    }
    
    public function notify(): void {
        foreach ($this->observers as $observer) {
            $observer->update($this);
        }
    }
    
    public function setState($state): void {
        $this->state = $state;
        $this->notify();
    }
    
    public function getState() {
        return $this->state;
    }
}

class ConcreteObserverA implements Observer {
    public function update(Subject $subject): void {
        echo "Observer A: Subject's state is now " . $subject->getState() . "\n";
    }
}

class ConcreteObserverB implements Observer {
    public function update(Subject $subject): void {
        echo "Observer B: Reacting to the state change to " . $subject->getState() . "\n";
    }
}

// Usage
$subject = new ConcreteSubject();

$observerA = new ConcreteObserverA();
$observerB = new ConcreteObserverB();

$subject->attach($observerA);
$subject->attach($observerB);

$subject->setState("new state");
// Output:
// Observer A: Subject's state is now new state
// Observer B: Reacting to the state change to new state
```

> **💡 Key Point:** Interfaces are a fundamental tool for implementing many design patterns because they enable loose coupling and focus on behavior rather than implementation.

<a id="best-practices"></a>
## ✅ Best Practices

Follow these guidelines to create effective interfaces in your PHP applications:

### 1. 🎯 Keep Interfaces Focused (ISP)

Follow the Interface Segregation Principle: create small, focused interfaces rather than large, general-purpose ones.

```php
// Bad: Too many methods in one interface
interface UserManager {
    public function register(string $username, string $password): User;
    public function login(string $username, string $password): bool;
    public function resetPassword(string $email): void;
    public function getProfile(int $userId): array;
    public function updateProfile(int $userId, array $data): void;
    public function deleteUser(int $userId): void;
    public function exportUserData(int $userId): string;
    // Too many responsibilities
}

// Good: Focused interfaces
interface UserRegistration {
    public function register(string $username, string $password): User;
}

interface UserAuthentication {
    public function login(string $username, string $password): bool;
    public function resetPassword(string $email): void;
}

interface UserProfileManager {
    public function getProfile(int $userId): array;
    public function updateProfile(int $userId, array $data): void;
}
```

### 2. 📝 Use Meaningful Names

Name interfaces to clearly describe what they do:

```php
// Good interface names
interface Serializable { /* ... */ }
interface JsonResponse { /* ... */ }
interface MessageHandler { /* ... */ }
interface PaymentProcessor { /* ... */ }

// Bad interface names - too vague or nouns instead of adjectives/verbs
interface Data { /* ... */ }
interface Manager { /* ... */ }
interface Helper { /* ... */ }
```

### 3. 📜 Document Interface Contracts

Use PHPDoc comments to explain expected behavior:

```php
/**
 * Represents an entity that can be stored in persistent storage.
 * 
 * Implementing classes must ensure that the save() method persists
 * the entire entity state to the appropriate storage medium, and
 * the delete() method completely removes the entity.
 */
interface Persistable {
    /**
     * Saves the entity to permanent storage.
     * 
     * @return bool True on success, false on failure
     * @throws PersistenceException If a storage error occurs
     */
    public function save(): bool;
    
    /**
     * Deletes the entity from permanent storage.
     * 
     * @return bool True if the entity was deleted, false if not found
     * @throws PersistenceException If a storage error occurs
     */
    public function delete(): bool;
}
```

### 4. 🚫 Don't Force Unnecessary Methods

Don't force implementing classes to provide methods they don't need:

```php
// Bad: Forces read-only repositories to implement write methods
interface Repository {
    public function find($id);
    public function save($entity);    // Not all repositories need this
    public function delete($entity);  // Not all repositories need this
}

// Better: Separate read and write capabilities
interface ReadableRepository {
    public function find($id);
    public function findAll();
}

interface WritableRepository {
    public function save($entity);
    public function delete($entity);
}

// Read-only repository only needs to implement ReadableRepository
class ReadOnlyUserRepository implements ReadableRepository { /* ... */ }

// Full repository implements both
class UserRepository implements ReadableRepository, WritableRepository { /* ... */ }
```

### 5. 🧩 Use Interfaces for Type Declarations

Use interfaces rather than concrete classes in type declarations:

```php
// Prefer this:
public function processLog(LoggerInterface $logger) { /* ... */ }

// Over this:
public function processLog(FileLogger $logger) { /* ... */ }
```

### 6. 🏷️ Consider "Marker" Interfaces for Typed Behavior

Marker interfaces (empty interfaces) can provide type information:

```php
// Marker interface - empty but provides type information
interface Cacheable {
    // No methods - just marks a class as cacheable
}

class User implements Cacheable { /* ... */ }
class Product implements Cacheable { /* ... */ }

// Cache system can check if an object is cacheable
function saveToCache($key, $object) {
    if (!$object instanceof Cacheable) {
        throw new InvalidArgumentException("Object must be cacheable");
    }
    // Cache the object
}
```

> **🔥 Pro Tip:** In PHP 8+, attributes might be a better solution than marker interfaces in some cases.

<a id="practice-exercise"></a>
## 🏋️ Practice Exercise

Create an event system using interfaces:

1. Create an `EventListener` interface with a `handleEvent` method that accepts an event object
2. Create an `EventDispatcher` interface with methods to add listeners and dispatch events
3. Create a few types of event listeners that react differently to events
4. Create a concrete `SimpleEventDispatcher` that implements the dispatcher interface

**Sample Solution:**

```php
<?php
// Step 1: Create an event value object
class Event {
    private $name;
    private $data;
    private $timestamp;
    
    public function __construct(string $name, array $data = []) {
        $this->name = $name;
        $this->data = $data;
        $this->timestamp = new DateTime();
    }
    
    public function getName(): string {
        return $this->name;
    }
    
    public function getData(): array {
        return $this->data;
    }
    
    public function getTimestamp(): DateTime {
        return $this->timestamp;
    }
}

// Step 2: Create the EventListener interface
interface EventListener {
    public function handleEvent(Event $event): void;
    public function getListenedEvents(): array;
}

// Step 3: Create the EventDispatcher interface
interface EventDispatcher {
    public function addListener(EventListener $listener): void;
    public function dispatch(Event $event): void;
    public function removeListener(EventListener $listener): void;
}

// Step 4: Create concrete listeners
class LoggingListener implements EventListener {
    private $logFile;
    
    public function __construct(string $logFile = 'events.log') {
        $this->logFile = $logFile;
    }
    
    public function handleEvent(Event $event): void {
        $timestamp = $event->getTimestamp()->format('Y-m-d H:i:s');
        $message = "[{$timestamp}] Event: {$event->getName()}\n";
        file_put_contents($this->logFile, $message, FILE_APPEND);
    }
    
    public function getListenedEvents(): array {
        return ['*']; // Listen to all events
    }
}

class UserNotificationListener implements EventListener {
    public function handleEvent(Event $event): void {
        $data = $event->getData();
        
        if (!isset($data['userId'])) {
            return;
        }
        
        $userId = $data['userId'];
        echo "Sending notification to user $userId about event: {$event->getName()}\n";
    }
    
    public function getListenedEvents(): array {
        return ['user.login', 'user.logout', 'user.registered'];
    }
}

class SecurityListener implements EventListener {
    public function handleEvent(Event $event): void {
        if ($event->getName() === 'security.breach') {
            echo "⚠️ SECURITY BREACH DETECTED! Taking security measures...\n";
            // Log the breach, notify administrators, block suspicious IP, etc.
        } else {
            $timestamp = $event->getTimestamp()->format('Y-m-d H:i:s');
            echo "[{$timestamp}] Security event: {$event->getName()}\n";
        }
    }
    
    public function getListenedEvents(): array {
        return ['security.breach', 'security.login_failed', 'security.permission_denied'];
    }
}

// Step 5: Create a concrete dispatcher implementation
class SimpleEventDispatcher implements EventDispatcher {
    private $listeners = [];
    
    public function addListener(EventListener $listener): void {
        $this->listeners[] = $listener;
    }
    
    public function removeListener(EventListener $listener): void {
        $index = array_search($listener, $this->listeners, true);
        if ($index !== false) {
            unset($this->listeners[$index]);
            $this->listeners = array_values($this->listeners); // Reindex
        }
    }
    
    public function dispatch(Event $event): void {
        foreach ($this->listeners as $listener) {
            $listenedEvents = $listener->getListenedEvents();
            
            // Check if the listener is interested in this event
            if (in_array($event->getName(), $listenedEvents) || in_array('*', $listenedEvents)) {
                $listener->handleEvent($event);
            }
        }
    }
}

// Usage example
$dispatcher = new SimpleEventDispatcher();

// Add listeners
$dispatcher->addListener(new LoggingListener());
$dispatcher->addListener(new UserNotificationListener());
$dispatcher->addListener(new SecurityListener());

// Dispatch events
$dispatcher->dispatch(new Event('user.login', ['userId' => 123]));
$dispatcher->dispatch(new Event('security.login_failed', ['ip' => '192.168.1.1', 'attempts' => 5]));
$dispatcher->dispatch(new Event('security.breach', ['location' => 'admin panel', 'ip' => '195.24.68.21']));
```

> **🌟 Key Takeaway:** Interfaces help create flexible, modular systems by defining contracts between components without tying them to specific implementations.

---

[Back to Fundamentals](./README.md) | [Previous: Abstraction](./06-abstraction.md) | [Next: Advanced OOP Concepts](../02-advanced-oop/README.md)
