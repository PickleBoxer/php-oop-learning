# 🔧 Refactoring Techniques

## 📚 Overview

Refactoring techniques are specific, proven methods for improving code structure without changing its external behavior. These techniques help address code smells and gradually transform problematic code into cleaner, more maintainable solutions.

## 🌟 Key Refactoring Techniques

Below are essential refactoring techniques organized by category, with PHP-specific examples for each.

## 🏗️ Composing Methods

### Extract Method

Break down large methods into smaller, focused ones.

```php
// Before
public function renderCustomerReport($customerId)
{
    $customer = $this->customerRepository->findById($customerId);
    if (!$customer) {
        throw new CustomerNotFoundException();
    }
    
    $output = "<h1>Customer Report</h1>\n";
    $output .= "<p>Name: " . htmlspecialchars($customer->getName()) . "</p>\n";
    $output .= "<p>Email: " . htmlspecialchars($customer->getEmail()) . "</p>\n";
    
    $output .= "<h2>Orders</h2>\n";
    $output .= "<table>\n";
    $output .= "<tr><th>ID</th><th>Date</th><th>Amount</th><th>Status</th></tr>\n";
    
    $orders = $this->orderRepository->findByCustomerId($customerId);
    foreach ($orders as $order) {
        $output .= "<tr>\n";
        $output .= "<td>" . $order->getId() . "</td>\n";
        $output .= "<td>" . $order->getDate()->format('Y-m-d') . "</td>\n";
        $output .= "<td>$" . number_format($order->getAmount(), 2) . "</td>\n";
        $output .= "<td>" . ucfirst($order->getStatus()) . "</td>\n";
        $output .= "</tr>\n";
    }
    
    $output .= "</table>\n";
    
    return $output;
}
```

```php
// After
public function renderCustomerReport($customerId)
{
    $customer = $this->getCustomerOrFail($customerId);
    $orders = $this->orderRepository->findByCustomerId($customerId);
    
    $output = $this->renderCustomerHeader($customer);
    $output .= $this->renderOrdersTable($orders);
    
    return $output;
}

private function getCustomerOrFail($customerId)
{
    $customer = $this->customerRepository->findById($customerId);
    if (!$customer) {
        throw new CustomerNotFoundException();
    }
    return $customer;
}

private function renderCustomerHeader($customer)
{
    $output = "<h1>Customer Report</h1>\n";
    $output .= "<p>Name: " . htmlspecialchars($customer->getName()) . "</p>\n";
    $output .= "<p>Email: " . htmlspecialchars($customer->getEmail()) . "</p>\n";
    return $output;
}

private function renderOrdersTable($orders)
{
    $output = "<h2>Orders</h2>\n";
    $output .= "<table>\n";
    $output .= "<tr><th>ID</th><th>Date</th><th>Amount</th><th>Status</th></tr>\n";
    
    foreach ($orders as $order) {
        $output .= $this->renderOrderRow($order);
    }
    
    $output .= "</table>\n";
    return $output;
}

private function renderOrderRow($order)
{
    $output = "<tr>\n";
    $output .= "<td>" . $order->getId() . "</td>\n";
    $output .= "<td>" . $order->getDate()->format('Y-m-d') . "</td>\n";
    $output .= "<td>$" . number_format($order->getAmount(), 2) . "</td>\n";
    $output .= "<td>" . ucfirst($order->getStatus()) . "</td>\n";
    $output .= "</tr>\n";
    return $output;
}
```

### Inline Method

Simplify overly-fragmented code by removing unnecessary methods.

```php
// Before
public function calculateTotal($price, $quantity)
{
    return $this->multiply($price, $quantity);
}

private function multiply($a, $b)
{
    return $a * $b;
}
```

```php
// After
public function calculateTotal($price, $quantity)
{
    return $price * $quantity;
}
```

### Replace Temp with Query

Replace temporary variables with reusable query methods.

```php
// Before
public function calculateDiscount()
{
    $basePrice = $this->quantity * $this->itemPrice;
    $discountFactor = 0.98;
    
    if ($basePrice > 1000) {
        $discountFactor = 0.95;
    }
    
    return $basePrice * $discountFactor;
}
```

