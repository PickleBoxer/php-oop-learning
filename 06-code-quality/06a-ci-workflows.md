# 🛠️ CI/CD Workflows for PHP Projects

## 📚 Overview

Effective CI/CD workflows automate the entire software delivery process, from integration and testing to deployment and monitoring. This guide focuses on designing robust CI/CD workflows specifically for PHP applications.

## 🎯 Common CI/CD Workflow Patterns

### Progressive Pipeline Pattern

A pipeline that gradually increases in complexity and strictness:

```mermaid
graph LR
    A[Fast Checks] --> B[Unit Tests]
    B --> C[Static Analysis]
    C --> D[Integration Tests]
    D --> E[Deployment]
    
    style A fill:#d4f1f9,stroke:#05a3c6,color:#003366
    style B fill:#d5f5e3,stroke:#1e8449,color:#003366
    style C fill:#fcf3cf,stroke:#d4ac0d,color:#003366
    style D fill:#fadbd8,stroke:#cb4335,color:#003366
    style E fill:#e8daef,stroke:#8e44ad,color:#003366
```

**Implementation Example:**
```yaml
stages:
  - verify     # Quick syntax checks, linting
  - test       # Unit tests
  - analyze    # Static analysis, complexity checks
  - integrate  # Integration/E2E tests
  - deploy     # Deployment to target environments
```

### Environment-Based Workflow

Deploys to different environments based on the branch or tag:

```mermaid
graph TD
    A[Code Change] --> B{Which Branch?}
    B -->|feature/*| C[Run Tests]
    B -->|develop| D[Run Tests + Analysis]
    B -->|main| E[Full Test Suite]
    C --> F[Deploy to Dev]
    D --> G[Deploy to Staging]
    E --> H[Deploy to Production]
    
    style A fill:#d4f1f9,stroke:#05a3c6,color:#003366
    style B fill:#d5f5e3,stroke:#1e8449,color:#003366
    style C fill:#fcf3cf,stroke:#d4ac0d,color:#003366
    style D fill:#fadbd8,stroke:#cb4335,color:#003366
    style E fill:#e8daef,stroke:#8e44ad,color:#003366
    style F fill:#f9e79f,stroke:#d68910,color:#003366
    style G fill:#abebc6,stroke:#186a3b,color:#003366
    style H fill:#f5b7b1,stroke:#cb4335,color:#003366
```

**Implementation Example:**
```yaml
on:
  push:
    branches: [feature/*, develop, main]
    
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run tests based on branch
        run: |
          if [[ $GITHUB_REF == refs/heads/main ]]; then
            composer test-full
          elif [[ $GITHUB_REF == refs/heads/develop ]]; then
            composer test && composer analyze
          else
            composer test-basic
          fi
```

### Matrix Testing Workflow

Tests across multiple configurations and dependencies:

```mermaid
graph TD
    A[Start] --> B[PHP 7.4 + MySQL 5.7]
    A --> C[PHP 7.4 + MySQL 8.0]
    A --> D[PHP 8.0 + MySQL 5.7]
    A --> E[PHP 8.0 + MySQL 8.0]
    A --> F[PHP 8.1 + MySQL 5.7]
    A --> G[PHP 8.1 + MySQL 8.0]
    
    B --> H[Report Results]
    C --> H
    D --> H
    E --> H
    F --> H
    G --> H
    
    style A fill:#d4f1f9,stroke:#05a3c6,color:#003366
    style H fill:#d5f5e3,stroke:#1e8449,color:#003366
```

**Implementation Example:**
```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        php-versions: ['7.4', '8.0', '8.1']
        mysql-versions: ['5.7', '8.0']
        
    services:
      mysql:
        image: mysql:${{ matrix.mysql-versions }}
        env:
          MYSQL_ROOT_PASSWORD: password
          MYSQL_DATABASE: test_db
        ports:
          - 3306:3306
    
    steps:
      - uses: actions/checkout@v3
      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: ${{ matrix.php-versions }}
      # Continue with tests...
```

## 🚀 Detailed Workflow Examples

### Laravel Application Workflow

