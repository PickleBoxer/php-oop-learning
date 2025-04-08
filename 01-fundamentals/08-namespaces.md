# 🔖 Namespaces in PHP

> **💡 Pro Tip:** "Namespaces are like last names for your code. They help organize and prevent conflicts in large projects, just as having a last name helps distinguish between people with the same first name."

## 📋 Table of Contents

| Emoji | Topic | Description |
|:---:|---|---|
| 📘 | [What Are Namespaces](#what-are-namespaces) | Understanding the concept and purpose |
| 🎯 | [Benefits of Namespaces](#benefits-of-namespaces) | Why and when to use namespaces |
| 🔰 | [Basic Syntax](#basic-syntax) | How to define and use namespaces |
| 🔍 | [Using Namespaced Code](#using-namespaced-code) | Different ways to access namespaced elements |
| 🌟 | [Multiple Namespaces](#multiple-namespaces) | Working with different namespaces in a project |
| 📁 | [Namespace Organization](#namespace-organization) | Structuring namespaces in your project |
| ⚠️ | [Common Pitfalls](#common-pitfalls) | Mistakes to avoid with namespaces |
| ✅ | [Best Practices](#best-practices) | Guidelines for effective namespace usage |
| 🏋️ | [Practice Exercise](#practice-exercise) | Apply namespace concepts in code |

<a id="what-are-namespaces"></a>
## 📘 What Are Namespaces

Namespaces are a way of encapsulating items such as classes, interfaces, functions, and constants to avoid name conflicts in larger applications or when using third-party code.

> **🔍 Definition:** A namespace is like a container that groups related code elements under a unique identifier, allowing you to have items with the same name in different contexts.

### Key Aspects of Namespaces

| Aspect | Description |
|--------|-------------|
| 🔤 Naming Scope | Creates separate naming spaces to avoid conflicts |
| 📦 Organization | Groups related classes and functions together |
| 🔄 Autoloading | Works with PSR-4 autoloading standards for automatic class loading |
| 📝 Identification | Provides fully qualified names for precise identification |

> **🔍 Real-world analogy:** Think of namespaces like departments in a company. Each department can have an employee named "John," but referring to "Marketing\John" and "Engineering\John" makes it clear which person you're talking about.

<a id="benefits-of-namespaces"></a>
## 🎯 Benefits of Namespaces

Namespaces provide several important benefits for PHP developers:

### 1. 🛡️ Avoid Name Conflicts

Namespaces prevent collisions between your code and third-party libraries, or between different parts of your application:

```php
// Without namespaces - conflict!
class User { /* Your User class */ }
class User { /* Third-party User class - ERROR! */ }

// With namespaces - no conflict
namespace App\Models;
class User { /* Your User class */ }

namespace ThirdParty\Auth;
class User { /* Third-party User class - OK! */ }
```

### 2. 🗂️ Better Code Organization

Namespaces help you organize code in a logical, hierarchical structure:

```php
namespace App\Controllers;  // For controller classes
namespace App\Models;       // For data models
namespace App\Services;     // For service classes
namespace App\Exceptions;   // For custom exceptions
```

### 3. 📚 Improved Readability

Namespaces make code intentions clearer by showing which component a class belongs to:

```php
use App\Services\PaymentProcessor;
use App\Services\EmailService;
use App\Repositories\UserRepository;

// It's clear which component each class belongs to
```

### 4. 🔄 Enabling PSR-4 Autoloading

Namespaces work with PSR-4 autoloading, making it easy to automatically load classes:

```php
// File structure:
// src/Models/User.php

// In User.php:
namespace App\Models;

class User {
    // Class implementation
}

// Autoloader can find this file based on the namespace
```

> **❓ Did you know?** PHP's namespace syntax was inspired by package naming conventions in other languages like Java and C#, but uses the backslash `\` as a separator instead of the dot `.` used in those languages.

<a id="basic-syntax"></a>
## 🔰 Basic Syntax

Declaring and using namespaces in PHP is straightforward:

### Defining a Namespace

```php
<?php
// Must be the first statement in the file (except for declare)
namespace App\Models;

class User {
    private $username;
    private $email;
    
    public function __construct(string $username, string $email) {
        $this->username = $username;
        $this->email = $email;
    }
    
    public function getUsername(): string {
        return $this->username;
    }
    
    public function getEmail(): string {
        return $this->email;
    }
}
```

### Nesting Within Namespaces

```php
<?php
namespace App\Models\Admin;  // Deeper nested namespace

class AdminUser {
    // Class implementation
}
```

### Declaring Functions and Constants in Namespaces

```php
<?php
namespace App\Utilities;

function formatCurrency($amount, $currency = 'USD') {
    // Implementation
    return "$currency " . number_format($amount, 2);
}

const MAX_USERNAME_LENGTH = 50;
const DEFAULT_TIMEZONE = 'UTC';
```

> **🔥 Pro Tip:** Each PHP file should contain only one namespace declaration to avoid confusion. If you need multiple namespaces, use separate files.

<a id="using-namespaced-code"></a>
## 🔍 Using Namespaced Code

There are several ways to access elements within namespaces:

### 1. Fully Qualified Names

Using the complete namespace path, including the leading backslash:

```php
<?php
// Using fully qualified class name
$user = new \App\Models\User('johndoe', 'john@example.com');

// Using fully qualified function
$formattedPrice = \App\Utilities\formatCurrency(29.99, 'EUR');

// Using fully qualified constant
$maxLength = \App\Utilities\MAX_USERNAME_LENGTH;
```

### 2. Use Statements

Importing classes, functions, or constants into the current scope:

```php
<?php
// Import at the top of your file
use App\Models\User;
use App\Utilities\formatCurrency;
use const App\Utilities\MAX_USERNAME_LENGTH;

// Then use them without the namespace
$user = new User('johndoe', 'john@example.com'); 
$formattedPrice = formatCurrency(29.99, 'EUR');
$maxLength = MAX_USERNAME_LENGTH;
```

### 3. Namespace Aliases

Renaming imported elements to avoid conflicts or for convenience:

```php
<?php
// Using aliases to avoid conflicts or for clarity
use App\Models\User as UserModel;
use App\Services\User as UserService;

$user = new UserModel('johndoe', 'john@example.com');
$userService = new UserService();
```

### 4. Group Use Declarations (PHP 7.0+)

Importing multiple elements from the same namespace:

```php
<?php
// Group use syntax for elements from the same namespace
use App\Models\{User, Post, Comment};
use App\Services\{AuthService, EmailService};

$user = new User();
$post = new Post();
$authService = new AuthService();
```

> **🔥 Pro Tip:** For better readability, choose either fully qualified names or use statements consistently. Mixing both styles can make code harder to follow.

<a id="multiple-namespaces"></a>
## 🌟 Multiple Namespaces

Working with multiple namespaces requires understanding how they interact:

### Importing from Multiple Namespaces

```php
<?php
namespace App\Controllers;

// Import from different namespaces
use App\Models\User;
use App\Services\EmailService;
use App\Repositories\UserRepository;

class UserController {
    private $repository;
    private $emailService;
    
    public function __construct() {
        $this->repository = new UserRepository();
        $this->emailService = new EmailService();
    }
    
    public function register($username, $email, $password) {
        $user = new User($username, $email);
        $user->setPassword($password);
        
        $this->repository->save($user);
        $this->emailService->sendWelcomeEmail($user);
        
        return $user;
    }
}
```

### Global Namespace

The global namespace refers to code without any namespace declaration, accessed with a leading backslash:

```php
<?php
namespace App\Utilities;

function formatText($text) {
    // Use PHP's native str_replace function from the global namespace
    return \str_replace('-', ' ', $text);
    
    // Without the backslash, PHP would look for App\Utilities\str_replace
}
```

### Accessing Current Namespace with `namespace` Keyword

```php
<?php
namespace App\Services;

class ServiceLocator {
    public static function getInstance($serviceName) {
        // Create a class in the current namespace
        $className = __NAMESPACE__ . '\\' . $serviceName;
        return new $className();
    }
}

// Usage:
$emailService = ServiceLocator::getInstance('EmailService'); // Returns App\Services\EmailService
```

> **❓ Did you know?** The special constants `__NAMESPACE__` contains the current namespace name, and can be useful for dynamic class loading.

<a id="namespace-organization"></a>
## 📁 Namespace Organization

Organizing your namespaces effectively is important for maintainable codebases:

### Matching Directory Structure

Following PSR-4, namespaces typically match directory structure:

```php
// Directory structure:
// src/
// ├── Controllers/
// │   └── UserController.php
// ├── Models/
// │   └── User.php
// ├── Services/
// │   └── EmailService.php

// In UserController.php:
namespace App\Controllers;

class UserController {
    // Class implementation
}

// In User.php:
namespace App\Models;

class User {
    // Class implementation
}

// In EmailService.php:
namespace App\Services;

class EmailService {
    // Class implementation
}
```

### Grouping Related Code

Group related classes, interfaces, and traits under the same namespace:

```php
namespace App\Models;

class User {
    // Class implementation
}

class Post {
    // Class implementation
}

class Comment {
    // Class implementation
}
```

### Avoiding Deep Nesting

Keep namespace depth reasonable to avoid complexity:

```php
// Avoid this:
namespace App\Project\Module\Submodule\Feature;

// Prefer this:
namespace App\Feature;
```

> **🔥 Pro Tip:** Use meaningful and descriptive names for namespaces to make your codebase easier to navigate.

<a id="common-pitfalls"></a>
## ⚠️ Common Pitfalls

1. **Forgetting the leading backslash**: When using core PHP functions within a namespace
2. **Namespace declaration position**: Must be the first statement in a file
3. **Case sensitivity**: Namespaces are case-sensitive 
4. **Global namespace**: Without a namespace declaration, classes are in the global namespace

<a id="best-practices"></a>
## ✅ Best Practices

1. **PSR-4 compliance**: Structure namespaces to match directory structure
2. **One namespace per file**: Each file should contain only one namespace
3. **Descriptive names**: Use clear, hierarchical namespaces
4. **Avoid deep nesting**: Keep namespace depth reasonable
5. **Use statements at top**: Group all use statements at the top of the file

<a id="practice-exercise"></a>
## 🏋️ Practice Exercise

Try creating a simple PHP project with namespaces:

1. Create a directory structure following PSR-4 standards.
2. Define namespaces for different components (e.g., Models, Controllers, Services).
3. Implement a few classes and use them in a script.
4. Practice using fully qualified names, use statements, and aliases.

---

[Back to Fundamentals](./README.md) | [Previous: Interfaces](./07-interfaces.md) | [Next: OOP Cheat Sheet](./09-oop-cheat-sheet.md)
