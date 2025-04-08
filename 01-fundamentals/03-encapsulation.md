# 🔒 Encapsulation in PHP

> **💡 Pro Tip:** "Good encapsulation is like a well-designed safe - it protects what's valuable while providing controlled access when needed."

## 📋 Table of Contents

| Emoji | Topic | Description |
|:---:|---|---|
| 📘 | [What Is Encapsulation](#what-is-encapsulation) | Understanding the concept and principles |
| 🎯 | [Purpose & Benefits](#purpose--benefits) | Why encapsulation matters in OOP |
| 🔰 | [Basic Implementation](#basic-implementation) | How to implement encapsulation in PHP |
| 🔑 | [Access Modifiers](#access-modifiers) | Controlling visibility with public, protected, private |
| 📥 | [Getters and Setters](#getters-and-setters) | Creating controlled property access points |
| 👁️ | [Property Visibility](#property-visibility) | Best practices for choosing visibility levels |
| 🌐 | [Real-World Examples](#real-world-examples) | Practical applications in common scenarios |
| ✅ | [Best Practices](#best-practices) | Guidelines for effective encapsulation |

<a id="what-is-encapsulation"></a>
## 📘 What Is Encapsulation

Encapsulation is one of the four fundamental Object-Oriented Programming (OOP) concepts, alongside inheritance, polymorphism, and abstraction.

> **🔍 Definition:** Encapsulation is the practice of **hiding internal state and implementation details** of an object and only exposing operations that are necessary through well-defined interfaces.

### Key Aspects of Encapsulation

| Aspect | Description |
|--------|-------------|
| 🔐 Data Hiding | Restricting direct access to an object's components |
| 🛡️ Protection | Preventing unauthorized modification of data |
| 📝 Interface | Providing a controlled public API to interact with the object |
| 🧩 Bundling | Combining data and methods that operate on that data |

> **🔍 Real-world analogy:** Think of encapsulation like a car's engine compartment. Most drivers don't need direct access to the engine components, but they can control the car through a well-designed interface (steering wheel, pedals, dashboard).

<a id="purpose--benefits"></a>
## 🎯 Purpose & Benefits

Encapsulation serves several important purposes in object-oriented design:

### 🛡️ Data Protection

By restricting direct access to properties, you prevent external code from putting objects into invalid states.

```php
<?php
// Without encapsulation
$user->age = -30; // Invalid but allowed

// With encapsulation
$user->setAge(-30); // Can be validated and rejected
```

### ✅ Data Validation

Setter methods can validate input before changing an object's state:

```php
public function setAge($age) {
    if ($age >= 0 && $age <= 120) {
        $this->age = $age;
        return true;
    }
    return false;
}
```

### 🔄 Implementation Flexibility

You can change the internal implementation without affecting external code:

```php
// Before: Stored as separate properties
private $firstName;
private $lastName;

// After: Changed to store as array internally
private $nameData = ['first' => '', 'last' => ''];

// External code still uses the same methods
public function getFullName() {
    // Implementation changed, but interface remains the same
    return $this->nameData['first'] . ' ' . $this->nameData['last'];
}
```

### 📊 Debugging Support

Centralizing access to properties makes it easier to debug because changes happen in specific methods:

```php
public function setQuantity($value) {
    $oldValue = $this->quantity;
    $this->quantity = $value;
    $this->logger->log("Quantity changed from $oldValue to $value");
}
```

> **❓ Did you know?** Encapsulation allows you to add features like logging, validation, and notifications when properties change without altering how clients use your objects.

<a id="basic-implementation"></a>
## 🔰 Basic Implementation

In PHP, encapsulation is achieved primarily through access modifiers and getter/setter methods.

```php
<?php
class BankAccount {
    // Private properties - not accessible outside the class
    private $accountNumber;
    private $balance;
    private $accountType;
    
    // Constructor to initialize the object
    public function __construct(string $accountNumber, float $initialBalance = 0.0, string $type = 'checking') {
        // Validate account number format
        if (!preg_match('/^\d{10}$/', $accountNumber)) {
            throw new InvalidArgumentException("Account number must be 10 digits");
        }
        
        $this->accountNumber = $accountNumber;
        
        // Use setter for validation
        $this->setBalance($initialBalance);
        
        // Only allow certain account types
        if (!in_array($type, ['checking', 'savings', 'business'])) {
            throw new InvalidArgumentException("Invalid account type");
        }
        $this->accountType = $type;
    }
    
    // Public methods - accessible interface for external code
    public function deposit(float $amount): bool {
        // Validate input
        if ($amount <= 0) {
            return false;
        }
        
        // Modify internal state
        $this->balance += $amount;
        return true;
    }
    
    public function withdraw(float $amount): bool {
        // Validate input and state
        if ($amount <= 0 || $amount > $this->balance) {
            return false;
        }
        
        // Modify internal state
        $this->balance -= $amount;
        return true;
    }
    
    // Getter method to access private property
    public function getBalance(): float {
        return $this->balance;
    }
    
    // Private setter for internal use
    private function setBalance(float $amount): void {
        if ($amount < 0) {
            throw new InvalidArgumentException("Initial balance cannot be negative");
        }
        $this->balance = $amount;
    }
    
    // Getter for account number (note there is no setter - immutable)
    public function getAccountNumber(): string {
        // Only show last 4 digits for security
        return '******' . substr($this->accountNumber, -4);
    }
    
    // Get full account details as array
    public function getAccountDetails(): array {
        return [
            'number' => $this->getAccountNumber(),
            'balance' => $this->balance,
            'type' => $this->accountType
        ];
    }
}

// Usage
try {
    $account = new BankAccount('1234567890', 500, 'savings');
    
    echo "New account: " . $account->getAccountNumber() . "\n";
    echo "Initial balance: $" . $account->getBalance() . "\n";
    
    $account->deposit(250);
    echo "After deposit: $" . $account->getBalance() . "\n";
    
    $account->withdraw(100);
    echo "After withdrawal: $" . $account->getBalance() . "\n";
    
    // This would fail - can't access private property directly
    // echo $account->balance;
    
    // This works - using the getter
    echo "Final balance: $" . $account->getBalance() . "\n";
} catch (InvalidArgumentException $e) {
    echo "Error: " . $e->getMessage();
}
```

> **🔥 Pro Tip:** Design your classes with the "need to know" principle in mind. Only expose what external code absolutely needs to access.

<a id="access-modifiers"></a>
## 🔑 Access Modifiers in PHP

PHP provides three access modifiers to implement encapsulation:

| Modifier | Symbol | Visibility | Description |
|----------|--------|-----------|-------------|
| 🟢 Public | `public` | Everywhere | Can be accessed from anywhere |
| 🟡 Protected | `protected` | Class & Children | Can be accessed within the class and by derived classes |
| 🔴 Private | `private` | Class Only | Can be accessed only within the class that defines them |

### Visual Example

```php
<?php
class MyClass {
    public $publicProperty = "Public";
    protected $protectedProperty = "Protected";
    private $privateProperty = "Private";
    
    public function accessOwnProperties() {
        echo "Inside class: \n";
        echo $this->publicProperty . "\n";    // ✅ OK
        echo $this->protectedProperty . "\n"; // ✅ OK
        echo $this->privateProperty . "\n";   // ✅ OK
    }
}

class ChildClass extends MyClass {
    public function accessParentProperties() {
        echo "Inside child class: \n";
        echo $this->publicProperty . "\n";    // ✅ OK
        echo $this->protectedProperty . "\n"; // ✅ OK
        // echo $this->privateProperty . "\n";   // ❌ Error
    }
}

$obj = new MyClass();
echo "Outside class: \n";
echo $obj->publicProperty . "\n";     // ✅ OK
// echo $obj->protectedProperty . "\n"; // ❌ Error
// echo $obj->privateProperty . "\n";   // ❌ Error
```

> **💡 Key Point:** Select the appropriate access modifier based on who needs access to each property or method.

<a id="getters-and-setters"></a>
## 📥 Getters and Setters

Getters and setters are methods that provide controlled access to private properties, allowing validation, transformation, and other operations.

### Basic Pattern

```php
class User {
    private $name;
    private $email;
    private $role;
    
    // Getter - retrieves property value
    public function getName(): string {
        return $this->name;
    }
    
    // Setter - validates and sets property value
    public function setName(string $name): void {
        // Validation
        $name = trim($name);
        if (empty($name)) {
            throw new InvalidArgumentException("Name cannot be empty");
        }
        
        // Set the property
        $this->name = $name;
    }
    
    // More getters/setters...
}
```

### Advanced Techniques

#### 1. Read-Only Properties

```php
class Invoice {
    private $invoiceNumber;
    private $createdAt;
    
    public function __construct() {
        $this->invoiceNumber = 'INV-' . uniqid();
        $this->createdAt = new DateTime();
    }
    
    // Getter but no setter = read-only property
    public function getInvoiceNumber(): string {
        return $this->invoiceNumber;
    }
    
    public function getCreatedAt(): DateTime {
        // Return a copy to prevent modification
        return clone $this->createdAt;
    }
}
```

#### 2. Computed Properties

```php
class Rectangle {
    private $width;
    private $height;
    
    public function setWidth(float $width): void {
        if ($width <= 0) {
            throw new InvalidArgumentException("Width must be positive");
        }
        $this->width = $width;
    }
    
    public function setHeight(float $height): void {
        if ($height <= 0) {
            throw new InvalidArgumentException("Height must be positive");
        }
        $this->height = $height;
    }
    
    // Computed property - doesn't exist as a field
    public function getArea(): float {
        return $this->width * $this->height;
    }
    
    public function getPerimeter(): float {
        return 2 * ($this->width + $this->height);
    }
}
```

> **❓ Did you know?** PHP magic methods `__get()` and `__set()` can be used to create dynamic getters and setters for multiple properties, though explicit methods are often preferred for clarity.

<a id="property-visibility"></a>
## 👁️ Property Visibility

Select property visibility based on these guidelines:

### Visibility Decision Guide

| Visibility | When to Use |
|------------|------------|
| 🔴 Private | • For most properties<br>• When the property should never be accessed directly outside the class<br>• When you need validation or control over modifications |
| 🟡 Protected | • When subclasses need direct access<br>• When a property is part of the implementation that child classes might need |
| 🟢 Public | • For constants<br>• When direct access is safe and appropriate<br>• When performance is critical (rare) |

### Example: Choosing the Right Visibility

```php
<?php
class Shape {
    // Public constant - unchangeable and safe to expose
    public const MAX_DIMENSION = 1000;
    
    // Private - core properties with validation needs
    private $color;
    private $borderWidth;
    
    // Protected - subclasses need these for calculations
    protected $x;
    protected $y;
    
    // Private calculated/cached value
    private $cachedArea = null;
    
    public function __construct(float $x, float $y, string $color = 'black', float $borderWidth = 1.0) {
        $this->x = $x;
        $this->y = $y;
        $this->setColor($color);
        $this->setBorderWidth($borderWidth);
    }
    
    // Color needs validation - private with getter/setter
    public function getColor(): string {
        return $this->color;
    }
    
    public function setColor(string $color): void {
        $validColors = ['red', 'green', 'blue', 'black', 'white', 'yellow'];
        if (!in_array(strtolower($color), $validColors)) {
            throw new InvalidArgumentException("Invalid color: $color");
        }
        $this->color = $color;
        
        // Reset cached values when properties change
        $this->cachedArea = null;
    }
    
    // Border width needs validation
    public function setBorderWidth(float $width): void {
        if ($width < 0 || $width > 10) {
            throw new InvalidArgumentException("Border width must be between 0 and 10");
        }
        $this->borderWidth = $width;
    }
    
    public function getBorderWidth(): float {
        return $this->borderWidth;
    }
    
    // Abstract method for area calculation
    public function calculateArea(): float {
        // Cache the result for better performance
        if ($this->cachedArea === null) {
            $this->cachedArea = $this->doCalculateArea();
        }
        return $this->cachedArea;
    }
    
    // Child classes implement this
    protected function doCalculateArea(): float {
        throw new \LogicException("Child class must implement doCalculateArea()");
    }
}

class Rectangle extends Shape {
    protected $width;
    protected $height;
    
    public function __construct(float $x, float $y, float $width, float $height, string $color = 'black') {
        parent::__construct($x, $y, $color);
        
        // Validate dimensions
        if ($width > self::MAX_DIMENSION || $height > self::MAX_DIMENSION) {
            throw new InvalidArgumentException("Dimensions exceed maximum allowed");
        }
        
        $this->width = $width;
        $this->height = $height;
    }
    
    // Implement the abstract method
    protected function doCalculateArea(): float {
        return $this->width * $this->height;
    }
    
    // Child-specific methods
    public function getWidth(): float {
        return $this->width;
    }
    
    public function getHeight(): float {
        return $this->height;
    }
}
```

<a id="real-world-examples"></a>
## 🌐 Real-World Examples

### Example 1: User Authentication System

```php
<?php
class User {
    private $id;
    private $username;
    private $email;
    private $passwordHash;
    private $failedLoginAttempts = 0;
    private $lastLoginAt;
    private $isActive = true;
    private $roles = [];
    
    public function __construct(string $username, string $email) {
        $this->setUsername($username);
        $this->setEmail($email);
    }
    
    // Username with validation
    public function getUsername(): string {
        return $this->username;
    }
    
    private function setUsername(string $username): void {
        $username = trim($username);
        if (strlen($username) < 3 || strlen($username) > 50) {
            throw new InvalidArgumentException("Username must be between 3-50 characters");
        }
        $this->username = $username;
    }
    
    // Email with validation
    public function getEmail(): string {
        return $this->email;
    }
    
    public function setEmail(string $email): void {
        if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
            throw new InvalidArgumentException("Invalid email format");
        }
        $this->email = $email;
    }
    
    // Password handling with security
    public function setPassword(string $password): void {
        // Validate password strength
        if (strlen($password) < 8) {
            throw new InvalidArgumentException("Password must be at least 8 characters");
        }
        
        // Hash the password - never store plain text!
        $this->passwordHash = password_hash($password, PASSWORD_DEFAULT);
    }
    
    public function verifyPassword(string $password): bool {
        // No direct access to passwordHash from outside
        if (empty($this->passwordHash)) {
            return false;
        }
        return password_verify($password, $this->passwordHash);
    }
    
    // Role management
    public function getRoles(): array {
        // Return a copy to prevent external modification
        return $this->roles;
    }
    
    public function addRole(string $role): void {
        $role = strtolower(trim($role));
        if (!in_array($role, $this->roles)) {
            $this->roles[] = $role;
        }
    }
    
    public function removeRole(string $role): void {
        $key = array_search(strtolower($role), $this->roles);
        if ($key !== false) {
            unset($this->roles[$key]);
            $this->roles = array_values($this->roles); // Reindex array
        }
    }
    
    public function hasRole(string $role): bool {
        return in_array(strtolower($role), $this->roles);
    }
    
    // Login handling
    public function recordSuccessfulLogin(): void {
        $this->lastLoginAt = new DateTime();
        $this->failedLoginAttempts = 0; // Reset counter
    }
    
    public function recordFailedLogin(): void {
        $this->failedLoginAttempts++;
    }
    
    public function isLocked(): bool {
        // Account gets temporarily locked after 5 failed attempts
        return $this->failedLoginAttempts >= 5;
    }
    
    // Account status
    public function deactivate(): void {
        $this->isActive = false;
    }
    
    public function activate(): void {
        $this->isActive = true;
    }
    
    public function isActive(): bool {
        return $this->isActive;
    }
}

// Usage
$user = new User("johndoe", "john@example.com");
$user->setPassword("SecurePass123!");
$user->addRole("editor");

// Login process
$credentials = [
    "username" => "johndoe",
    "password" => "SecurePass123!"
];

if ($user->isLocked()) {
    echo "Account is locked due to too many failed attempts";
} elseif (!$user->isActive()) {
    echo "Account is deactivated";
} elseif ($user->verifyPassword($credentials["password"])) {
    echo "Login successful!";
    $user->recordSuccessfulLogin();
    
    if ($user->hasRole("admin")) {
        echo "Welcome to the admin panel";
    }
} else {
    echo "Invalid credentials";
    $user->recordFailedLogin();
    
    if ($user->isLocked()) {
        echo "Account has been locked due to too many failed attempts";
    }
}
```

> **🔥 Pro Tip:** Notice how every interaction with sensitive data happens through methods that can enforce business rules and maintain data integrity.

### Example 2: Shopping Cart System

```php
<?php
class CartItem {
    private $productId;
    private $name;
    private $price;
    private $quantity;
    
    public function __construct(int $productId, string $name, float $price) {
        $this->productId = $productId;
        $this->name = $name;
        $this->setPrice($price);
        $this->quantity = 1; // Default quantity
    }
    
    public function getProductId(): int {
        return $this->productId;
    }
    
    public function getName(): string {
        return $this->name;
    }
    
    public function getPrice(): float {
        return $this->price;
    }
    
    private function setPrice(float $price): void {
        if ($price < 0) {
            throw new InvalidArgumentException("Price cannot be negative");
        }
        $this->price = $price;
    }
    
    public function getQuantity(): int {
        return $this->quantity;
    }
    
    public function setQuantity(int $quantity): void {
        if ($quantity < 1) {
            throw new InvalidArgumentException("Quantity must be at least 1");
        }
        if ($quantity > 100) {
            throw new InvalidArgumentException("Cannot order more than 100 items");
        }
        $this->quantity = $quantity;
    }
    
    public function getSubtotal(): float {
        return $this->price * $this->quantity;
    }
}

class ShoppingCart {
    private $items = [];
    private $couponCode = null;
    private $discountPercentage = 0;
    
    // Add item to cart
    public function addItem(int $productId, string $name, float $price, int $quantity = 1): void {
        // Check if product already exists in cart
        foreach ($this->items as $item) {
            if ($item->getProductId() === $productId) {
                // Update quantity instead of adding new item
                $newQuantity = $item->getQuantity() + $quantity;
                $item->setQuantity($newQuantity);
                return;
            }
        }
        
        // Add new item
        $item = new CartItem($productId, $name, $price);
        $item->setQuantity($quantity);
        $this->items[] = $item;
    }
    
    // Remove item from cart
    public function removeItem(int $productId): bool {
        foreach ($this->items as $key => $item) {
            if ($item->getProductId() === $productId) {
                unset($this->items[$key]);
                $this->items = array_values($this->items); // Reindex array
                return true;
            }
        }
        return false;
    }
    
    // Update item quantity
    public function updateQuantity(int $productId, int $quantity): bool {
        foreach ($this->items as $item) {
            if ($item->getProductId() === $productId) {
                $item->setQuantity($quantity);
                return true;
            }
        }
        return false;
    }
    
    // Get all items
    public function getItems(): array {
        return $this->items;
    }
    
    // Count items in cart
    public function getItemCount(): int {
        $count = 0;
        foreach ($this->items as $item) {
            $count += $item->getQuantity();
        }
        return $count;
    }
    
    // Apply coupon
    public function applyCoupon(string $code, float $discountPercentage): void {
        // Validate discount
        if ($discountPercentage < 0 || $discountPercentage > 100) {
            throw new InvalidArgumentException("Invalid discount percentage");
        }
        
        $this->couponCode = $code;
        $this->discountPercentage = $discountPercentage;
    }
    
    // Remove coupon
    public function removeCoupon(): void {
        $this->couponCode = null;
        $this->discountPercentage = 0;
    }
    
    // Get subtotal (before discount)
    public function getSubtotal(): float {
        $subtotal = 0;
        foreach ($this->items as $item) {
            $subtotal += $item->getSubtotal();
        }
        return $subtotal;
    }
    
    // Get discount amount
    public function getDiscountAmount(): float {
        if ($this->discountPercentage <= 0) {
            return 0;
        }
        return $this->getSubtotal() * ($this->discountPercentage / 100);
    }
    
    // Get total (after discount)
    public function getTotal(): float {
        return $this->getSubtotal() - $this->getDiscountAmount();
    }
    
    // Clear cart
    public function clear(): void {
        $this->items = [];
        $this->removeCoupon();
    }
}

// Usage
$cart = new ShoppingCart();

// Add items
$cart->addItem(1, "PHP Cookbook", 29.99, 1);
$cart->addItem(2, "Laravel T-shirt", 19.95, 2);

// Update quantity
$cart->updateQuantity(1, 2);

// Apply coupon
$cart->applyCoupon("SUMMER25", 25);

// Display cart summary
echo "Items in cart: " . $cart->getItemCount() . "\n";
echo "Subtotal: $" . number_format($cart->getSubtotal(), 2) . "\n";
echo "Discount: $" . number_format($cart->getDiscountAmount(), 2) . "\n";
echo "Total: $" . number_format($cart->getTotal(), 2) . "\n";

// Cannot access or modify internal properties directly
// $cart->items = []; // ❌ Error
// $cart->discountPercentage = 50; // ❌ Error
```

<a id="best-practices"></a>
## ✅ Best Practices

Follow these guidelines to implement effective encapsulation in your PHP code:

### 1. 🔒 Default to Private

Start with all properties as private, then only increase visibility when there's a clear need.

```php
class Customer {
    // Default to private
    private $name;
    private $email;
    private $dateOfBirth;
    // ...
}
```

### 2. 🛠️ Create Focused Methods

Create methods that perform specific operations rather than generic getters/setters.

```php
// Instead of:
$user->setStatus('inactive');

// Prefer:
$user->deactivate();
$user->suspend($reason);
$user->markOnVacation($startDate, $endDate);
```

### 3. 🔄 Validate Input in Setters

Always validate data before storing it in your object.

```php
public function setEmail(string $email): void {
    if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
        throw new InvalidArgumentException("Invalid email format");
    }
    $this->email = $email;
}
```

### 4. 🛑 Make Immutable What Shouldn't Change

Some properties should never change after object creation.

```php
class Transaction {
    private $id;
    private $timestamp;
    private $amount;
    
    public function __construct(float $amount) {
        $this->id = uniqid('txn_');
        $this->timestamp = new DateTime();
        $this->amount = $amount;
    }
    
    // Getters but NO setters for id and timestamp
    public function getId(): string {
        return $this->id;
    }
    
    public function getTimestamp(): DateTime {
        return clone $this->timestamp;
    }
    
    public function getAmount(): float {
        return $this->amount;
    }
}
```

### 5. 🔍 Return New Objects or Clones for Mutable Types

Prevent external code from modifying internal objects.

```php
class Event {
    private $date;
    
    public function setDate(DateTime $date): void {
        $this->date = clone $date; // Clone to prevent external changes
    }
    
    public function getDate(): DateTime {
        return clone $this->date; // Return clone to prevent modification
    }
}
```

### 6. 💡 Use Type Hints and Return Types

Type declarations help enforce contracts and improve code reliability.

```php
public function setAge(int $age): void {
    if ($age < 0 || $age > 120) {
        throw new InvalidArgumentException("Invalid age");
    }
    $this->age = $age;
}

public function getAge(): int {
    return $this->age;
}
```

### 7. 🎭 Program to Interfaces, Not Implementations

Hide implementation details behind interfaces.

```php
interface UserRepositoryInterface {
    public function findById(int $id): ?User;
    public function save(User $user): void;
}

class DatabaseUserRepository implements UserRepositoryInterface {
    private $db; // Implementation detail
    
    // Implementation of interface methods
}

// Client code works with the interface, not the implementation
function processUser(UserRepositoryInterface $repository, int $userId) {
    $user = $repository->findById($userId);
    // ...
}
```

> **💡 Key Insight:** Strong encapsulation leads to more maintainable, testable, and flexible code. It helps prevent bugs by limiting how objects can interact with each other.

---

[Back to Fundamentals](./README.md) | [Previous: Properties and Methods](./02-properties-and-methods.md) | [Next: Inheritance](./04-inheritance.md)