```yaml
name: Laravel CI/CD

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      mysql:
        image: mysql:8.0
        env:
          MYSQL_ROOT_PASSWORD: password
          MYSQL_DATABASE: laravel_test
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
        
    - name: Get composer cache directory
      id: composer-cache
      run: echo "dir=$(composer config cache-files-dir)" >> $GITHUB_OUTPUT
      
    - name: Cache composer dependencies
      uses: actions/cache@v3
      with:
        path: ${{ steps.composer-cache.outputs.dir }}
        key: ${{ runner.os }}-composer-${{ hashFiles('**/composer.lock') }}
        restore-keys: ${{ runner.os }}-composer-
    
    - name: Install dependencies
      run: composer install --prefer-dist --no-progress
    
    - name: Create .env file
      run: cp .env.example .env
      
    - name: Generate application key
      run: php artisan key:generate
      
    - name: Set up database
      run: |
        php artisan migrate
        php artisan db:seed
    
    - name: Run static analysis
      run: vendor/bin/phpstan analyse app tests --level=4
    
    - name: Run coding standards check
      run: vendor/bin/phpcs --standard=PSR12 app
    
    - name: Run tests
      run: vendor/bin/phpunit --coverage-clover coverage.xml
      
    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v3
      with:
        files: coverage.xml

  deploy-staging:
    needs: test
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup SSH
      uses: webfactory/ssh-agent@v0.7.0
      with:
        ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}
    
    - name: Deploy to staging
      run: |
        ssh -o StrictHostKeyChecking=no user@staging.example.com '
          cd /var/www/staging &&
          git pull origin develop &&
          composer install --no-dev --optimize-autoloader &&
          php artisan migrate --force &&
          php artisan config:cache &&
          php artisan route:cache &&
          php artisan view:cache
        '

  deploy-production:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup SSH
      uses: webfactory/ssh-agent@v0.7.0
      with:
        ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}
    
    - name: Deploy to production
      run: |
        ssh -o StrictHostKeyChecking=no user@production.example.com '
          cd /var/www/production &&
          git pull origin main &&
          composer install --no-dev --optimize-autoloader &&
          php artisan migrate --force &&
          php artisan config:cache &&
          php artisan route:cache &&
          php artisan view:cache
        '
```

### Symfony Application Workflow

```yaml
name: Symfony CI/CD

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:13
        env:
          POSTGRES_USER: symfony
          POSTGRES_PASSWORD: symfony
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
        extensions: mbstring, xml, ctype, iconv, intl, pdo_pgsql, dom, filter, gd
        tools: symfony-cli
        coverage: xdebug
        
    - name: Get composer cache directory
      id: composer-cache
      run: echo "dir=$(composer config cache-files-dir)" >> $GITHUB_OUTPUT
      
    - name: Cache composer dependencies
      uses: actions/cache@v3
      with:
        path: ${{ steps.composer-cache.outputs.dir }}
        key: ${{ runner.os }}-composer-${{ hashFiles('**/composer.lock') }}
        restore-keys: ${{ runner.os }}-composer-
    
    - name: Install dependencies
      run: composer install --prefer-dist --no-progress
    
    - name: Create .env.test.local
      run: |
        echo "DATABASE_URL=\"postgresql://symfony:symfony@127.0.0.1:5432/symfony_test?serverVersion=13&charset=utf8\"" > .env.test.local
      
    - name: Set up test database
      run: |
        php bin/console doctrine:database:create --env=test
        php bin/console doctrine:migrations:migrate --no-interaction --env=test
        php bin/console doctrine:fixtures:load --no-interaction --env=test
    
    - name: Run static analysis
      run: vendor/bin/phpstan analyse src tests --level=4
    
    - name: Run coding standards check
      run: vendor/bin/phpcs --standard=PSR12 src
    
    - name: Run tests
      run: vendor/bin/phpunit
      
    - name: Run Symfony security check
      run: symfony check:security

  deploy-staging:
    needs: test
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    
    steps:
    - name: Deploy to staging
      uses: appleboy/ssh-action@master
      with:
        host: staging.example.com
        username: ${{ secrets.SSH_USERNAME }}
        key: ${{ secrets.SSH_PRIVATE_KEY }}
        script: |
          cd /var/www/staging
          git pull origin develop
          composer install --no-dev --optimize-autoloader
          php bin/console doctrine:migrations:migrate --no-interaction
          php bin/console cache:clear
          php bin/console cache:warmup

  deploy-production:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production
    
    steps:
    - name: Deploy to production
      uses: appleboy/ssh-action@master
      with:
        host: production.example.com
        username: ${{ secrets.SSH_USERNAME }}
        key: ${{ secrets.SSH_PRIVATE_KEY }}
        script: |
          cd /var/www/production
          git pull origin main
          composer install --no-dev --optimize-autoloader
          php bin/console doctrine:migrations:migrate --no-interaction
          php bin/console cache:clear --env=prod
          php bin/console cache:warmup --env=prod
```

## 🧠 Advanced Workflow Techniques

### 1. Parallelizing Tests

Split your test suite to run in parallel:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        test-group: ['Unit', 'Feature', 'Integration']
        
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: '8.1'
    
    - name: Install dependencies
      run: composer install --prefer-dist --no-progress
    
    - name: Run tests
      run: vendor/bin/phpunit --testsuite ${{ matrix.test-group }}