```php
// After
public function calculateDiscount()
{
    return $this->getBasePrice() * $this->getDiscountFactor();
}

private function getBasePrice()
{
    return $this->quantity * $this->itemPrice;
}

private function getDiscountFactor()
{
    return $this->getBasePrice() > 1000 ? 0.95 : 0.98;
}
```

## 🔄 Moving Features

### Move Method

Move a method from one class to another where it's more relevant.

```php
// Before
class Customer
{
    // ...other customer methods

    public function calculateOrderTotal(Order $order)
    {
        $total = 0;
        foreach ($order->getItems() as $item) {
            $total += $item->getPrice() * $item->getQuantity();
        }
        
        if ($this->isVip()) {
            $total *= 0.9; // 10% discount for VIP customers
        }
        
        return $total;
    }
}
```

```php
// After
class Customer
{
    // ...other customer methods
    
    public function isVip()
    {
        return $this->type === 'vip';
    }
}

class Order
{
    // ...order properties and methods
    
    public function calculateTotal(Customer $customer)
    {
        $total = 0;
        foreach ($this->getItems() as $item) {
            $total += $item->getPrice() * $item->getQuantity();
        }
        
        if ($customer->isVip()) {
            $total *= 0.9; // 10% discount for VIP customers
        }
        
        return $total;
    }
}
```

### Extract Class

Split large classes by moving related fields and methods into a new class.

```php
// Before
class Person
{
    private $name;
    private $phone;
    private $streetAddress;
    private $city;
    private $state;
    private $zipCode;
    
    public function getName() { return $this->name; }
    public function setName($name) { $this->name = $name; }
    
    public function getPhone() { return $this->phone; }
    public function setPhone($phone) { $this->phone = $phone; }
    
    public function getStreetAddress() { return $this->streetAddress; }
    public function setStreetAddress($address) { $this->streetAddress = $address; }
    
    public function getCity() { return $this->city; }
    public function setCity($city) { $this->city = $city; }
    
    public function getState() { return $this->state; }
    public function setState($state) { $this->state = $state; }
    
    public function getZipCode() { return $this->zipCode; }
    public function setZipCode($zipCode) { $this->zipCode = $zipCode; }
    
    public function getFullAddress() {
        return sprintf(
            "%s, %s, %s %s",
            $this->streetAddress,
            $this->city,
            $this->state,
            $this->zipCode
        );
    }
}
```

```php
// After
class Person
{
    private $name;
    private $phone;
    private $address;
    
    public function __construct()
    {
        $this->address = new Address();
    }
    
    public function getName() { return $this->name; }
    public function setName($name) { $this->name = $name; }
    
    public function getPhone() { return $this->phone; }
    public function setPhone($phone) { $this->phone = $phone; }
    
    public function getAddress() { return $this->address; }
    public function setAddress(Address $address) { $this->address = $address; }
}

class Address
{
    private $streetAddress;
    private $city;
    private $state;
    private $zipCode;
    
    public function getStreetAddress() { return $this->streetAddress; }
    public function setStreetAddress($address) { $this->streetAddress = $address; }
    
    public function getCity() { return $this->city; }
    public function setCity($city) { $this->city = $city; }
    
    public function getState() { return $this->state; }
    public function setState($state) { $this->state = $state; }
    
    public function getZipCode() { return $this->zipCode; }
    public function setZipCode($zipCode) { $this->zipCode = $zipCode; }
    
    public function getFullAddress() {
        return sprintf(
            "%s, %s, %s %s",
            $this->streetAddress,
            $this->city,
            $this->state,
            $this->zipCode
        );
    }
}
```

### Hide Delegate

Encapsulate delegation to another object.

```php
// Before
class Person
{
    private $department;
    
    public function getDepartment() {
        return $this->department;
    }
    
    public function setDepartment(Department $department) {
        $this->department = $department;
    }
}

// Client code
$manager = $person->getDepartment()->getManager();
```

```php
// After
class Person
{
    private $department;
    
    public function getDepartment() {
        return $this->department;
    }
    
    public function setDepartment(Department $department) {
        $this->department = $department;
    }
    
    public function getManager() {
        return $this->department->getManager();
    }
}

// Client code
$manager = $person->getManager();
```

