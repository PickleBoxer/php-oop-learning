# 🐞 Xdebug Setup and Usage

## 📚 Overview

[Xdebug](https://xdebug.org/) is a powerful PHP extension that provides debugging and profiling capabilities. It significantly enhances the development experience by enabling step debugging, stack traces, function traces, code coverage analysis, and profiling.

## 🔧 Installation

### Step 1: Check PHP Version and Configuration

```bash
# Get PHP version
php -v

# Get PHP configuration information
php -i
```

### Step 2: Install Xdebug

#### For Linux/Ubuntu:

```bash
# Install Xdebug
sudo apt-get install php-xdebug

# For a specific PHP version
sudo apt-get install php8.1-xdebug
```

#### For Windows:

1. Download the appropriate Xdebug DLL from [xdebug.org/download](https://xdebug.org/download)
2. Place the DLL in your PHP extension directory
3. Update php.ini

#### For macOS:

```bash
# With Homebrew
brew install php-xdebug

# OR with PECL
pecl install xdebug
```

#### Using the Xdebug Wizard:

1. Run `php -i` and copy the output
2. Paste it into the [Xdebug Installation Wizard](https://xdebug.org/wizard)
3. Follow the provided instructions

### Step 3: Configure php.ini

Add these settings to your php.ini file:

```ini
[xdebug]
zend_extension=xdebug
xdebug.mode=develop,debug
xdebug.start_with_request=yes
xdebug.client_host=127.0.0.1
xdebug.client_port=9003
xdebug.log=/path/to/xdebug.log
```

### Step 4: Verify Installation

Create a PHP file with `<?php phpinfo(); ?>` and check if Xdebug is listed in the modules.

```bash
# Test if Xdebug is loaded
php -v
# Should show Xdebug in the output
```

## 🔄 Xdebug Modes

Xdebug operates in different modes depending on your needs:

| Mode | Description | php.ini Configuration |
|------|-------------|----------------------|
| **develop** | Improves error messages, var_dump output | `xdebug.mode=develop` |
| **debug** | Enables step debugging | `xdebug.mode=debug` |
| **trace** | Enables function tracing | `xdebug.mode=trace` |
| **profile** | Enables profiling | `xdebug.mode=profile` |
| **coverage** | Enables code coverage analysis | `xdebug.mode=coverage` |

You can enable multiple modes simultaneously:

```ini
xdebug.mode=develop,debug,coverage
```

## 🎯 Step Debugging with Xdebug

Step debugging allows you to pause execution and inspect variables at specific points in your code.

### IDE Configuration

#### PhpStorm Setup

1. Go to **Settings** → **PHP** → **Debug**
2. Ensure the Debug port is set to 9003
3. Create a **PHP Web Page** run configuration
4. Set a breakpoint in your code
5. Click the "Start Listening for PHP Debug Connections" button
6. Load your page with the `XDEBUG_SESSION` cookie or parameter

#### VS Code Setup

1. Install the [PHP Debug](https://marketplace.visualstudio.com/items?itemName=felixfbecker.php-debug) extension
2. Create a `.vscode/launch.json` file:

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for Xdebug",
            "type": "php",
            "request": "launch",
            "port": 9003,
            "pathMappings": {
                "/var/www/html": "${workspaceFolder}"
            }
        }
    ]
}
```

3. Start debugging by clicking "Run and Debug" in VS Code
4. Set breakpoints and load your page

### Debugging Web Applications

#### Trigger Debugging Session

You can trigger debugging in several ways:

1. **URL Parameter**: Add `?XDEBUG_SESSION=1` to your URL
2. **Cookie**: Set the `XDEBUG_SESSION` cookie
3. **Browser Extension**: Use plugins like "Xdebug Helper" for Chrome/Firefox

#### Browser Extensions

- [Xdebug Helper for Chrome](https://chrome.google.com/webstore/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc)
- [Xdebug Helper for Firefox](https://addons.mozilla.org/en-US/firefox/addon/xdebug-helper-for-firefox/)

### Debugging CLI Scripts

For CLI scripts, configure Xdebug to start automatically:

```ini
xdebug.start_with_request=yes
```

Then run your script with your IDE listening for connections:

```bash
php script.php
```

## 📊 Function Tracing

Function tracing records all function calls, their arguments, and return values.

### Configuration (php.ini)

```ini
xdebug.mode=trace
xdebug.trace_output_dir=/path/to/traces
xdebug.trace_format=0
xdebug.trace_options=0
xdebug.collect_return=1
xdebug.collect_params=4
```

### Starting a Trace

```php
// Start trace manually in code
xdebug_start_trace('/path/to/trace_file');

// Your code here
function test($a, $b) {
    return $a + $b;
}
test(5, 10);

// Stop trace
xdebug_stop_trace();
```

### Reading Trace Files

Trace files show function calls, parameters, and return values in a structured format:

```
TRACE START [timestamp]
    0 1.2345     -> test($a = 5, $b = 10) /path/to/file.php:10
    0 1.2346     <- test = 15 /path/to/file.php:10
TRACE END   [timestamp]
```

## 📝 Code Coverage

Xdebug can generate code coverage reports to measure how much of your code is executed by tests.

### Configuration

```ini
xdebug.mode=coverage
```

### With PHPUnit

```bash
XDEBUG_MODE=coverage vendor/bin/phpunit --coverage-html coverage
```

### Manual Code Coverage

```php
// Start code coverage
xdebug_start_code_coverage(XDEBUG_CC_UNUSED | XDEBUG_CC_DEAD_CODE);

// Code to analyze
require 'my_script.php';

// Get coverage data
$coverage = xdebug_get_code_coverage();

// Stop code coverage
xdebug_stop_code_coverage();

// Display or save results
print_r($coverage);
```

## 🔍 Profiling with Xdebug

Profiling helps identify performance bottlenecks in your code.

### Configuration

```ini
xdebug.mode=profile
xdebug.output_dir=/path/to/profiles
xdebug.profiler_output_name=cachegrind.out.%p
```

### Trigger Profiling

1. **URL Parameter**: Add `?XDEBUG_PROFILE=1` to your URL
2. **Always On**: Set `xdebug.start_with_request=yes` with mode=profile

### Analyzing Profiles

Profile files are in Cachegrind format. Use tools to analyze them:

1. **KCachegrind** (Linux): Visualization tool for profiles
   ```bash
   sudo apt-get install kcachegrind
   kcachegrind /path/to/cachegrind.out.12345
   ```

2. **QCachegrind** (macOS/Windows): Cross-platform alternative
   ```bash
   brew install qcachegrind  # On macOS
   ```

3. **Webgrind**: Browser-based profile viewer
   ```bash
   git clone https://github.com/jokkedk/webgrind.git
   # Access via web browser
   ```

## 🔄 Xdebug's Pretty Printer

Xdebug enhances `var_dump()` with better formatting:

```php
// Without Xdebug
var_dump($complexObject);
// Output: hard-to-read, single-line dump

// With Xdebug
var_dump($complexObject);
// Output: formatted, colorized, with proper indentation and limits
```

## 🚨 Common Issues and Solutions

| Issue | Solution |
|-------|----------|
| Xdebug not connecting | Check firewall settings, confirm correct port and host |
| Performance impact | Disable Xdebug in production, enable only when needed |
| Large trace files | Use `xdebug.trace_options=1` to append rather than overwrite |
| IDE path mapping | Ensure local paths correctly map to server paths |
| Works in CLI but not web | Check web server user permissions for log/output directories |

## 🔄 Xdebug 3 vs Xdebug 2

If upgrading from Xdebug 2, note these key differences:

| Xdebug 2 | Xdebug 3 |
|----------|----------|
| `xdebug.remote_enable=1` | `xdebug.mode=debug` |
| `xdebug.remote_host` | `xdebug.client_host` |
| `xdebug.remote_port` | `xdebug.client_port` |
| `xdebug.remote_autostart=1` | `xdebug.start_with_request=yes` |

## 💡 Expert Tips

1. **Selective Debugging**: Use browser bookmarklets to enable debugging only when needed
2. **Environment-Specific Config**: Use different php.ini files for development and production
3. **Filter Output**: Set `xdebug.show_local_vars=0` in development for cleaner error messages
4. **Remote Debugging**: For Docker or VMs, set `xdebug.client_host=host.docker.internal`
5. **Log Filters**: Use `xdebug.filter_*` settings to focus on specific code paths

## 🧭 Navigation

- [← Back to Debugging Strategies](./05a-debugging-strategies.md)
- [→ Profiling Tools and Techniques](./05c-profiling.md)

## 📚 Further Reading

- [Official Xdebug Documentation](https://xdebug.org/docs/)
- [Xdebug 3 Configuration Guide](https://xdebug.org/docs/all_settings)
- [PhpStorm Xdebug Setup Guide](https://www.jetbrains.com/help/phpstorm/configuring-xdebug.html)
- [VS Code PHP Debugging Guide](https://code.visualstudio.com/docs/languages/php#_debugging)
- [Advanced Xdebug Techniques](https://stitcher.io/blog/advanced-xdebug-usage)