```

### 2. Staged Deployments

Deploy gradually across environments with approval gates:

```yaml
jobs:
  deploy-staging:
    # Deploy to staging steps...
  
  approve-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment:
      name: production-approval
    steps:
      - name: Manual approval for production
        run: echo "Approved for production"
  
  deploy-production:
    needs: approve-production
    # Deploy to production steps...
```

### 3. Database Schema Changes

Validate database migrations before applying them:

```yaml
jobs:
  validate-migrations:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: '8.1'
    
    - name: Install dependencies
      run: composer install --prefer-dist --no-progress
      
    - name: Validate migrations
      run: |
        # Create test database
        mysql -h127.0.0.1 -uroot -ppassword -e "CREATE DATABASE migration_test"
        
        # Run migrations
        php bin/console doctrine:migrations:migrate --no-interaction --env=test
        
        # Validate schema
        php bin/console doctrine:schema:validate --env=test
```

### 4. Feature Branch Previews

Deploy feature branches to temporary environments:

```yaml
jobs:
  preview:
    if: startsWith(github.ref, 'refs/heads/feature/')
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    # Generate a unique subdomain based on branch name
    - name: Generate preview URL
      id: preview
      run: echo "url=preview-$(echo $GITHUB_REF | sed 's/refs\/heads\/feature\///g' | sed 's/\//-/g').example.com" >> $GITHUB_OUTPUT
    
    # Deploy to preview environment
    - name: Deploy preview
      uses: appleboy/ssh-action@master
      with:
        host: preview.example.com
        username: ${{ secrets.SSH_USERNAME }}
        key: ${{ secrets.SSH_PRIVATE_KEY }}
        script: |
          export PREVIEW_URL="${{ steps.preview.outputs.url }}"
          /opt/create-preview.sh $PREVIEW_URL $GITHUB_REF
    
    # Comment on PR with preview URL
    - name: Comment on PR
      uses: actions/github-script@v6
      with:
        github-token: ${{ secrets.GITHUB_TOKEN }}
        script: |
          github.rest.issues.createComment({
            issue_number: context.issue.number,
            owner: context.repo.owner,
            repo: context.repo.repo,
            body: `🚀 Preview environment deployed: https://${{ steps.preview.outputs.url }}`
          })
```

### 5. Scheduled Security Scans

Run regular security checks even without code changes:

```yaml
name: Security Scan

on:
  schedule:
    - cron: '0 3 * * 1' # Every Monday at 3AM
  workflow_dispatch: # Allow manual triggering

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: '8.1'
    
    - name: Install dependencies
      run: composer install --prefer-dist --no-progress
      
    - name: Run Symfony security checker
      run: |
        curl -L https://github.com/fabpot/local-php-security-checker/releases/download/v2.0.6/local-php-security-checker_2.0.6_linux_amd64 -o security-checker
        chmod +x security-checker
        ./security-checker
      
    - name: Run PHPCS Security Audit
      run: |
        composer require --dev pheromone/phpcs-security-audit
        vendor/bin/phpcs --extensions=php --standard=vendor/pheromone/phpcs-security-audit/example_base_ruleset.xml src
        
    - name: Send notification on failure
      if: failure()
      uses: rtCamp/action-slack-notify@v2
      env:
        SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
        SLACK_COLOR: danger
        SLACK_MESSAGE: 'Security scan failed :rotating_light:'
        SLACK_TITLE: Security Alert
```

## 🛠️ Workflow Integration with PHP Tools

### PHPUnit

```yaml
- name: Run PHPUnit
  run: vendor/bin/phpunit --testdox --coverage-clover coverage.xml

- name: Upload coverage to Codecov
  uses: codecov/codecov-action@v3
  with:
    files: coverage.xml
```

### PHP_CodeSniffer

```yaml
- name: Run PHP_CodeSniffer
  run: |
    vendor/bin/phpcs --report=checkstyle --report-file=phpcs-results.xml || true
    
- name: Publish code style results
  uses: jwgmeligmeyling/checkstyle-github-action@master
  with:
    path: phpcs-results.xml
```

### PHPStan

```yaml
- name: Run PHPStan
  run: |
    vendor/bin/phpstan analyse src --error-format=github
```

### Rector

```yaml
- name: Run Rector (check only)
  run: |
    composer require --dev rector/rector
    vendor/bin/rector process src --dry-run
```

## 🧭 Navigation

- [← Back to Continuous Integration](./06-continuous-integration.md)
- [→ GitHub Actions](./06b-github-actions.md)

## 📚 Further Reading

- [Continuous Integration & Continuous Delivery](https://martinfowler.com/bliki/ContinuousDeliveryVsContinuousDeployment.html)
- [PHP CI/CD Best Practices](https://php.watch/articles/php-ci-cd-best-practices)
- [Trunk-Based Development](https://trunkbaseddevelopment.com/)
- [The Practical Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html)
- [Feature Toggles (Feature Flags)](https://martinfowler.com/articles/feature-toggles.html)
