# EMR Development Guide

## Project Structure

```
/src/              - Modern PSR-4 code (EMR\ namespace)
/library/          - Legacy procedural PHP code
/interface/        - Web UI controllers and templates
/templates/        - Smarty/Twig templates
/tests/            - Test suite (unit, e2e, api, services)
/sql/              - Database schema and migrations
/public/           - Static assets
/docker/           - Docker configurations
/modules/          - Custom and third-party modules
```

## Technology Stack

- **PHP:** 8.2+ required
- **Backend:** Laminas MVC, Symfony components
- **Templates:** Twig 3.x (modern), Smarty 4.5 (legacy)
- **Frontend:** Angular 1.8, jQuery 3.7, Bootstrap 4.6
- **Build:** Gulp 4, SASS
- **Database:** MySQL via ADODB wrapper
- **Testing:** PHPUnit 11, Jest 29

## Local Development

See `CONTRIBUTING.md` for full setup instructions. Quick start:

```bash
cd docker/development-easy
docker compose up --detach --wait
```

- **App URL:** http://localhost:8300/ or https://localhost:9300/
- **Login:** `admin` / `pass`
- **phpMyAdmin:** http://localhost:8310/

## Testing

Tests run inside Docker via devtools. Run from `docker/development-easy/`:

```bash
# Run all tests
docker compose exec emr /root/devtools clean-sweep-tests

# Individual test suites
docker compose exec emr /root/devtools unit-test
docker compose exec emr /root/devtools api-test
docker compose exec emr /root/devtools e2e-test
docker compose exec emr /root/devtools services-test

# View PHP error log
docker compose exec emr /root/devtools php-log
```

**Tip:** Install emr-cmd for shorter commands (e.g., `emr-cmd ut` for unit tests) from any directory.

## Code Quality

These run on the host (requires local PHP/Node):

```bash
# Run all PHP quality checks (phpcs, phpstan, rector)
composer code-quality

# Individual checks (composer scripts handle memory limits)
composer phpstan          # Static analysis
composer phpcs            # PHP code style check
composer phpcbf           # PHP code style auto-fix
composer rector-check     # Code modernization (dry-run)

# JavaScript/CSS
npm run lint:js           # ESLint check
npm run lint:js-fix       # ESLint auto-fix
npm run stylelint         # CSS/SCSS lint
```

## Build Commands

```bash
npm run build        # Production build
npm run dev          # Development with file watching
npm run gulp-build   # Build only (no watch)
```

## Coding Standards

- **Indentation:** 4 spaces
- **Line endings:** LF (Unix)
- **No strict_types:** Project doesn't use `declare(strict_types=1)`
- **Namespaces:** PSR-4 with `EMR\` prefix for `/src/`
- New code goes in `/src/`, legacy helpers in `/library/`

## Commit Messages

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <description>
```

**Types:** feat, fix, docs, style, refactor, perf, test, build, ci, chore, revert

**Examples:**
- `feat(api): add PATCH support for patient resource`
- `fix(calendar): correct date parsing for recurring events`
- `chore(deps): bump monolog/monolog to 3.10.0`

## Service Layer Pattern

New services should extend `BaseService`:

```php
namespace EMR\Services;

class ExampleService extends BaseService
{
    public const TABLE_NAME = "table_name";

    public function __construct()
    {
        parent::__construct(self::TABLE_NAME);
    }
}
```

## File Headers

When modifying PHP files, ensure proper docblock:

```php
/**
 * Brief description
 *
 * @package   EMR
 * @link      https://your-domain.com
 * @author    Your Name <your@email.com>
 * @copyright Copyright (c) YEAR Your Name or Organization
 * @license   https://github.com/your-org/emr/blob/master/LICENSE GNU General Public License 3
 */
```

Preserve existing authors/copyrights when editing files.

## Common Gotchas

- Multiple template engines: check extension (.twig, .html, .php)
- Event system uses Symfony EventDispatcher
- Pre-commit hooks available via `.pre-commit-config.yaml`

## Key Documentation

- `CONTRIBUTING.md` - Contributing guidelines
- `API_README.md` - REST API docs
- `FHIR_README.md` - FHIR implementation
- `tests/Tests/README.md` - Testing guide