## 🏛️ Organizing Data

### Replace Array with Object

Transform arrays into proper objects with named properties.

```php
// Before
$user = [
    'name' => 'John Doe',
    'email' => 'john@example.com',
    'age' => 34,
    'address' => [
        'street' => '123 Main St',
        'city' => 'Anytown',
        'zipCode' => '12345'
    ]
];

function displayUserInfo($user) {
    echo "Name: " . $user['name'] . "\n";
    echo "Email: " . $user['email'] . "\n";
    echo "Address: " . $user['address']['street'] . ", " 
         . $user['address']['city'] . " " . $user['address']['zipCode'] . "\n";
}
```

```php
// After
class Address
{
    private $street;
    private $city;
    private $zipCode;
    
    public function __construct($street, $city, $zipCode)
    {
        $this->street = $street;
        $this->city = $city;
        $this->zipCode = $zipCode;
    }
    
    public function getStreet() { return $this->street; }
    public function getCity() { return $this->city; }
    public function getZipCode() { return $this->zipCode; }
    
    public function getFullAddress()
    {
        return "{$this->street}, {$this->city} {$this->zipCode}";
    }
}

class User
{
    private $name;
    private $email;
    private $age;
    private $address;
    
    public function __construct($name, $email, $age, Address $address)
    {
        $this->name = $name;
        $this->email = $email;
        $this->age = $age;
        $this->address = $address;
    }
    
    public function getName() { return $this->name; }
    public function getEmail() { return $this->email; }
    public function getAge() { return $this->age; }
    public function getAddress() { return $this->address; }
}

function displayUserInfo(User $user) {
    echo "Name: " . $user->getName() . "\n";
    echo "Email: " . $user->getEmail() . "\n";
    echo "Address: " . $user->getAddress()->getFullAddress() . "\n";
}

// Usage
$address = new Address('123 Main St', 'Anytown', '12345');
$user = new User('John Doe', 'john@example.com', 34, $address);
displayUserInfo($user);
```

### Encapsulate Field

Hide direct access to fields by using getter and setter methods.

```php
// Before
class Person
{
    public $name;
}

// Client code
$person = new Person();
$person->name = 'John';
```

```php
// After
class Person
{
    private $name;
    
    public function getName()
    {
        return $this->name;
    }
    
    public function setName($name)
    {
        $this->name = $name;
    }
}

// Client code
$person = new Person();
$person->setName('John');
```

### Replace Type Code with Class

Replace primitive type codes with class hierarchies.

```php
// Before
class Employee
{
    const ENGINEER = 0;
    const MANAGER = 1;
    const DIRECTOR = 2;
    
    private $type;
    
    public function __construct($type)
    {
        $this->type = $type;
    }
    
    public function getSalary()
    {
        switch ($this->type) {
            case self::ENGINEER:
                return 70000;
            case self::MANAGER:
                return 100000;
            case self::DIRECTOR:
                return 150000;
        }
    }
}
```

```php
// After
abstract class Employee
{
    abstract public function getSalary();
}

class Engineer extends Employee
{
    public function getSalary()
    {
        return 70000;
    }
}

class Manager extends Employee
{
    public function getSalary()
    {
        return 100000;
    }
}

class Director extends Employee
{
    public function getSalary()
    {
        return 150000;
    }
}

// Factory method
class EmployeeFactory
{
    public static function createEmployee($type)
    {
        switch ($type) {
            case 'engineer':
                return new Engineer();
            case 'manager':
                return new Manager();
            case 'director':
                return new Director();
            default:
                throw new InvalidArgumentException("Unknown employee type: $type");
        }
    }
}
```

## 🧩 Simplifying Conditional Expressions

### Decompose Conditional

Break down complex conditionals into separate methods.

```php
// Before
public function calculateCharge($date, $quantity)
{
    if ($date->before(SUMMER_START) || $date->after(SUMMER_END)) {
        $charge = $quantity * $this->winterRate + $this->winterServiceCharge;
    } else {
        $charge = $quantity * $this->summerRate;
    }
    return $charge;
}
```

