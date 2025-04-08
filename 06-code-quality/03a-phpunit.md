# 🧪 Unit Testing with PHPUnit

## 📚 Overview

PHPUnit is the most widely used testing framework for PHP, providing a comprehensive suite of tools to write and run tests. It follows the xUnit architecture and allows you to verify that individual units of code (typically methods) work as expected.

## ⚙️ Installation and Setup

```bash
# Install via Composer
composer require --dev phpunit/phpunit

# Create a basic configuration file
./vendor/bin/phpunit --generate-configuration

# Check installation
./vendor/bin/phpunit --version
```

## 📋 Configuration (phpunit.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/10.0/phpunit.xsd"
         bootstrap="vendor/autoload.php"
         colors="true"
         cacheDirectory=".phpunit.cache">
  <testsuites>
    <testsuite name="Unit">
      <directory>tests/Unit</directory>
    </testsuite>
    <testsuite name="Integration">
      <directory>tests/Integration</directory>
    </testsuite>
  </testsuites>
  <coverage>
    <include>
      <directory suffix=".php">src</directory>
    </include>
    <exclude>
      <directory suffix=".php">src/Legacy</directory>
    </exclude>
  </coverage>
  <php>
    <env name="APP_ENV" value="testing"/>
    <env name="DB_CONNECTION" value="sqlite"/>
    <env name="DB_DATABASE" value=":memory:"/>
  </php>
</phpunit>
```

## 🚀 Writing Your First Test

```php
<?php

namespace Tests\Unit;

use PHPUnit\Framework\TestCase;
use App\StringCalculator;

class StringCalculatorTest extends TestCase
{
    public function testEmptyStringReturnsZero()
    {
        $calculator = new StringCalculator();
        $result = $calculator->add("");
        $this->assertEquals(0, $result);
    }

    public function testSingleNumberReturnsValue()
    {
        $calculator = new StringCalculator();
        $result = $calculator->add("5");
        $this->assertEquals(5, $result);
    }

    public function testTwoNumbersCommaDelimitedReturnsSum()
    {
        $calculator = new StringCalculator();
        $result = $calculator->add("5,7");
        $this->assertEquals(12, $result);
    }
}
```

Class being tested:

```php
<?php

namespace App;

class StringCalculator
{
    public function add(string $numbers): int
    {
        if (empty($numbers)) {
            return 0;
        }
        
        $delimiter = ",";
        $parts = explode($delimiter, $numbers);
        return array_sum(array_map('intval', $parts));
    }
}
```

## 💪 Test Methods and Assertions

PHPUnit provides many assertion methods:

| Assertion | Description | Example |
|-----------|-------------|---------|
| assertEquals | Verifies values are equal | `$this->assertEquals(5, $result);` |
| assertSame | Verifies values are identical (===) | `$this->assertSame(true, $result);` |
| assertTrue / assertFalse | Verifies boolean value | `$this->assertTrue($result);` |
| assertContains | Verifies array contains value | `$this->assertContains('apple', $fruits);` |
| assertCount | Verifies count of array | `$this->assertCount(3, $array);` |
| assertEmpty | Verifies value is empty | `$this->assertEmpty($collection);` |
| assertInstanceOf | Verifies object type | `$this->assertInstanceOf(User::class, $obj);` |
| expectException | Verifies exception thrown | `$this->expectException(\InvalidArgumentException::class);` |

## 🎯 Test Structure: Arrange-Act-Assert

Organize your tests with the AAA pattern:

```php
public function testUserRegistration()
{
    // Arrange: Set up the test conditions
    $userData = [
        'name' => 'Jane Doe',
        'email' => 'jane@example.com',
        'password' => 'secret123'
    ];
    $userService = new UserService();
    
    // Act: Perform the operation being tested
    $user = $userService->register($userData);
    
    // Assert: Check the results
    $this->assertInstanceOf(User::class, $user);
    $this->assertEquals('Jane Doe', $user->name);
    $this->assertEquals('jane@example.com', $user->email);
    $this->assertTrue(password_verify('secret123', $user->password));
}
```

## 🔄 Test Lifecycle Hooks

PHPUnit provides methods that run at different points in the test lifecycle:

```php
class UserServiceTest extends TestCase
{
    private $userService;
    private $db;
    
    // Runs once before any tests in this class
    public static function setUpBeforeClass(): void
    {
        // Set up shared resources
        self::$dbConnection = new DatabaseConnection();
    }
    
    // Runs before each test
    protected function setUp(): void
    {
        // Set up fresh test data
        $this->db = new TestDatabase();
        $this->userService = new UserService($this->db);
    }
    
    // Runs after each test
    protected function tearDown(): void
    {
        // Clean up after test
        $this->db->rollback();
    }
    
    // Runs once after all tests in this class
    public static function tearDownAfterClass(): void
    {
        // Clean up shared resources
        self::$dbConnection->close();
    }
    
    public function testExample()
    {
        // Test code here
    }
}
```

## 📊 Data Providers

Data providers allow you to run the same test with multiple sets of data:

```php
/**
 * @dataProvider additionProvider
 */
public function testAdd($a, $b, $expected)
{
    $calculator = new Calculator();
    $this->assertEquals($expected, $calculator->add($a, $b));
}

