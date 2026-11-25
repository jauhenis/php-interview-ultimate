---
title: 'Composer and PHP Tooling'
slug: '/answers/composer_tools'
---

# Composer and PHP Tooling

[Composer documentation](https://getcomposer.org/doc/)

This guide covers Composer 2 and the tools a PHP project commonly installs with it.

## `composer install` vs `composer update`

- **`composer install`** reads `composer.lock` and installs exactly the versions listed there. If there is no lock file, it behaves like `update` and creates one.
- **`composer update`** ignores the lock file, resolves the newest versions allowed by the constraints in `composer.json` and writes a new `composer.lock`. `composer update vendor/package` updates only the named package.

Use `install` in CI and production, and `update` only when you intend to change dependency versions.

## Version constraints

Composer follows Semantic Versioning: `MAJOR.MINOR.PATCH`. A major release may break backward compatibility, a minor release adds features and a patch release fixes bugs.

| Constraint | Allowed versions                                                               |
| ---------- | ------------------------------------------------------------------------------ |
| `1.2.3`    | exactly `1.2.3`                                                                |
| `^1.2.3`   | `>=1.2.3 <2.0.0`                                                               |
| `^0.3`     | `>=0.3.0 <0.4.0`, because in `0.x` every minor release may break compatibility |
| `~1.2`     | `>=1.2 <2.0`                                                                   |
| `~1.2.3`   | `>=1.2.3 <1.3.0`                                                               |
| `1.*`      | `>=1.0 <2.0`                                                                   |

`^1.0 || ^2.0` accepts either range. `^` is the recommended default for most dependencies.

## `require` vs `require-dev`

- **`require`:** packages the application needs at runtime, such as a framework, an HTTP client or an ORM.
- **`require-dev`:** packages for development and CI, such as PHPUnit, PHPStan or PHP-CS-Fixer. Add one with `composer require --dev vendor/package`.

`composer install --no-dev` skips `require-dev` and `autoload-dev`, which makes `vendor/` smaller and reduces the attack surface in production. Composer installs only the root project's dev dependencies, never those of the installed packages.

## The `vendor` directory

Composer installs packages into `vendor/` and generates `vendor/autoload.php`. Add `vendor/` to `.gitignore`: `composer.json` and `composer.lock` fully describe it. Do not edit files in `vendor/`, because the next install overwrites them. To fix a bug in a dependency, send the fix upstream, use a fork, or apply a patch with `cweagans/composer-patches`.

## Autoloading

```json
{
  "autoload": {
    "psr-4": { "App\\": "src/" },
    "classmap": ["legacy/"],
    "files": ["src/helpers.php"]
  },
  "autoload-dev": {
    "psr-4": { "Tests\\": "tests/" }
  }
}
```

- **`psr-4`:** maps a namespace prefix to a directory. `App\Service\Mailer` is loaded from `src/Service/Mailer.php`.
- **`classmap`:** Composer scans the directories and builds a class-to-file map. Useful for legacy code without namespaces.
- **`files`:** included on every request. PHP cannot autoload functions, so global helper functions go here. Keep the list short.
- **`psr-0`:** the deprecated predecessor of PSR-4.

After changing these rules, run `composer dump-autoload`.

## Optimizing the autoloader

- **`--optimize-autoloader` (`-o`):** converts PSR-4 and PSR-0 rules into a class map. Classes missing from the map are still searched by the PSR-4 rules.
- **`--classmap-authoritative` (`-a`):** implies `-o` and never searches the disk for classes missing from the map. It is the fastest option, but classes generated at runtime are not found.
- **`--apcu-autoloader`:** caches class lookups in APCu. Requires the APCu extension.

A typical production build:

```bash
composer install --no-dev --optimize-autoloader --classmap-authoritative --no-interaction
```

## Committing `composer.lock`

- **Application:** always commit it. Developers, CI and production then install the same versions.
- **Library:** its lock file is ignored when the library is installed as a dependency. Committing it keeps the team's development environment consistent, but can hide incompatibilities with newer dependency versions. Test libraries in CI with both `composer update --prefer-lowest` and `composer update`.

Do not resolve conflicts in `composer.lock` by hand. Take one side, then run `composer update vendor/package` for the packages that changed on the other side.

## Scripts and plugins

```json
{
  "scripts": {
    "test": "phpunit",
    "analyse": "phpstan analyse"
  },
  "config": {
    "allow-plugins": {
      "symfony/flex": true
    }
  }
}
```

- **Scripts** are commands in the `scripts` section. Run them by name (`composer test`) or attach them to events such as `post-install-cmd`, `post-update-cmd` and `post-autoload-dump`. `--no-scripts` disables them.
- **Plugins** are packages of type `composer-plugin` that extend Composer, for example `symfony/flex` or `phpstan/extension-installer`. A plugin runs code during `install`, so since Composer 2.2 each one must be allowed in `config.allow-plugins`.

## Platform requirements

Packages can require a PHP version and extensions, for example `"php": "^8.2"` or `"ext-intl": "*"`. Composer checks them against the PHP that runs Composer, and generates `vendor/composer/platform_check.php`, which fails at runtime when the server's PHP is too old.

If developers run PHP 8.4 but production runs PHP 8.2, `composer update` can select versions that need PHP 8.3. Declare the production platform:

```json
{
  "config": {
    "platform": {
      "php": "8.2.0"
    }
  }
}
```

`composer check-platform-reqs` verifies a server before deployment. Avoid `--ignore-platform-reqs`, which hides real incompatibilities.

## Resolving dependency conflicts

1. Read the error: Composer prints the chain of conflicting constraints.
2. `composer why vendor/package` shows which packages require a package. `composer why-not vendor/package 2.0` shows what blocks a version.
3. Update related packages together: `-w` (`--with-dependencies`) also updates their dependencies, and `-W` (`--with-all-dependencies`) also updates dependencies listed in your root `require`.
4. Relax your constraints, or upgrade or replace the package that blocks the update.
5. As a temporary measure, use a fork in `repositories` or an inline alias such as `"vendor/package": "dev-fix as 1.4.0"`.

## `provide`, `replace` and `conflict`

- **`provide`:** the package implements a virtual package. `monolog/monolog` provides `psr/log-implementation`, so a library can require that virtual package and let the application choose a logger.
- **`replace`:** the package contains another package's code, so Composer does not install the other package. Projects on a recent PHP version sometimes replace polyfills they do not need: `"replace": {"symfony/polyfill-php80": "*"}`.
- **`conflict`:** versions that cannot be installed together with the package, for example `"conflict": {"doctrine/orm": "<2.15"}`.

## Dependency security

- `composer audit` checks installed packages against security advisories. Composer also runs it after `require` and `update`. Add it to CI.
- `roave/security-advisories` conflicts with every known vulnerable version, so Composer refuses to install them.
- Commit `composer.lock` and use `composer install` in CI and production.
- Allow only plugins you trust in `config.allow-plugins`.
- Dependabot or Renovate open pull requests for new versions, so tests check every update.
- Every package and its dependencies add code you did not write. Check a package's maintenance status before adding it.

## Private packages and monorepos

```json
{
  "repositories": [
    { "type": "vcs", "url": "git@github.com:acme/billing-sdk.git" },
    { "type": "path", "url": "packages/*", "options": { "symlink": true } }
  ],
  "require": {
    "acme/billing-sdk": "^2.1",
    "acme/shared-kernel": "*@dev"
  }
}
```

- **`vcs`:** a Git repository. Composer reads its tags and branches as versions. It becomes slow with many packages.
- **`composer`:** a package registry such as Satis (self-hosted), Private Packagist, or the GitLab and GitHub package registries.
- **`path`:** a local directory, symlinked into `vendor/`. Suits monorepos and developing a package together with an application.

Store credentials in `auth.json`, added to `.gitignore`, or in the `COMPOSER_AUTH` environment variable in CI.

## PHP-CS-Fixer

[PHP-CS-Fixer](https://github.com/PHP-CS-Fixer/PHP-CS-Fixer) fixes code style according to rule sets such as `@PER-CS` or `@Symfony`: formatting, spacing, import order and similar rules. Run `php-cs-fixer fix --dry-run --diff` in CI to report violations, and `php-cs-fixer fix` locally or in a pre-commit hook. Laravel Pint is a preconfigured wrapper around PHP-CS-Fixer. PHP_CodeSniffer is an alternative: `phpcs` reports violations and `phpcbf` fixes them.

## Rector

[Rector](https://getrector.com/) changes code automatically according to the rules you enable. Typical uses are moving to newer PHP syntax, such as constructor property promotion or `match`, upgrading Symfony, Laravel or PHPUnit, adding type declarations and removing dead code. `rector process --dry-run` shows the changes without applying them. Review the result and run the tests, because Rector rewrites code at scale.

## Profiling

Measure before optimizing:

- **APM** (New Relic, Datadog, tools based on OpenTelemetry) records every request and shows which endpoints, database queries and external calls are slow.
- **Profilers** show where a single request spends its time. Blackfire is commercial and can safely profile selected requests in production. XHProf and SPX are open-source alternatives. Tideways combines an APM with a profiler.

Common findings in PHP applications are N+1 database queries, missing indexes, missing caches and an unoptimized autoloader.