```php
// After
public function calculateCharge($date, $quantity)
{
    if ($this->isWinter($date)) {
        return $this->calculateWinterCharge($quantity);
    } else {
        return $this->calculateSummerCharge($quantity);
    }
}

private function isWinter($date)
{
    return $date->before(SUMMER_START) || $date->after(SUMMER_END);
}

private function calculateWinterCharge($quantity)
{
    return $quantity * $this->winterRate + $this->winterServiceCharge;
}

private function calculateSummerCharge($quantity)
{
    return $quantity * $this->summerRate;
}
```

### Replace Nested Conditional with Guard Clauses

Simplify deeply nested conditionals by using early returns.

```php
// Before
public function getPayAmount()
{
    $result = 0;
    
    if ($this->isDead) {
        $result = $this->deadAmount();
    } else {
        if ($this->isSeparated) {
            $result = $this->separatedAmount();
        } else {
            if ($this->isRetired) {
                $result = $this->retiredAmount();
            } else {
                $result = $this->normalPayAmount();
            }
        }
    }
    
    return $result;
}
```

```php
// After
public function getPayAmount()
{
    if ($this->isDead) {
        return $this->deadAmount();
    }
    
    if ($this->isSeparated) {
        return $this->separatedAmount();
    }
    
    if ($this->isRetired) {
        return $this->retiredAmount();
    }
    
    return $this->normalPayAmount();
}
```

### Replace Conditional with Polymorphism

Replace conditional logic with polymorphic objects.

```php
// Before
class Bird
{
    private $type;
    
    public function getSpeed()
    {
        switch ($this->type) {
            case 'EUROPEAN':
                return $this->getBaseSpeed();
            case 'AFRICAN':
                return $this->getBaseSpeed() - $this->getLoadFactor() * $this->getNumberOfCoconuts();
            case 'NORWEGIAN_BLUE':
                return ($this->isNailed) ? 0 : $this->getBaseSpeed();
        }
        throw new Exception("Unknown bird type");
    }
}
```

```php
// After
abstract class Bird
{
    abstract public function getSpeed();
    
    protected function getBaseSpeed()
    {
        return 10; // Default base speed
    }
}

class EuropeanBird extends Bird
{
    public function getSpeed()
    {
        return $this->getBaseSpeed();
    }
}

class AfricanBird extends Bird
{
    private $numberOfCoconuts;
    
    public function getSpeed()
    {
        return $this->getBaseSpeed() - $this->getLoadFactor() * $this->numberOfCoconuts;
    }
    
    private function getLoadFactor()
    {
        return 0.2;
    }
}

class NorwegianBlueBird extends Bird
{
    private $isNailed;
    
    public function getSpeed()
    {
        return ($this->isNailed) ? 0 : $this->getBaseSpeed();
    }
}
```

## 📝 Making Method Calls Simpler

### Rename Method

Change method names to better reflect their purpose.

```php
// Before
class Customer
{
    public function hp()  // Unclear name
    {
        return $this->hasLoyaltyPoints();
    }
}
```

```php
// After
class Customer
{
    public function hasLoyaltyDiscount()  // Clear, descriptive name
    {
        return $this->hasLoyaltyPoints();
    }
}
```

### Add Parameter

Add parameters to make methods more versatile.

```php
// Before
public function sendEmail($subject, $message)
{
    mail($this->email, $subject, $message);
}
```

```php
// After
public function sendEmail($subject, $message, $cc = null)
{
    $headers = '';
    
    if ($cc) {
        $headers = "Cc: $cc";
    }
    
    mail($this->email, $subject, $message, $headers);
}
```

### Parameterize Method

Replace multiple methods that do similar things with a single method that takes a parameter.

```php
// Before
public function fivePercentRaise()
{
    $this->salary *= 1.05;
}

public function tenPercentRaise()
{
    $this->salary *= 1.10;
}
```

```php
// After
public function raise($percentage)
{
    $this->salary *= (1 + $percentage / 100);
}

// Usage
$employee->raise(5);  // 5% raise
$employee->raise(10); // 10% raise
```

## 🔄 Dealing with Generalization

### Pull Up Method

Move method from subclasses to superclass.

```php
// Before
class Vehicle {}

class Car extends Vehicle
{
    public function startEngine()
    {
        // Start engine logic
    }
}

class Truck extends Vehicle
{
    public function startEngine()
    {
        // Duplicate start engine logic
    }
}
```

