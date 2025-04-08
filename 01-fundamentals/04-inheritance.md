# 🧬 Inheritance in PHP

> **💡 Pro Tip:** "Inheritance lets you build hierarchical relationships between classes, reusing code while still maintaining specificity."

## 📋 Table of Contents

| Emoji | Topic | Description |
|:---:|---|---|
| 📘 | [What Is Inheritance](#what-is-inheritance) | Understanding the concept and its purpose |
| 🏗️ | [Basic Syntax](#basic-syntax) | How to create parent and child classes in PHP |
| 🚀 | [Using Inherited Classes](#using-inherited-classes) | Working with objects from inherited classes |
| 🔄 | [Method Overriding](#method-overriding) | Replacing parent methods with child implementations |
| 👆 | [Accessing Parent Methods](#accessing-parent-methods) | Using the parent keyword to call overridden methods |
| 🔒 | [The final Keyword](#the-final-keyword) | Preventing overrides and further inheritance |
| 🌲 | [Multi-level Inheritance](#multi-level-inheritance) | Creating deeper class hierarchies |
| ⚖️ | [Inheritance vs. Composition](#inheritance-vs-composition) | Choosing the right relationship between classes |
| ✅ | [Best Practices](#best-practices) | Guidelines for effective inheritance |
| 🏋️ | [Practice Exercise](#practice-exercise) | Apply inheritance concepts in a practical example |

<a id="what-is-inheritance"></a>
## 📘 What Is Inheritance

Inheritance is a fundamental OOP concept that enables a class (child/subclass) to inherit properties and methods from another class (parent/superclass). This establishes an "is-a" relationship between classes, creating a hierarchy of related types.

> **🔍 Definition:** Inheritance is the mechanism by which a class can derive properties and behaviors from another class, allowing for code reuse and hierarchical relationships.

### Key Terminology

| Term | Description | Example |
|------|-------------|---------|
| 👨‍👦 Parent Class | The class being inherited from (superclass, base class) | `Vehicle` |
| 👶 Child Class | The class that inherits (subclass, derived class) | `Car extends Vehicle` |
| 🧩 Inherit | To receive properties and methods from a parent class | Car inherits `startEngine()` |
| 🔀 Override | To replace a parent's method with a new implementation | Car overrides `getDetails()` |
| 📚 Extend | The PHP keyword used to create inheritance | `class Car extends Vehicle` |

> **🔍 Real-world analogy:** Think of species classification in biology. All mammals share certain characteristics (warm-blooded, have hair, produce milk), but a cat has specific features that differentiate it from other mammals. In OOP, `Mammal` would be the parent class, and `Cat` would be a child class that inherits the general mammal properties while adding its own specific ones.

<a id="basic-syntax"></a>
## 🏗️ Basic Syntax

Creating an inheritance relationship in PHP is straightforward, using the `extends` keyword:

```php
<?php
// Parent class (superclass)
class Vehicle {
    // Properties
    protected $make;
    protected $model;
    protected $year;
    protected $isRunning = false;
    
    // Constructor
    public function __construct($make, $model, $year) {
        $this->make = $make;
        $this->model = $model;
        $this->year = $year;
    }
    
    // Methods
    public function getDetails() {
        return "Vehicle: {$this->year} {$this->make} {$this->model}";
    }
    
    public function startEngine() {
        $this->isRunning = true;
        return "Engine started! Vrrrmmm...";
    }
    
    public function stopEngine() {
        $this->isRunning = false;
        return "Engine stopped.";
    }
    
    public function isEngineRunning() {
        return $this->isRunning;
    }
}

// Child class (subclass) inheriting from Vehicle
class Car extends Vehicle {
    // Additional properties
    private $numDoors;
    private $fuelType;
    
    // Extended constructor
    public function __construct($make, $model, $year, $numDoors, $fuelType = 'gasoline') {
        // Call parent constructor first
        parent::__construct($make, $model, $year);
        
        // Initialize Car-specific properties
        $this->numDoors = $numDoors;
        $this->fuelType = $fuelType;
    }
    
    // Override parent method
    public function getDetails() {
        return "Car: {$this->year} {$this->make} {$this->model}, {$this->numDoors}-door, {$this->fuelType}";
    }
    
    // Car-specific method
    public function honkHorn() {
        return "Beep beep!";
    }
    
    // Getter for a Car-specific property
    public function getNumDoors() {
        return $this->numDoors;
    }
}
```

> **💡 Key Point:** The `extends` keyword establishes the parent-child relationship, allowing the child class to inherit all the public and protected methods and properties from the parent.

<a id="using-inherited-classes"></a>
## 🚀 Using Inherited Classes

Once you've established an inheritance hierarchy, you can create and work with objects from any class in the hierarchy:

```php
<?php
// Create a Vehicle object (parent class)
$genericVehicle = new Vehicle("Generic", "Transport", 2023);
echo $genericVehicle->getDetails(); // "Vehicle: 2023 Generic Transport"
echo $genericVehicle->startEngine(); // "Engine started! Vrrrmmm..."

// Create a Car object (child class)
$myCar = new Car("Toyota", "Corolla", 2020, 4, "hybrid");

// Using methods defined in the child class
echo $myCar->getNumDoors(); // 4
echo $myCar->honkHorn(); // "Beep beep!"

// Using methods inherited from the parent class
echo $myCar->startEngine(); // "Engine started! Vrrrmmm..." (from Vehicle)
echo $myCar->isEngineRunning(); // true (from Vehicle)

// Using overridden method - calls the Car version
echo $myCar->getDetails(); // "Car: 2020 Toyota Corolla, 4-door, hybrid"

// This would cause an error - protected property not accessible outside class hierarchy
// echo $myCar->make; // Error: Cannot access protected property

// Inheritance enables polymorphism - treating child objects as instances of the parent type
function startVehicle(Vehicle $vehicle) {
    return $vehicle->startEngine();
}

// Works with either type
echo startVehicle($genericVehicle); // "Engine started! Vrrrmmm..."
echo startVehicle($myCar); // "Engine started! Vrrrmmm..."
```

> **❓ Did you know?** Child class objects can be used anywhere a parent class is expected. This is part of the Liskov Substitution Principle, one of the SOLID principles of object-oriented design.

<a id="method-overriding"></a>
## 🔄 Method Overriding

Method overriding allows a child class to provide a specific implementation of a method that's already defined in its parent class. The overridden method in the child class must have the same name, return type, and parameter list as the method in the parent class.

### How Method Overriding Works

```php
<?php
class Animal {
    public function makeSound() {
        return "Animal makes a generic sound";
    }
    
    public function move() {
        return "Animal moves in some way";
    }
}

class Cat extends Animal {
    // Override the makeSound method
    public function makeSound() {
        return "Meow!";
    }
    
    // Override the move method
    public function move() {
        return "Cat prowls silently";
    }
}

class Dog extends Animal {
    // Override the makeSound method
    public function makeSound() {
        return "Woof!";
    }
}

$animal = new Animal();
$cat = new Cat();
$dog = new Dog();

echo $animal->makeSound(); // "Animal makes a generic sound"
echo $cat->makeSound(); // "Meow!"
echo $dog->makeSound(); // "Woof!"

echo $animal->move(); // "Animal moves in some way"
echo $cat->move(); // "Cat prowls silently"
echo $dog->move(); // "Animal moves in some way" (inherits from parent)
```

> **🔥 Pro Tip:** When overriding methods, try to respect the "contract" of the original method. Your overridden method should fulfill the same purpose, just in a more specialized way for your child class.

### Type Declarations in Overridden Methods

PHP 7.4+ allows you to use covariant return types, meaning an overridden method can return a more specific type than its parent:

```php
<?php
class Document {
    public function createCopy(): Document {
        return new Document();
    }
}

class PDFDocument extends Document {
    // Covariant return type - more specific than the parent
    public function createCopy(): PDFDocument {
        return new PDFDocument();
    }
}
```

<a id="accessing-parent-methods"></a>
## 👆 Accessing Parent Methods

Sometimes you want to extend a parent's method rather than completely replacing it. PHP's `parent::` keyword allows you to call the parent class's implementation of a method:

```php
<?php
class Vehicle {
    protected $type = "generic";
    
    public function describe() {
        return "This is a {$this->type} vehicle.";
    }
    
    public function startEngine() {
        return "Engine started.";
    }
}

class Car extends Vehicle {
    public function __construct() {
        $this->type = "passenger"; // Modify inherited property
    }
    
    public function describe() {
        // Call parent method first
        $baseDescription = parent::describe();
        // Add additional information
        return $baseDescription . " It has four wheels and carries people.";
    }
    
    public function startEngine() {
        // Add additional functionality while preserving parent behavior
        return parent::startEngine() . " The car is ready to drive.";
    }
}

$car = new Car();
echo $car->describe(); 
// "This is a passenger vehicle. It has four wheels and carries people."

echo $car->startEngine();
// "Engine started. The car is ready to drive."
```

> **💡 Key Point:** Using `parent::method()` allows you to build on functionality rather than replace it entirely, promoting code reuse and avoiding duplication.

<a id="the-final-keyword"></a>
## 🔒 The `final` Keyword

The `final` keyword in PHP prevents method overriding and class inheritance, which can be useful for security and design purposes:

### 1. Final Methods

When a method is declared as `final`, it cannot be overridden by any child class:

```php
<?php
class BankAccount {
    protected $balance;
    
    public function deposit($amount) {
        if ($amount > 0) {
            $this->balance += $amount;
            return true;
        }
        return false;
    }
    
    // Critical security method that shouldn't be modified
    final public function transferMoney($destination, $amount) {
        // Complex secure logic for transferring money
        if ($this->withdraw($amount)) {
            $destination->deposit($amount);
            return true;
        }
        return false;
    }
    
    protected function withdraw($amount) {
        if ($amount <= $this->balance) {
            $this->balance -= $amount;
            return true;
        }
        return false;
    }
}

class SavingsAccount extends BankAccount {
    // This is allowed - not a final method
    public function deposit($amount) {
        // Add interest to deposits
        $amount *= 1.01;
        return parent::deposit($amount);
    }
    
    // This would cause a fatal error:
    // public function transferMoney($destination, $amount) {
    //     // Custom implementation - NOT ALLOWED
    // }
}
```

### 2. Final Classes

When a class is declared as `final`, it cannot be extended:

```php
<?php
// This class cannot be extended
final class SecuritySettings {
    private $encryptionKey;
    
    public function __construct($key) {
        $this->encryptionKey = $key;
    }
    
    public function encrypt($data) {
        // Encryption logic
    }
    
    public function decrypt($data) {
        // Decryption logic
    }
}

// This would cause a fatal error:
// class CustomSecuritySettings extends SecuritySettings {
//     // Not allowed because SecuritySettings is final
// }
```

> **❓ Did you know?** The `final` keyword can help maintain the integrity of your codebase by preventing unintended modifications to critical functionality, especially in security-sensitive areas.

<a id="multi-level-inheritance"></a>
## 🌲 Multi-level Inheritance

PHP supports multi-level inheritance, where a class can extend another class that itself extends another class. This creates a deeper hierarchy of specialization:

```php
<?php
// Base class
class Vehicle {
    protected $engineStatus = false;
    
    public function startEngine() {
        $this->engineStatus = true;
        return "Engine started";
    }
    
    public function stopEngine() {
        $this->engineStatus = false;
        return "Engine stopped";
    }
}

// First level of inheritance
class Car extends Vehicle {
    protected $numWheels = 4;
    protected $fuelType = "gasoline";
    
    public function drive() {
        if (!$this->engineStatus) {
            return "Cannot drive. Engine is off.";
        }
        return "Car is driving";
    }
}

// Second level of inheritance
class ElectricCar extends Car {
    public function __construct() {
        // Override the inherited property
        $this->fuelType = "electricity";
    }
    
    // Override the method from the parent
    public function drive() {
        if (!$this->engineStatus) {
            return "Cannot drive. Battery is not engaged.";
        }
        return "Electric car is driving silently";
    }
    
    // Add specialized method
    public function charge() {
        return "Electric car is charging";
    }
}

// Third level of inheritance
class SelfDrivingElectricCar extends ElectricCar {
    private $autonomyLevel = 5;
    
    public function enableAutopilot() {
        if (!$this->engineStatus) {
            return "Cannot enable autopilot. System is off.";
        }
        return "Autopilot engaged at level {$this->autonomyLevel}";
    }
    
    // Override method from grandparent
    public function drive() {
        return "Self-driving car is navigating autonomously";
    }
}

$teslaModel3 = new ElectricCar();
echo $teslaModel3->startEngine(); // From Vehicle: "Engine started"
echo $teslaModel3->drive(); // From ElectricCar: "Electric car is driving silently"

$waymo = new SelfDrivingElectricCar();
echo $waymo->startEngine(); // From Vehicle: "Engine started"
echo $waymo->charge(); // From ElectricCar: "Electric car is charging"
echo $waymo->enableAutopilot(); // From SelfDrivingElectricCar: "Autopilot engaged at level 5"
```

> **⚠️ Warning:** Deep inheritance hierarchies can become difficult to maintain and understand. Try to limit your inheritance chain to 2-3 levels when possible.

<a id="inheritance-vs-composition"></a>
## ⚖️ Inheritance vs. Composition

Inheritance and composition are two different approaches to reusing code, each with its own advantages:

### Inheritance ("is-a" relationship)

```php
class Vehicle { /* ... */ }
class Car extends Vehicle { /* ... */ }
```

A car **is a** vehicle.

### Composition ("has-a" relationship)

```php
class Engine { /* ... */ }
class Car {
    private $engine;
    
    public function __construct() {
        $this->engine = new Engine();
    }
}
```

A car **has an** engine.

### Visual Comparison

| Feature | Inheritance | Composition |
|---------|-------------|-------------|
| **Relationship** | is-a | has-a |
| **Implementation** | `class Child extends Parent` | `class Container { private $component; }` |
| **Code Reuse** | Inherits methods & properties | Contains & delegates to other objects |
| **Flexibility** | Static, fixed at compile time | Dynamic, can be changed at runtime |
| **Coupling** | Tightly coupled with parent | Loosely coupled with components |

### Example of Composition Instead of Inheritance

Instead of:

```php
abstract class Transport {
    public function move() { /* ... */ }
}

class Car extends Transport { /* ... */ }
class Boat extends Transport { /* ... */ }
class Airplane extends Transport { /* ... */ }
```

Consider:

```php
interface MovementStrategy {
    public function move();
}

class RoadMovement implements MovementStrategy {
    public function move() { return "Driving on roads"; }
}

class WaterMovement implements MovementStrategy {
    public function move() { return "Sailing on water"; }
}

class AirMovement implements MovementStrategy {
    public function move() { return "Flying through air"; }
}

class Vehicle {
    private $movementStrategy;
    
    public function __construct(MovementStrategy $strategy) {
        $this->movementStrategy = $strategy;
    }
    
    public function travel() {
        return $this->movementStrategy->move();
    }
}

// Usage
$car = new Vehicle(new RoadMovement());
$boat = new Vehicle(new WaterMovement());
$plane = new Vehicle(new AirMovement());

echo $car->travel(); // "Driving on roads"
echo $boat->travel(); // "Sailing on water"
echo $plane->travel(); // "Flying through air"

// Can even change behavior at runtime
$amphibiousVehicle = new Vehicle(new RoadMovement());
echo $amphibiousVehicle->travel(); // "Driving on roads"
// Switch to water movement
$amphibiousVehicle = new Vehicle(new WaterMovement());
echo $amphibiousVehicle->travel(); // "Sailing on water"
```

> **🔥 Pro Tip:** "Favor composition over inheritance" is a well-known design principle. Composition often provides more flexibility and can help avoid the complexities of deep inheritance hierarchies.

<a id="best-practices"></a>
## ✅ Best Practices

Follow these guidelines to use inheritance effectively:

### 1. 📏 Use inheritance for "is-a" relationships only

Only use inheritance when the child truly is a specialized version of the parent.

**Good:**
```php
class Animal { /* ... */ }
class Dog extends Animal { /* ... */ }
```

**Questionable:**
```php
class FileSystem { /* ... */ }
class Logger extends FileSystem { /* ... */ }  // A logger is not a file system
```

### 2. 📊 Favor shallow hierarchies

Keep inheritance hierarchies as shallow as possible, preferably not more than 2-3 levels deep.

### 3. 🎯 Respect the Liskov Substitution Principle

A child class should be usable wherever its parent class is expected without causing issues.

```php
// If this function expects a Vehicle
function testDrive(Vehicle $vehicle) {
    echo $vehicle->startEngine();
    echo $vehicle->drive();
    echo $vehicle->stopEngine();
}

// It should work properly with any Vehicle subclass
$car = new Car();
$electric = new ElectricCar();

testDrive($car);      // Should work as expected
testDrive($electric); // Should also work as expected
```

### 4. 🔄 Don't override to lessen functionality

Child methods shouldn't weaken preconditions or strengthen postconditions.

**Problematic:**
```php
class Collection {
    public function add($item) {
        // Adds any item
    }
}

class NumberCollection extends Collection {
    public function add($item) {
        if (!is_numeric($item)) {
            throw new Exception("Only numbers allowed"); // Strengthens precondition
        }
        // Add item
    }
}
```

### 5. 🧩 Prefer composition for "has-a" relationships

Use composition for components and behaviors.

```php
// Instead of inheritance:
// class Car extends Engine { }

// Use composition:
class Car {
    private $engine;
    
    public function __construct(Engine $engine) {
        $this->engine = $engine;
    }
}
```

### 6. 📚 Document the intent of inheritance

Make it clear why inheritance was chosen and how the hierarchy works.

```php
/**
 * Defines basic behavior for all bank account types.
 * Extended by specific account types like Checking, Savings, etc.
 */
abstract class BankAccount {
    // ...
}
```

### 7. 🚪 Use abstract classes for base functionality

Abstract classes provide a good way to share common implementation while forcing subclasses to implement required methods.

```php
abstract class DatabaseConnection {
    // Shared implementation
    public function query($sql) {
        $connection = $this->getConnection();
        // Execute query
    }
    
    // Must be implemented by child classes
    abstract protected function getConnection();
}

class MySQLConnection extends DatabaseConnection {
    protected function getConnection() {
        // MySQL-specific connection
    }
}
```

<a id="practice-exercise"></a>
## 🏋️ Practice Exercise

Create an inheritance hierarchy for a e-commerce system with:

1. A base `Product` class with:
   - Properties: `id`, `name`, `price`, `description`
   - Methods: `getPrice()`, `getDetails()`

2. Specialized product classes:
   - `PhysicalProduct` with `weight` and `shippingCost` properties
   - `DigitalProduct` with `downloadLink` and `fileSizeInMb` properties
   - `SubscriptionProduct` with `durationInMonths` and `renewalPrice` properties

3. Each should override `getPrice()` to include its specific pricing logic:
   - `PhysicalProduct`: includes shipping costs
   - `DigitalProduct`: no additional costs
   - `SubscriptionProduct`: calculates monthly price

**Sample Solution:**

```php
<?php
abstract class Product {
    protected $id;
    protected $name;
    protected $basePrice;
    protected $description;
    
    public function __construct($id, $name, $basePrice, $description) {
        $this->id = $id;
        $this->name = $name;
        $this->basePrice = $basePrice;
        $this->description = $description;
    }
    
    // Base price getter - to be extended by child classes
    public function getPrice() {
        return $this->basePrice;
    }
    
    public function getDetails() {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'price' => $this->getPrice(),
            'description' => $this->description,
            'type' => $this->getType()
        ];
    }
    
    // Force children to define their type
    abstract protected function getType();
}

class PhysicalProduct extends Product {
    protected $weight;
    protected $shippingCost;
    
    public function __construct($id, $name, $basePrice, $description, $weight) {
        parent::__construct($id, $name, $basePrice, $description);
        $this->weight = $weight;
        $this->calculateShippingCost();
    }
    
    protected function calculateShippingCost() {
        // Simple calculation based on weight
        $this->shippingCost = $this->weight * 1.5;
    }
    
    public function getPrice() {
        // Override to include shipping costs
        return $this->basePrice + $this->shippingCost;
    }
    
    public function getWeight() {
        return $this->weight;
    }
    
    protected function getType() {
        return 'physical';
    }
}

class DigitalProduct extends Product {
    protected $downloadLink;
    protected $fileSizeInMb;
    
    public function __construct($id, $name, $basePrice, $description, $fileSizeInMb) {
        parent::__construct($id, $name, $basePrice, $description);
        $this->fileSizeInMb = $fileSizeInMb;
        $this->generateDownloadLink();
    }
    
    protected function generateDownloadLink() {
        $this->downloadLink = "example.com/download/" . $this->id;
    }
    
    public function getDownloadLink() {
        return $this->downloadLink;
    }
    
    public function getFileSize() {
        return $this->fileSizeInMb;
    }
    
    protected function getType() {
        return 'digital';
    }
}

class SubscriptionProduct extends Product {
    protected $durationInMonths;
    protected $renewalPrice;
    
    public function __construct($id, $name, $basePrice, $description, $durationInMonths) {
        parent::__construct($id, $name, $basePrice, $description);
        $this->durationInMonths = $durationInMonths;
        // Renewal price is typically 90% of original price
        $this->renewalPrice = $basePrice * 0.9;
    }
    
    public function getMonthlyPrice() {
        return $this->basePrice / $this->durationInMonths;
    }
    
    public function getRenewalPrice() {
        return $this->renewalPrice;
    }
    
    public function getDuration() {
        return $this->durationInMonths;
    }
    
    protected function getType() {
        return 'subscription';
    }
}

// Usage example
$physical = new PhysicalProduct(1, "Coffee Mug", 15.00, "A ceramic coffee mug", 0.5);
$digital = new DigitalProduct(2, "E-book", 9.99, "Programming guide in PDF format", 4.2);
$subscription = new SubscriptionProduct(3, "Premium Membership", 59.99, "Access to all premium features", 12);

echo "Physical product: $" . $physical->getPrice(); // Price + shipping
echo "Digital product: $" . $digital->getPrice(); // Base price only
echo "Subscription: $" . $subscription->getPrice() . " (or $" . $subscription->getMonthlyPrice() . "/month)";
```

---

[Back to Fundamentals](./README.md) | [Previous: Encapsulation](./03-encapsulation.md) | [Next: Polymorphism](./05-polymorphism.md)
