s# 🔄 GitHub Actions for PHP Projects

## 📚 Overview

GitHub Actions is a CI/CD platform integrated directly into GitHub that allows you to automate your build, test, and deployment workflows. It's especially powerful for PHP projects due to its flexibility and extensive ecosystem of pre-built actions.

## 🚀 Getting Started with GitHub Actions

### Setting Up Your First Workflow

GitHub Actions workflows are defined in YAML files stored in the `.github/workflows` directory of your repository.

```bash
# Create the workflows directory
mkdir -p .github/workflows

# Create a basic PHP workflow file
touch .github/workflows/php-ci.yml
```

### Basic PHP Workflow Structure

```yaml
name: PHP CI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3

    - name: Setup PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: '8.1'

    - name: Install Dependencies
      run: composer install -q --no-ansi --no-interaction --no-scripts --no-progress --prefer-dist

    - name: Run Tests
      run: vendor/bin/phpunit
```

## 🛠️ Common GitHub Actions for PHP Projects

### PHP Setup Action

The [shivammathur/setup-php](https://github.com/shivammathur/setup-php) action is essential for PHP projects:

```yaml
- name: Setup PHP with extensions and tools
  uses: shivammathur/setup-php@v2
  with:
    php-version: '8.1'
    extensions: mbstring, intl, pdo_mysql, zip, exif, pcntl, gd
    coverage: xdebug
    tools: composer:v2, phpunit, php-cs-fixer, phpstan
```

### Caching Dependencies

Speed up your workflows by caching Composer dependencies:

```yaml
- name: Get Composer cache directory
  id: composer-cache
  run: echo "dir=$(composer config cache-files-dir)" >> $GITHUB_OUTPUT

- name: Cache dependencies
  uses: actions/cache@v3
  with:
    path: ${{ steps.composer-cache.outputs.dir }}
    key: ${{ runner.os }}-composer-${{ hashFiles('**/composer.lock') }}
    restore-keys: ${{ runner.os }}-composer-
```

### Running Tests with Coverage

```yaml
- name: Run tests with coverage
  run: |
    XDEBUG_MODE=coverage vendor/bin/phpunit --coverage-clover=coverage.xml

- name: Upload coverage to Codecov
  uses: codecov/codecov-action@v3
  with:
    files: ./coverage.xml
```

## 🧰 Complete PHP Project Workflow

Here's a comprehensive workflow for PHP projects that includes testing, static analysis, and code style checking:

```yaml
name: PHP Project Workflow

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  verify:
    name: Verify Code
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: '8.1'
        extensions: mbstring, intl, pdo_mysql
        coverage: xdebug
        tools: composer:v2
    
    - name: Validate composer.json
      run: composer validate --strict
    
    - name: Cache dependencies
      uses: actions/cache@v3
      with:
        path: ~/.composer/cache
        key: ${{ runner.os }}-composer-${{ hashFiles('**/composer.lock') }}
        restore-keys: ${{ runner.os }}-composer-
    
    - name: Install dependencies
      run: composer install --prefer-dist --no-progress
    
    - name: Check syntax
      run: find src -type f -name "*.php" -exec php -l {} \;
    
    - name: Run static analysis
      run: |
        composer require --dev phpstan/phpstan
        vendor/bin/phpstan analyse src --level=5

    - name: Run coding standards check
      run: |
        composer require --dev squizlabs/php_codesniffer
        vendor/bin/phpcs src --standard=PSR12

  test:
    name: Run Tests
    runs-on: ubuntu-latest
    strategy:
      matrix:
        php: ['7.4', '8.0', '8.1']
        deps: ['lowest', 'stable']
        include:
          - php: '8.2'
            deps: 'dev'
      fail-fast: false
    
    services:
      mysql:
        image: mysql:5.7
        env:
          MYSQL_ALLOW_EMPTY_PASSWORD: yes
          MYSQL_DATABASE: test_db
        ports:
          - 3306:3306
        options: --health-cmd="mysqladmin ping" --health-interval=10s --health-timeout=5s --health-retries=3
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: ${{ matrix.php }}
        extensions: mbstring, intl, pdo_mysql
        coverage: xdebug
    
    - name: Install dependencies (lowest)
      if: matrix.deps == 'lowest'
      run: composer update --prefer-lowest --no-interaction
    
    - name: Install dependencies (stable)
      if: matrix.deps == 'stable'
      run: composer update --no-interaction
    
    - name: Install dependencies (dev)
      if: matrix.deps == 'dev'
      run: composer update --no-interaction --prefer-stable

    - name: Run test suite
      run: vendor/bin/phpunit --coverage-clover=coverage.xml
      env:
        DB_HOST: 127.0.0.1
        DB_PORT: 3306
        DB_DATABASE: test_db
        DB_USERNAME: root
        DB_PASSWORD: ''
    
    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v3
      with:
        file: ./coverage.xml
        fail_ci_if_error: false

  deploy:
    name: Deploy Application
    needs: [verify, test]
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
      with:
        fetch-depth: 0

    - name: Setup PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: '8.1'
        
    - name: Install dependencies
      run: composer install --no-dev --optimize-autoloader --prefer-dist --no-progress
    
    - name: Deploy to production
      uses: appleboy/ssh-action@master
      with:
        host: ${{ secrets.DEPLOY_HOST }}
        username: ${{ secrets.DEPLOY_USER }}
        key: ${{ secrets.DEPLOY_KEY }}
        script: |
          cd /var/www/production
          git pull origin main
          composer install --no-dev --optimize-autoloader
          php artisan migrate --force
          php artisan optimize
          php artisan config:cache
```

## 🔀 Matrix Testing Strategy

Matrix testing allows you to test multiple combinations of PHP versions, dependencies, and other variables:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        php: ['7.4', '8.0', '8.1', '8.2']
        laravel: ['8.*', '9.*', '10.*']
        exclude:
          # Laravel 10 doesn't support PHP 7.4
          - laravel: '10.*'
            php: '7.4'
    
    name: PHP ${{ matrix.php }} - Laravel ${{ matrix.laravel }}
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: ${{ matrix.php }}
          
      - name: Install Laravel
        run: composer require "laravel/framework:${{ matrix.laravel }}" --no-interaction --no-update
        
      - name: Install dependencies
        run: composer update --prefer-dist --no-progress
        
      - name: Run tests
        run: vendor/bin/phpunit
```

## 🗃️ Managing Secrets and Environment Variables

GitHub Actions provides a secure way to store sensitive information:

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to server
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.DEPLOY_HOST }}
          username: ${{ secrets.DEPLOY_USER }}
          key: ${{ secrets.DEPLOY_KEY }}
          script: |
            cd /var/www/app
            git pull
            composer install --no-dev
            php artisan migrate --force
```

To set up secrets:
1. Go to your repository on GitHub
2. Navigate to Settings → Secrets and variables → Actions
3. Click "New repository secret"
4. Add your secrets (e.g., DEPLOY_HOST, DEPLOY_USER, DEPLOY_KEY)

## 📋 Using Environment Files

For projects that use `.env` files:

```yaml
- name: Create .env file
  run: |
    echo "APP_NAME=\"My App\"" > .env
    echo "APP_ENV=testing" >> .env
    echo "APP_KEY=${{ secrets.APP_KEY }}" >> .env
    echo "DB_CONNECTION=mysql" >> .env
    echo "DB_HOST=127.0.0.1" >> .env
    echo "DB_PORT=3306" >> .env
    echo "DB_DATABASE=test_db" >> .env
    echo "DB_USERNAME=root" >> .env
    echo "DB_PASSWORD=" >> .env
```

## 🔄 Workflow Events and Triggers

### Branch-Specific Workflows

```yaml
on:
  push:
    branches: [ main, develop ]
    paths-ignore:
      - '**.md'
      - 'docs/**'
  pull_request:
    branches: [ main ]
```

### Schedule-Based Workflows

```yaml
on:
  schedule:
    - cron: '0 2 * * *'  # Run at 2 AM every day
```

### Manual Workflow Dispatch

```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to deploy to'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production
```

## 🐳 Using Docker Containers

You can run your workflows in Docker containers for consistent environments:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    container:
      image: php:8.1-cli
      
    steps:
      - uses: actions/checkout@v3
      
      - name: Install dependencies
        run: |
          apt-get update
          apt-get install -y git unzip
          curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
          
      - name: Install project dependencies
        run: composer install
        
      - name: Run tests
        run: vendor/bin/phpunit
```

## 🧪 Testing Database Workflows

For testing with databases, use GitHub Actions service containers:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      mysql:
        image: mysql:8.0
        env:
          MYSQL_ROOT_PASSWORD: password
          MYSQL_DATABASE: test_db
        ports:
          - 3306:3306
        options: --health-cmd="mysqladmin ping" --health-interval=10s --health-timeout=5s --health-retries=3
        
      redis:
        image: redis:6
        ports:
          - 6379:6379
        options: --health-cmd="redis-cli ping" --health-interval=10s --health-timeout=5s --health-retries=3
    
    steps:
      # Configure your tests to connect to these services
      - name: Run tests with database
        run: vendor/bin/phpunit
        env:
          DB_CONNECTION: mysql
          DB_HOST: 127.0.0.1
          DB_PORT: 3306
          DB_DATABASE: test_db
          DB_USERNAME: root
          DB_PASSWORD: password
          REDIS_HOST: 127.0.0.1
          REDIS_PORT: 6379
```

## 🏛️ Reusing Workflows and Custom Actions

### Creating Reusable Workflow

```yaml
# .github/workflows/reusable-php-tests.yml
name: Reusable PHP Tests

on:
  workflow_call:
    inputs:
      php-version:
        required: false
        default: '8.1'
        type: string
    secrets:
      db-password:
        required: false

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: ${{ inputs.php-version }}
          
      - name: Run tests
        run: vendor/bin/phpunit
```

### Using a Reusable Workflow

```yaml
# .github/workflows/main.yml
name: Main Workflow

on:
  push:
    branches: [ main ]

jobs:
  tests:
    uses: ./.github/workflows/reusable-php-tests.yml
    with:
      php-version: '8.1'
    secrets:
      db-password: ${{ secrets.DB_PASSWORD }}
```

## 🧩 Workflow Examples for Popular PHP Frameworks

### Laravel Workflow

```yaml
name: Laravel

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  laravel-tests:
    runs-on: ubuntu-latest
    
    services:
      mysql:
        image: mysql:5.7
        env:
          MYSQL_ROOT_PASSWORD: password
          MYSQL_DATABASE: laravel
        ports:
          - 3306:3306
        options: --health-cmd="mysqladmin ping" --health-interval=10s --health-timeout=5s --health-retries=3
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: '8.1'
        extensions: mbstring, dom, fileinfo, mysql
        coverage: xdebug
    
    - name: Copy .env
      run: php -r "file_exists('.env') || copy('.env.example', '.env');"
    
    - name: Install Composer dependencies
      run: composer install -q --no-ansi --no-interaction --no-scripts --no-progress --prefer-dist
    
    - name: Generate key
      run: php artisan key:generate
    
    - name: Directory permissions
      run: chmod -R 777 storage bootstrap/cache
    
    - name: Run migrations
      run: php artisan migrate
      env:
        DB_HOST: 127.0.0.1
        DB_PORT: 3306
        DB_DATABASE: laravel
        DB_USERNAME: root
        DB_PASSWORD: password
    
    - name: Execute tests via PHPUnit
      run: vendor/bin/phpunit --coverage-clover=coverage.xml
      env:
        DB_HOST: 127.0.0.1
        DB_PORT: 3306
        DB_DATABASE: laravel
        DB_USERNAME: root
        DB_PASSWORD: password
```

### Symfony Workflow

```yaml
name: Symfony

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  symfony-tests:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:13
        env:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: symfony_test
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: '8.1'
        extensions: mbstring, xml, ctype, iconv, intl, pdo, pdo_pgsql, dom, filter, gd, json, pgsql
    
    - name: Validate composer.json and composer.lock
      run: composer validate
    
    - name: Install dependencies
      run: composer install --prefer-dist --no-progress
    
    - name: Run database migrations
      run: |
        php bin/console doctrine:database:create --env=test --if-not-exists
        php bin/console doctrine:migrations:migrate -n --env=test
      env:
        DATABASE_URL: postgresql://postgres:postgres@127.0.0.1:5432/symfony_test?serverVersion=13&charset=utf8
    
    - name: Execute tests (Unit and Feature tests)
      run: vendor/bin/phpunit
      env:
        DATABASE_URL: postgresql://postgres:postgres@127.0.0.1:5432/symfony_test?serverVersion=13&charset=utf8
```

## 🧭 Navigation

- [← Back to Continuous Integration](./06-continuous-integration.md)
- [→ Quality Gates](./06c-quality-gates.md)

## 📚 Further Reading

- [Official GitHub Actions Documentation](https://docs.github.com/en/actions)
- [GitHub Actions Marketplace](https://github.com/marketplace?type=actions)
- [Using PHP with GitHub Actions](https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-php)
- [Setup PHP Action Documentation](https://github.com/shivammathur/setup-php)
- [GitHub Actions Best Practices](https://github.blog/2022-02-02-build-ci-cd-pipeline-github-actions-four-steps/)