public function additionProvider()
{
    return [
        'positive numbers' => [5, 3, 8],
        'negative numbers' => [-5, -3, -8],
        'mixed numbers' => [5, -3, 2],
        'zeros' => [0, 0, 0]
    ];
}
```

## 🧩 Test Doubles: Stubs, Mocks, and Fakes

PHPUnit provides various test doubles to isolate your code:

### Stubs

```php
public function testGetUserReturnsUserFromRepository()
{
    // Create a stub of the UserRepository
    $userRepository = $this->createStub(UserRepository::class);
    
    // Configure the stub
    $userRepository->method('findById')
                  ->willReturn(new User(1, 'John Doe'));
    
    // Inject the stub
    $userService = new UserService($userRepository);
    
    // Test using the stub
    $user = $userService->getUser(1);
    $this->assertEquals('John Doe', $user->getName());
}
```

### Mocks

```php
public function testSendEmailCallsMailer()
{
    // Create a mock of the Mailer
    $mailer = $this->createMock(Mailer::class);
    
    // Set expectations on the mock
    $mailer->expects($this->once())
           ->method('send')
           ->with(
               $this->equalTo('user@example.com'),
               $this->equalTo('Subject'),
               $this->containsString('Hello')
           );
    
    // Inject the mock
    $notifier = new UserNotifier($mailer);
    
    // Execute
    $notifier->notifyUser('user@example.com');
}
```

## 🔍 Testing Exceptions

```php
public function testDivideByZeroThrowsException()
{
    $calculator = new Calculator();
    
    // Expect an exception
    $this->expectException(\InvalidArgumentException::class);
    $this->expectExceptionMessage('Division by zero');
    
    // This should throw the exception
    $calculator->divide(10, 0);
}
```

## ⚡ Performance Testing

```php
public function testLargeDatasetProcessingPerformance()
{
    $dataProcessor = new DataProcessor();
    $largeDataset = $this->generateLargeDataset();
    
    $startTime = microtime(true);
    $result = $dataProcessor->process($largeDataset);
    $endTime = microtime(true);
    
    $executionTime = $endTime - $startTime;
    $this->assertLessThan(2.0, $executionTime, "Data processing took too long: {$executionTime} seconds");
    
    // Also verify the result is correct
    $this->assertEquals(1000, count($result));
}
```

## 🔄 Running Tests

```bash
# Run all tests
./vendor/bin/phpunit

# Run specific test class
./vendor/bin/phpunit tests/Unit/CalculatorTest.php

# Run specific test method
./vendor/bin/phpunit --filter testAdd tests/Unit/CalculatorTest.php

# Run specific test suite
./vendor/bin/phpunit --testsuite Unit

# Generate coverage report
XDEBUG_MODE=coverage ./vendor/bin/phpunit --coverage-html coverage
```

## 📈 Understanding Test Reports

Sample PHPUnit output:

```
PHPUnit 10.0.0 by Sebastian Bergmann and contributors.

Runtime:       PHP 8.1.0
Configuration: /path/to/project/phpunit.xml

....F.....                                                    10 / 10 (100%)

Time: 00:00.015, Memory: 6.00 MB

There was 1 failure:

1) Tests\Unit\CalculatorTest::testDivide
Failed asserting that 5 is equal to 6.

/path/to/project/tests/Unit/CalculatorTest.php:45

FAILURES!
Tests: 10, Assertions: 15, Failures: 1.
```

## 💡 PHPUnit Best Practices

1. **Keep Tests Fast**: Optimize for quick feedback
2. **One Assert Per Test**: Test one behavior at a time
3. **Independent Tests**: Tests should not depend on each other
4. **Descriptive Method Names**: `testUserCannotLoginWithInvalidCredentials()` is better than `testLogin2()`
5. **Avoid Logic in Tests**: Keep tests simple and straightforward
6. **Test the Public API**: Focus on testing public methods
7. **Use Test Doubles Appropriately**: Don't mock everything
8. **Test Edge Cases**: Test boundary conditions and error states

## 🧪 Testing Database Operations

```php
class UserRepositoryTest extends TestCase
{
    private $pdo;
    private $repository;
    
    protected function setUp(): void
    {
        // Set up in-memory SQLite database
        $this->pdo = new PDO('sqlite::memory:');
        $this->pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        
        // Create test table
        $this->pdo->exec('
            CREATE TABLE users (
                id INTEGER PRIMARY KEY,
                name TEXT,
                email TEXT UNIQUE
            )
        ');
        
        // Set up repository with test database
        $this->repository = new UserRepository($this->pdo);
        
        // Insert test data
        $this->pdo->exec("
            INSERT INTO users (id, name, email) VALUES 
            (1, 'John Doe', 'john@example.com'),
            (2, 'Jane Smith', 'jane@example.com')
        ");
    }
    
    public function testFindUserById()
    {
        $user = $this->repository->findById(1);
        
        $this->assertNotNull($user);
        $this->assertEquals('John Doe', $user->name);
        $this->assertEquals('john@example.com', $user->email);
    }
    
    public function testCreateUser()
    {
        $newUser = [
            'name' => 'Bob Johnson',
            'email' => 'bob@example.com'
        ];
        
        $userId = $this->repository->create($newUser);
        
        // Verify user was created
        $this->assertNotNull($userId);
        
        // Verify we can retrieve the created user
        $user = $this->repository->findById($userId);
        $this->assertEquals('Bob Johnson', $user->name);
        $this->assertEquals('bob@example.com', $user->email);
    }
}
```

## 🧭 Navigation

- [← Back to Testing Practices](./03-testing-practices.md)
- [→ Test-Driven Development](./03b-tdd.md)

## 📚 Further Reading

- [PHPUnit Documentation](https://phpunit.readthedocs.io/)
- [Effective Unit Testing](https://www.amazon.com/Effective-Unit-Testing-guide-developers/dp/1935182579)
- [PHPUnit Cheat Sheet](https://devhints.io/phpunit)
- [Test Doubles in PHPUnit](https://phpunit.readthedocs.io/en/9.5/test-doubles.html)
- [Laravel Testing Documentation](https://laravel.com/docs/9.x/testing) (for Laravel projects)