```php
// After
class Vehicle
{
    public function startEngine()
    {
        // Common start engine logic
    }
}

class Car extends Vehicle
{
    // Inherits startEngine from Vehicle
}

class Truck extends Vehicle
{
    // Inherits startEngine from Vehicle
}
```

### Extract Interface

Define an interface for common behavior.

```php
// Before
class Employee
{
    public function calculatePay()
    {
        // Calculate pay
    }
    
    public function reportHours()
    {
        // Report hours
    }
    
    public function save()
    {
        // Save employee data
    }
}

class Manager extends Employee
{
    // Override methods as needed
}
```

```php
// After
interface Payable
{
    public function calculatePay();
}

interface Reportable
{
    public function reportHours();
}

interface Persistable
{
    public function save();
}

class Employee implements Payable, Reportable, Persistable
{
    public function calculatePay()
    {
        // Calculate pay
    }
    
    public function reportHours()
    {
        // Report hours
    }
    
    public function save()
    {
        // Save employee data
    }
}

class Manager extends Employee
{
    // Override methods as needed
}

// Now you can use type hints for specific behaviors
function processPayroll(Payable $entity)
{
    // Process payroll using calculatePay
}
```

### Use Composition Over Inheritance

Replace inheritance with composition for more flexibility.

```php
// Before
abstract class Vehicle
{
    protected $speed = 0;
    
    public function accelerate($amount)
    {
        $this->speed += $amount;
    }
}

class Car extends Vehicle
{
    public function drive()
    {
        // Car-specific driving
    }
}
```

```php
// After
class Engine
{
    public function accelerate($amount, &$speed)
    {
        $speed += $amount;
    }
}

class Vehicle
{
    protected $speed = 0;
    protected $engine;
    
    public function __construct(Engine $engine)
    {
        $this->engine = $engine;
    }
    
    public function accelerate($amount)
    {
        $this->engine->accelerate($amount, $this->speed);
    }
}

class Car extends Vehicle
{
    public function drive()
    {
        // Car-specific driving
    }
}
```

## 📊 Automated Refactoring

### Using IDE Tools

Modern IDEs like PhpStorm provide automated refactoring tools:

| Refactoring | PhpStorm Shortcut | Description |
|-------------|-------------------|-------------|
| Rename | Shift+F6 | Rename classes, methods, variables |
| Extract Method | Ctrl+Alt+M | Extract selected code into a method |
| Extract Variable | Ctrl+Alt+V | Extract expression into a variable |
| Extract Constant | Ctrl+Alt+C | Extract value into a constant |
| Extract Interface | Ctrl+Shift+F6 | Extract interface from class |
| Move Class/Method | F6 | Move classes or methods between files |

### Using PHP Rector

[Rector](https://github.com/rectorphp/rector) is a powerful tool for automated PHP refactoring:

```bash
# Install Rector
composer require --dev rector/rector

# Create rector.php configuration
<?php
// rector.php
use Rector\Config\RectorConfig;
use Rector\Set\ValueObject\SetList;

return static function (RectorConfig $rectorConfig): void {
    $rectorConfig->paths([__DIR__ . '/src']);
    
    // Register sets of refactoring rules
    $rectorConfig->sets([
        SetList::CODE_QUALITY,
        SetList::DEAD_CODE,
        SetList::CODING_STYLE,
    ]);
};

# Run Rector
vendor/bin/rector process
```

## 🧭 Navigation

- [← Back to Common Code Smells](./04b-code-smells.md)
- [→ Debugging and Profiling](./05-debugging-profiling.md)

## 📚 Further Reading

- [Refactoring.guru](https://refactoring.guru/refactoring/techniques) - Comprehensive catalog of refactoring techniques
- [Refactoring: Improving the Design of Existing Code](https://martinfowler.com/books/refactoring.html) by Martin Fowler
- [Working Effectively with Legacy Code](https://www.amazon.com/Working-Effectively-Legacy-Michael-Feathers/dp/0131177052) by Michael Feathers
- [Clean Code: A Handbook of Agile Software Craftsmanship](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) by Robert C. Martin
- [PHP Rector Documentation](https://getrector.org/documentation)
