# 🏛️ Classes and Objects in PHP

> **💡 Pro Tip:** "Objects are the building blocks of OOP, and classes are their blueprints."

## 📋 Table of Contents

| Emoji | Topic | Description |
|:---:|---|---|
| 🌟 | [Introduction](#introduction) | Overview of OOP in PHP |
| 🔄 | [Classes vs Objects](#classes-vs-objects) | Understanding the difference between blueprints and instances |
| 🔨 | [Creating Classes & Objects](#creating-classes--objects) | How to define classes and instantiate objects |
| 🧰 | [Properties & Methods](#properties--methods) | Working with class data and behaviors |
| 🔗 | [The $this Keyword](#the-this-keyword) | Accessing the current object instance |
| 🏗️ | [Constructors & Destructors](#constructors-and-destructors) | Object initialization and cleanup |
| 🔒 | [Class Constants](#class-constants) | Working with unchangeable class values |
| ✅ | [Best Practices](#best-practices) | Recommended OOP coding standards |
| ⚠️ | [Common Pitfalls](#common-pitfalls) | Mistakes to avoid in PHP OOP |
| 🏋️ | [Practice Exercise](#practice-exercise) | Hands-on coding challenge |

<a id="introduction"></a>
## 🌟 Introduction

Object-Oriented Programming (OOP) is a powerful paradigm that allows you to structure your code in a modular, reusable way. PHP has robust support for OOP, making it easier to build complex applications.

<a id="classes-vs-objects"></a>
## 🔄 Classes vs Objects

| Concept   | Description                                                       | Real-world Analogy                              |
|-----------|-------------------------------------------------------------------|-------------------------------------------------|
| 📝 Class  | A blueprint that defines properties (data) and methods (functions) | A blueprint for building a house                |
| 🏠 Object | An instance of a class with its own state                         | An actual house built from the blueprint        |

> **💡 Key Point:** Many different objects can be created from a single class, each with their own unique data.

### 🏗️ What are Classes?

A class is a **blueprint** or template that defines the properties (data) and methods (functions) that objects of that class will have. Think of it as a plan for creating objects of a specific type.

> **🔍 Real-world analogy:** A class is like a blueprint for a house. It specifies what rooms it will have, the layout, etc., but it's not the actual house.

```php
// Class definition (blueprint)
class House {
    // Blueprint details here
}
```

### 🧩 What are Objects?

An object is an **instance** of a class. When you instantiate a class, you create an object based on that class's definition. Each object has its own state (property values) but shares the same structure and behavior (methods) defined in the class.

> **🔍 Real-world analogy:** If a class is a house blueprint, an object is an actual house built from that blueprint. You can build multiple houses from the same blueprint, each with its own address, color, and occupants.

```php
// Creating objects (actual houses) from the blueprint
$house1 = new House();  // 123 Main St
$house2 = new House();  // 456 Oak Ave
```

> **❓ Did you know?** An application can have thousands or even millions of objects running simultaneously, each with its own state.

<a id="creating-classes--objects"></a>
## 🔨 Creating a Basic Class in PHP

Classes in PHP are defined using the `class` keyword followed by the class name and a pair of curly braces `{}` that contain the class members.

```php
<?php
class Person {
    // Properties (data attributes)
    public $name;     // Stores the person's name
    public $age;      // Stores the person's age
    
    // Constructor - automatically called when creating a new instance
    public function __construct($name, $age) {
        $this->name = $name;  // Initialize the name property
        $this->age = $age;    // Initialize the age property
    }
    
    // Method (function that belongs to the class)
    public function greet() {
        return "Hello, my name is {$this->name} and I am {$this->age} years old.";
    }
}
```

> **📝 Note:** By convention, class names in PHP start with an uppercase letter and use CamelCase (e.g., `Person`, `BankAccount`, `ShoppingCart`).

<a id="instantiating-objects"></a>
## 🚀 Instantiating Objects

Creating an object from a class is called instantiation. In PHP, you use the `new` keyword followed by the class name and parentheses (which may contain arguments for the constructor).

```php
<?php
// Create a new Person object
$person1 = new Person("John", 30);

// Create another Person object
$person2 = new Person("Jane", 25);

// Each object maintains its own state
echo $person1->greet(); // "Hello, my name is John and I am 30 years old."
echo $person2->greet(); // "Hello, my name is Jane and I am 25 years old."

// You can create as many objects as you need
$employees = [];
$employees[] = new Person("Alice", 28);
$employees[] = new Person("Bob", 35);
$employees[] = new Person("Charlie", 42);

// Loop through all employee objects
foreach ($employees as $employee) {
    echo $employee->greet() . "<br>";
}
```

> **🔥 Pro Tip:** Objects are passed by reference in PHP, not by value. This means if you assign an object to a new variable, both variables point to the same object in memory.

<a id="accessing-properties-and-methods"></a>
## 🔍 Accessing Properties and Methods

```php
<?php
// Accessing properties
echo $person1->name; // "John"
echo $person1->age;  // 30

// Modifying properties
$person1->name = "Jonathan";
echo $person1->name; // "Jonathan"

// Calling methods
echo $person1->greet(); // "Hello, my name is Jonathan and I am 30 years old."
```

<a id="properties--methods"></a>
## 🧰 Properties & Methods

### 📊 Class Properties

Properties are variables that belong to a class. They represent the data or state of an object.

```php
<?php
class Car {
    // Property declarations
    public $brand;        // No default value
    public $model;        // No default value
    public $color = "white"; // With default value
    private $vin;        // Private property - discussed in encapsulation
}

$car = new Car();
$car->brand = "Toyota";  // Setting a property
$car->model = "Corolla";
echo $car->color;        // Accessing a property: "white" (default value)
```

### ⚙️ Class Methods

Methods are functions that belong to a class. They define the behaviors of objects created from that class.

```php
<?php
class Calculator {
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
echo $calc->add(5, 3);      // 8
echo $calc->subtract(5, 3); // 2
echo $calc->multiply(5, 3); // 15
echo $calc->divide(6, 2);   // 3
```

<a id="the-this-keyword"></a>
## 🔗 The `$this` Keyword

The `$this` variable refers to the current instance of the class. It's used to access the properties and methods of the class from within the class itself.

```php
<?php
class BankAccount {
    public $accountNumber;
    public $balance = 0;
    
    public function deposit($amount) {
        if ($amount > 0) {
            $this->balance += $amount;
            return "Deposited $amount. New balance: {$this->balance}";
        }
        return "Cannot deposit negative amount.";
    }
    
    public function withdraw($amount) {
        if ($amount <= $this->balance) {
            $this->balance -= $amount;
            return "Withdrew $amount. New balance: {$this->balance}";
        }
        return "Insufficient funds.";
    }
}

$account = new BankAccount();
$account->accountNumber = "123456789";
echo $account->deposit(500);  // "Deposited 500. New balance: 500"
echo $account->withdraw(200); // "Withdrew 200. New balance: 300"
```
> **💡 Key Point:** Use `$this` to refer to the current object instance, especially when accessing properties and methods within the class.

<a id="constructors-and-destructors"></a>
## 🏗️ Constructors and Destructors

### 🔨 Constructor (`__construct`)

The `__construct()` method is called automatically when a new object is created. It's used to initialize the object's properties.

```php
<?php
class Book {
    public $title;
    public $author;
    public $pages;
    
    public function __construct($title, $author, $pages) {
        $this->title = $title;
        $this->author = $author;
        $this->pages = $pages;
        echo "Book '{$this->title}' has been created.\n";
    }
}

$book1 = new Book("The Hobbit", "J.R.R. Tolkien", 310);
```

```
Output: "Book 'The Hobbit' has been created."
```

### 🧹 Destructor (`__destruct`)

The `__destruct()` method is called automatically when an object is no longer referenced or when the script execution ends. It's used for cleanup tasks.

```php
<?php
class DatabaseConnection {
    private $connection;
    
    public function __construct($host, $username, $password) {
        echo "Connecting to database...\n";
        // In real applications, this would be a database connection
        $this->connection = "Connected to $host as $username";
    }
    
    public function __destruct() {
        echo "Closing database connection...\n";
        // In real applications, this would close the connection
        $this->connection = null;
    }
}

function openAndCloseConnection() {
    $db = new DatabaseConnection("localhost", "user", "password");
    // Do some database operations...
    echo "Operations completed.\n";
    // $db goes out of scope here, so __destruct() is called
}

openAndCloseConnection();
```

```
Output:
"Connecting to database..."
"Operations completed."
"Closing database connection..."
```

<a id="class-constants"></a>
## 🔒 Class Constants

Class constants are values that remain unchanged throughout the execution of a script. They are defined using the `const` keyword inside a class.

```php
<?php
class Circle {
    // Class constant
    const PI = 3.14159;
    
    public $radius;
    
    public function __construct($radius) {
        $this->radius = $radius;
    }
    
    public function calculateArea() {
        return self::PI * $this->radius * $this->radius;
    }
    
    public function calculateCircumference() {
        return 2 * self::PI * $this->radius;
    }
}

$circle = new Circle(5);
echo $circle->calculateArea();         // ~78.54
echo $circle->calculateCircumference(); // ~31.42
echo Circle::PI;                       // 3.14159
```

<a id="best-practices"></a>
## ✅ Best Practices

1. 📁 **One class per file:** Name the file the same as the class (Person.php for Person class)
2. 🔤 **Follow naming conventions:**
    - **Class names:** Use PascalCase (e.g., `Person`, `BankAccount`)
    - **Method/property names:** Use camelCase (e.g., `getUserData()`, `$totalAmount`)
3. 🧩 **Single responsibility:** Each class should do one thing well
4. 📚 **Document your code:** Use PHPDoc comments to explain class purpose
5. 🌳 **Favor composition over inheritance:** Build objects from smaller objects
6. 🚪 **Use proper access modifiers:** Don't make everything public

> **Pro Tip:** Think of classes as nouns and methods as verbs. A class should represent a "thing" while methods represent actions that thing can perform.

<a id="common-pitfalls"></a>
## ⚠️ Common Pitfalls
1. **Forgetting `$this`:** Always use `$this->property` to access class properties
2. **Not initializing properties:** Set defaults or initialize in constructor
3. **Creating "God" classes:** Avoid classes that do too many things
4. **Case sensitivity:** `new person()` won't work if the class is named `Person`
5. **Circular references:** Objects referencing each other can cause memory leaks

<a id="practice-exercise"></a>
## 🏋️ Practice Exercise

Create a `Student` class with:

- Properties: `name`, `id`, `grades` (array)
- Methods:
  - Constructor that accepts name and id
  - `addGrade($subject, $grade)` - adds a grade for a subject
  - `getAverageGrade()` - calculates average of all grades
  - `getHighestGrade()` - returns the highest grade and subject

**Sample Solution**

```php
<?php
class Student {
    public $name;
    public $id;
    private $grades = [];
    
    public function __construct($name, $id) {
        $this->name = $name;
        $this->id = $id;
    }
    
    public function addGrade($subject, $grade) {
        $this->grades[$subject] = $grade;
    }
    
    public function getAverageGrade() {
        if (empty($this->grades)) {
            return 0;
        }
        return array_sum($this->grades) / count($this->grades);
    }
    
    public function getHighestGrade() {
        if (empty($this->grades)) {
            return ["subject" => "None", "grade" => 0];
        }
        
        $highestSubject = array_keys($this->grades, max($this->grades))[0];
        return [
            "subject" => $highestSubject,
            "grade" => $this->grades[$highestSubject]
        ];
    }
}

// Usage
$student = new Student("Alice Smith", "A12345");
$student->addGrade("Math", 85);
$student->addGrade("Science", 92);
$student->addGrade("History", 78);

echo "Average grade: " . $student->getAverageGrade() . "\n";  // 85
$highest = $student->getHighestGrade();
echo "Highest grade: " . $highest["grade"] . " in " . $highest["subject"]; // 92 in Science
```

---

[Back to Fundamentals](./README.md) | [Next: Properties and Methods](./02-properties-and-methods.md)
